---
title: Konfigurera din egen nyckel för att kryptera Azure Service Bus data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för att kryptera Azure Service Bus data rest.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3e8f3a599ee5fe40c85a93dd58d36e6cd611c9ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631774"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurera Kundhanterade nycklar för att kryptera Azure Service Bus data i vila med hjälp av Azure Portal
Azure Service Bus Premium tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Service Bus Premium är beroende av Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med hjälp av Microsoft-hanterade nycklar. 

## <a name="overview"></a>Översikt
Azure Service Bus stöder nu möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar (Bring Your Own Key-BYOK). med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till de Kundhanterade nycklar som används för att kryptera Azure Service Bus i vila.

Att aktivera funktionen BYOK är en tids inställnings process i namn området.

> [!NOTE]
> Det finns vissa varningar om kundens hanterade nyckel för kryptering på tjänst sidan. 
>   * Den här funktionen stöds av [Azure Service Bus Premium](service-bus-premium-messaging.md) -nivån. Det går inte att aktivera den för standard nivå Service Bus namn områden.
>   * Kryptering kan bara aktive ras för nya eller tomma namn områden. Om namn området innehåller några köer eller ämnen kommer krypterings åtgärden att Miss sen.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckel användning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du konfigurerar ett nyckel valv med Kundhanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: skapa ett Azure Key Vault med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Att använda Kundhanterade nycklar med Azure Service Bus kräver att nyckel valvet har två obligatoriska egenskaper konfigurerade. De är:  **mjuk borttagning** och **Rensa inte**. De här egenskaperna är aktiverade som standard när du skapar ett nytt nyckel valv i Azure Portal. Men om du behöver aktivera dessa egenskaper i ett befintligt nyckel valv måste du använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar
Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Gå till ditt Service Bus Premium-namnområde.
2. På sidan **Inställningar** i Service Bus namn området väljer du **kryptering**.
3. Välj den **Kundhanterade nyckel krypteringen i vila** , som visas i följande bild.

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckel valv med nycklar

När du har aktiverat Kundhanterade nycklar måste du associera kundens hanterade nyckel med din Azure Service Bus-namnrymd. Service Bus stöder endast Azure Key Vault. Om du aktiverar alternativet för **kryptering med kundhanterad nyckel** i föregående avsnitt måste nyckeln importeras till Azure Key Vault. Nycklarna måste också ha **mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med hjälp av [PowerShell](../key-vault/general/key-vault-recovery.md) eller [CLI](../key-vault/general/key-vault-recovery.md).

1. Om du vill skapa ett nytt nyckel valv följer du [snabb](../key-vault/general/overview.md)starten för Azure Key Vault. Mer information om hur du importerar befintliga nycklar finns i [om nycklar, hemligheter och certifikat](../key-vault/general/about-keys-secrets-certificates.md).
1. Om du vill aktivera både mjuk borttagning och tömning av skydd när du skapar ett valv använder du kommandot AZ-kommandot för att [skapa](/cli/azure/keyvault#az-keyvault-create) ett valv.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensnings skydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot AZ-kommando för att [Uppdatera](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.
        
        ![Välj knappen generera/importera](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 

    1. Nu kan du välja den här nyckeln för att associera med Service Bus namn området för kryptering i list rutan. 

        ![Välj nyckel från Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Du kan lägga till upp till tre nycklar för redundans. Om en av nycklarna har upphört att gälla eller inte går att komma åt, används de andra nycklarna för kryptering.
        
    1. Fyll i informationen om nyckeln och klicka på **Välj**. Detta möjliggör kryptering av data i vila i namn området med en kundhanterad nyckel. 


    > [!IMPORTANT]
    > Om du vill använda kundhanterad nyckel tillsammans med geo haveri beredskap, granskar du nedan – 
    >
    > Om du vill aktivera kryptering i vila med kundhanterad nyckel, konfigureras en [åtkomst princip](../key-vault/general/secure-your-key-vault.md) för den Service Bus hanterade identiteten på det angivna Azure-valvet. Detta säkerställer kontrollerad åtkomst till Azure-valvet från Azure Service Bus namn området.
    >
    > På grund av detta:
    > 
    >   * Om [geo haveri beredskap](service-bus-geo-dr.md) redan har Aktiver ats för Service Bus-namnrymden och du vill aktivera kundhanterad nyckel, så 
    >     * Bryt ihopparningen
    >     * [Konfigurera åtkomst principen](../key-vault/general/assign-access-policy-portal.md) för den hanterade identiteten för både den primära och sekundära namn rymden till nyckel valvet.
    >     * Konfigurera kryptering i det primära namn området.
    >     * Para om de primära och sekundära namn områdena.
    > 
    >   * Om du vill aktivera geo-DR på ett Service Bus namn område där kundhanterad nyckel redan har kon figurer ATS, så-
    >     * [Konfigurera åtkomst principen](../key-vault/general/assign-access-policy-portal.md) för den hanterade identiteten för det sekundära namn området till nyckel valvet.
    >     * Para ihop de primära och sekundära namn områdena.


## <a name="rotate-your-encryption-keys"></a>Rotera dina krypterings nycklar

Du kan rotera din nyckel i nyckel valvet med hjälp av rotations funktionen för Azure Key Vault. Aktiverings-och utgångs datum kan också ställas in för att automatisera nyckel rotationen. Tjänsten Service Bus identifierar nya nyckel versioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar

Om du återkallar åtkomst till krypterings nycklarna rensas inte data från Service Bus. Men det går inte att komma åt data från namn området Service Bus. Du kan återkalla krypterings nyckeln via åtkomst principen eller genom att ta bort nyckeln. Läs mer om åtkomst principer och skydda nyckel valvet från [säker åtkomst till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).

När krypterings nyckeln har återkallats går Service Buss tjänsten på det krypterade namn området inte att fungera. Om åtkomsten till nyckeln är aktive rad eller om den borttagna nyckeln återställs, kommer Service Bus-tjänsten att välja nyckeln så att du kan komma åt data från namn området krypterad Service Bus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använd Resource Manager-mall för att aktivera kryptering
I det här avsnittet visas hur du utför följande uppgifter med hjälp av **Azure Resource Manager mallar**. 

1. Skapa ett **premium** Service Bus-namnområde med en **hanterad tjänst identitet**.
2. Skapa ett **nyckel valv** och ge tjänst identitets åtkomst till nyckel valvet. 
3. Uppdatera Service Bus-namnrymden med nyckel valvs informationen (nyckel/värde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Skapa ett Premium Service Bus-namnområde med hanterad tjänst identitet
I det här avsnittet visas hur du skapar ett Azure Service Bus-namnområde med hanterad tjänst identitet med hjälp av en Azure Resource Manager mall och PowerShell. 

1. Skapa en Azure Resource Manager-mall för att skapa ett namn område för en Service Bus Premium-nivå med en hanterad tjänst identitet. Ge filen namnet: **CreateServiceBusPremiumNamespace.jspå**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Skapa en mall med namnet: **CreateServiceBusPremiumNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>` – Namnet på din Service Bus namn område
    > - `<Location>` – Plats för Service Bus namn området

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa ett Premium Service Bus-namnområde. Hämta sedan ID: t för Service Bus namn området om du vill använda det senare. Ersätt `{MyRG}` med namnet på resurs gruppen innan du kör kommandot.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Bevilja Service Bus namn rymds identitets åtkomst till Key Vault

1. Kör följande kommando för att skapa ett nyckel valv med **rensnings skydd** och **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    ELLER
    
    Kör följande kommando för att uppdatera ett **befintligt nyckel valv**. Ange värden för resurs gruppen och nyckel valvs namnen innan du kör kommandot. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange åtkomst principen för nyckel valv så att den hanterade identiteten för Service Bus namn området kan komma åt nyckel värden i nyckel valvet. Använd ID: t för Service Bus namn området i föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i Service Bus namnrymd med kundhanterad nyckel från Key Vault
Du har utfört följande steg: 

1. Skapade ett Premium-namnområde med en hanterad identitet.
2. Skapa ett nyckel valv och bevilja åtkomst till hanterad identitet till nyckel valvet. 

I det här steget ska du uppdatera Service Bus-namnrymden med Key Vault-information. 

1. Skapa en JSON-fil med namnet **UpdateServiceBusNamespaceWithEncryption.jspå** med följande innehåll: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Skapa en mall för mallparameter: **UpdateServiceBusNamespaceWithEncryptionParams.jspå**.

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>` – Namnet på din Service Bus namn område
    > - `<Location>` – Plats för Service Bus namn området
    > - `<KeyVaultName>` – Namn på ditt nyckel valv
    > - `<KeyName>` – Namnet på nyckeln i nyckel valvet  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Kör följande PowerShell-kommando för att distribuera Resource Manager-mallen. Ersätt `{MyRG}` med namnet på din resurs grupp innan du kör kommandot. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Service Bus](service-bus-messaging-overview.md)
- [Översikt över Key Vault](../key-vault/general/overview.md)