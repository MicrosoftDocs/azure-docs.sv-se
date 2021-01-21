---
title: Konfigurera Kundhanterade nycklar för att kryptera data i vila i ISEs
description: Skapa och hantera dina egna krypterings nycklar för att skydda data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629682"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps

Azure Logic Apps förlitar sig på Azure Storage för att lagra och automatiskt [kryptera data i vila](../storage/common/storage-service-encryption.md). Den här krypteringen skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Som standard använder Azure Storage Microsoft-hanterade nycklar för att kryptera dina data. Mer information om hur Azure Storage kryptering fungerar finns i [Azure Storage kryptering för data i vila](../storage/common/storage-service-encryption.md) och [Azure Data Encryption – i vila](../security/fundamentals/encryption-atrest.md).

När du skapar en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) som är värd för dina Logi Kap par, och du vill ha mer kontroll över de krypterings nycklar som används av Azure Storage, kan du konfigurera, använda och hantera din egen nyckel med hjälp av [Azure Key Vault](../key-vault/general/overview.md). Den här funktionen kallas "Bring Your Own Key" (BYOK) och din nyckel kallas för "kundhanterad nyckel". Med den här funktionen aktiverar Azure Storage automatiskt [Double kryptering eller *infrastruktur kryptering* med hjälp av plattforms hanterade nycklar](../security/fundamentals/double-encryption.md) för din nyckel. Mer information finns i [dubblera kryptering av data med infrastruktur kryptering](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption).

Det här avsnittet visar hur du konfigurerar och anger din egen krypterings nyckel som ska användas när du skapar din ISE med hjälp av Logic Apps REST API. De allmänna stegen för att skapa en ISE genom Logic Apps REST API finns i [skapa en integrerings tjänst miljö (ISE) med hjälp av Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Överväganden

* För närvarande är kundhanterad nyckel support för en ISE endast tillgänglig i dessa Azure-regioner: USA, västra 2, östra USA och södra centrala USA

* Du kan bara ange en kundhanterad nyckel *när du skapar din ISE*, inte senare. Du kan inte inaktivera den här nyckeln när du har skapat din ISE. För närvarande finns det inget stöd för att rotera en kundhanterad nyckel för en ISE.

* För att stödja Kundhanterade nycklar kräver din ISE att du aktiverar antingen [systemtilldelad eller användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Med den här identiteten kan du autentisera åtkomst till skyddade resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i eller är anslutna till ett virtuellt Azure-nätverk. På så sätt behöver du inte logga in med dina autentiseringsuppgifter.

* För närvarande måste du anropa Logic Apps REST API genom att använda en HTTPS-begäran för att skapa en ISE som stöder Kundhanterade nycklar och har antingen hanterad identitets typ aktive rad.

* Du måste [ge nyckel valvet åtkomst till din ISES hanterade identitet](#identity-access-to-key-vault), men tiden är beroende av vilken hanterad identitet du använder.

  * **Systemtilldelad hanterad identitet**: inom *30 minuter efter* att du skickat https-begäran som skapar din ISE måste du [ge nyckel valv åtkomst till din ISES hanterade identitet](#identity-access-to-key-vault). Annars Miss lyckas ISE-skapande och du får ett behörighets fel.

  * **Användardefinierad hanterad identitet**: innan du skickar en https-begäran som skapar din ISE ger du [nyckel valv åtkomst till din ISES hanterade identitet](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Förutsättningar

* Samma krav [](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) och [krav för att ge åtkomst till din ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) som när du skapar en ISE i Azure Portal

* Ett Azure Key Vault som har den **mjuka borttagningen** och **inte rensa** egenskaper aktiverat

  Mer information om hur du aktiverar dessa egenskaper finns i [Azure Key Vault översikt över mjuk borttagning](../key-vault/general/soft-delete-overview.md) och [Konfigurera Kundhanterade nycklar med Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Om du inte har använt [Azure Key Vault](../key-vault/general/overview.md)kan du lära dig hur du skapar ett nyckel valv med hjälp av [Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md)eller [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

* I ditt nyckel valv är en nyckel som skapas med följande egenskaps värden:

  | Egenskap | Värde |
  |----------|-------|
  | **Nyckel typ** | RSA |
  | **Nyckel storlek för RSA** | 2048 |
  | **Aktiverad** | Yes |
  |||

  ![Skapa din kund hanterade krypterings nyckel](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Mer information finns i [Konfigurera Kundhanterade nycklar med Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) eller Azure PowerShell kommandot [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Ett verktyg som du kan använda för att skapa din ISE genom att anropa Logic Apps REST API med en begäran om HTTPS-begäran. Du kan till exempel använda [Postman](https://www.getpostman.com/downloads/), eller så kan du bygga en logisk app som utför den här uppgiften.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Skapa ISE med Key Vault och stöd för hanterad identitet

Om du vill skapa din ISE genom att anropa Logic Apps REST API, gör du denna HTTPS-begäran:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01-versionen kräver att du gör en egen HTTPS-begäran för ISE-anslutningar.

Distributionen tar vanligt vis inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrol lera distributions statusen i [Azure Portal](https://portal.azure.com)går du till Azure-verktygsfältet och väljer aviserings ikonen, som öppnar fönstret meddelanden.

> [!NOTE]
> Om distributionen Miss lyckas eller om du tar bort din ISE kan Azure ta upp till en timme innan du släpper upp dina undernät. Den här fördröjningen innebär att du kan behöva vänta innan du återanvänder dessa undernät i en annan ISE.
>
> Om du tar bort det virtuella nätverket tar Azure vanligt vis upp till två timmar innan du frigör dina undernät, men den här åtgärden kan ta längre tid. 
> Se till att inga resurser fortfarande är anslutna när du tar bort virtuella nätverk. 
> Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Begärandehuvud

I begär ande huvudet inkluderar du följande egenskaper:

* `Content-type`: Ange det här egenskap svärdet till `application/json` .

* `Authorization`: Ange det här egenskap svärdet till Bearer-token för den kund som har åtkomst till den Azure-prenumeration eller resurs grupp som du vill använda.

### <a name="request-body"></a>Begärandetext

I begär ande texten aktiverar du stöd för dessa ytterligare objekt genom att ange informationen i din ISE-definition:

* Den hanterade identitet som din ISE använder för att få åtkomst till ditt nyckel valv
* Ditt nyckel valv och den Kundhanterade nyckel som du vill använda

#### <a name="request-body-syntax"></a>Syntax för begär ande text

Här är syntaxen för begär ande texten, som beskriver de egenskaper som ska användas när du skapar din ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Exempel på begär ande text

Den här exempel texten visar exempel värden:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Även om tids inställningen skiljer sig utifrån den hanterade identitet som du använder, måste du [ge nyckel valvet åtkomst till din ISES hanterade identitet](#identity-access-to-key-vault).

* **Systemtilldelad hanterad identitet**: inom *30 minuter efter* att du har skickat https-begäran som skapar din ISE måste du lägga till en åtkomst princip i nyckel VALVEt för din ISE: s systemtilldelade identitet. Annars Miss lyckas skapande av ISE och du får ett behörighets fel.

* **Användardefinierad hanterad identitet**: innan du skickar en https-begäran som skapar din ISE lägger du till en åtkomst princip i nyckel valvet för din ISE användares tilldelade hanterade identitet.

För den här uppgiften kan du använda antingen kommandot Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , eller så kan du följa de här stegen i Azure Portal:

1. Öppna Azure Key Vault i [Azure Portal](https://portal.azure.com).

1. På menyn Key Vault väljer du **åtkomst principer**  >  **Lägg till åtkomst princip**, till exempel:

   ![Lägg till åtkomst princip för systemtilldelad hanterad identitet](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. När fönstret **Lägg till åtkomst princip** har öppnats följer du dessa steg:

   1. Välj följande alternativ:

      | Inställning | Värden |
      |---------|--------|
      | **Konfigurera från mall (valfritt) lista** | Nyckel hantering |
      | **Nyckel behörigheter** | - **Nyckel hanterings åtgärder**: Hämta, lista <p><p>- **Kryptografiska åtgärder**: unwrap Key, wrap Key |
      |||

      ![Välj nyckel hantering > nyckel behörigheter](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. För **Välj huvud konto** väljer du **ingen vald**. När **huvud** fönstret öppnas går du till sökrutan och söker efter och väljer din ISE. När du är klar väljer du **Välj**  >  **Lägg till**.

      ![Välj den ISE som ska användas som huvud konto](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. När du är klar med fönstret **åtkomst principer** väljer du **Spara**.

Mer information finns i [så här autentiserar du till Key Vault](../key-vault/general/authentication.md) och [tilldelar en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Key Vault](../key-vault/general/overview.md)
