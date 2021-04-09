---
title: Kryptera registret med en kundhanterad nyckel
description: Lär dig mer om kryptering – resten av ditt Azure Container Registry och hur du krypterar ditt Premium-register med en kundhanterad nyckel som lagras i Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: aad9419fdb139ff615bfe07075be78a2ca4ee4ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489080"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Kryptera registret med en kundhanterad nyckel

När du lagrar bilder och andra artefakter i ett Azure Container Registry krypterar Azure automatiskt register innehållet i vila med [tjänst hanterade nycklar](../security/fundamentals/encryption-models.md). Du kan komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av en nyckel som du skapar och hanterar i Azure Key Vault (en kundhanterad nyckel). Den här artikeln vägleder dig genom stegen med Azure CLI, Azure Portal eller en Resource Manager-mall.

Kryptering på Server sidan med Kundhanterade nycklar stöds genom integrering med [Azure Key Vault](../key-vault/general/overview.md): 

* Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv eller använda Azure Key Vault s API: er för att generera nycklar. 
* Med Azure Key Vault kan du också granska nyckel användningen.
* Azure Container Registry stöder automatisk rotation av register krypterings nycklar när en ny nyckel version är tillgänglig i Azure Key Vault. Du kan också rotera register krypterings nycklar manuellt.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).


## <a name="things-to-know"></a>Saker att känna till

* Du kan för närvarande endast aktivera en kundhanterad nyckel när du skapar ett register. När du aktiverar nyckeln konfigurerar du en *användardefinierad* hanterad identitet för att få åtkomst till nyckel valvet.
* När du har aktiverat kryptering med en kundhanterad nyckel i ett register kan du inte inaktivera krypteringen.  
* Azure Container Registry stöder endast RSA-eller RSA-HSM-nycklar. Elliptic kurv nycklar stöds inte för närvarande.
* [Innehålls förtroende](container-registry-content-trust.md) stöds för närvarande inte i ett register som är krypterat med en kundhanterad nyckel.
* I ett register som är krypterat med en kundhanterad nyckel behålls kör loggar för [ACR-aktiviteter](container-registry-tasks-overview.md) för närvarande endast i 24 timmar. Om du behöver behålla loggarna under en längre period, se rikt linjer för att [Exportera och lagra aktivitets körnings loggar](container-registry-tasks-logs.md#alternative-log-storage).


> [!IMPORTANT]
> Om du planerar att lagra register krypterings nyckeln i ett befintligt Azure Key Vault som nekar offentlig åtkomst och bara tillåter privat slut punkt eller valda virtuella nätverk krävs ytterligare konfigurations steg. Se [Avancerat scenario: Key Vault brand vägg](#advanced-scenario-key-vault-firewall) i den här artikeln.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatisk eller manuell uppdatering av nyckel versioner

Ett viktigt övervägande för säkerheten i ett register som krypteras med en kundhanterad nyckel är hur ofta du uppdaterar krypterings nyckeln (rotera). Din organisation kan ha efterlevnadsprinciper som kräver att nyckel [versioner](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) som lagras regelbundet uppdateras i Azure Key Vault när de används som Kundhanterade nycklar. 

När du konfigurerar register kryptering med en kundhanterad nyckel har du två alternativ för att uppdatera nyckel versionen som används för kryptering:

* **Uppdatera nyckel versionen automatiskt** för att automatiskt uppdatera en kundhanterad nyckel när en ny version är tillgänglig i Azure Key Vault, utelämna nyckel versionen när du aktiverar register kryptering med en kundhanterad nyckel. När ett register krypteras med en icke-versions nyckel kontrollerar Azure Container Registry regelbundet nyckel valvet för en ny nyckel version och uppdaterar den Kundhanterade nyckeln inom en timme. Azure Container Registry använder automatiskt den senaste versionen av nyckeln.

* **Uppdatera nyckel versionen manuellt** – om du vill använda en specifik version av en nyckel för register kryptering anger du den nyckel versionen när du aktiverar register kryptering med en kundhanterad nyckel. När ett register krypteras med en specifik nyckel version använder Azure Container Registry den versionen för kryptering tills du roterar den Kundhanterade nyckeln manuellt.

Mer information finns i [Välj nyckel-ID med eller utan nyckel version](#choose-key-id-with-or-without-key-version) och [uppdaterings nyckel version](#update-key-version)längre fram i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-stegen i den här artikeln behöver du Azure CLI version 2.2.0 eller senare, eller Azure Cloud Shell. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Aktivera kundhanterad nyckel-CLI

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp för att skapa nyckel valvet, container Registry och andra nödvändiga resurser.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användardefinierad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) med kommandot [AZ Identity Create][az-identity-create] . Den här identiteten används av ditt register för att få åtkomst till tjänsten Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

I kommandot utdata noterar du följande värden: `id` och `principalId` . Du behöver dessa värden i senare steg för att konfigurera register åtkomst till nyckel valvet.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

För enkelhetens skull kan du lagra dessa värden i miljövariabler:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa ett nyckel valv med [AZ-valv skapa][az-keyvault-create] för att lagra en kundhanterad nyckel för register kryptering. 

Som standard aktive ras inställningen för **mjuk borttagning** automatiskt i ett nytt nyckel valv. För att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valv borttagningar, aktiverar du även inställningen för att **ta bort skydd** .

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Hämta resurs-ID för nyckel valvet för användning i senare steg:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den. I följande AZ för nyckel [valv anger][az-keyvault-set-policy] du huvud-ID: t för den hanterade identitet som du har skapat, lagrad tidigare i en miljö variabel. Ange nyckel behörigheter för att **Hämta**, **unwrapKey** och **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) för att tilldela behörigheter till identiteten för att få åtkomst till nyckel valvet. Tilldela till exempel krypterings rollen Key Vault krypterings tjänst till identiteten med hjälp av kommandot [AZ roll tilldelning Create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Skapa nyckel och hämta nyckel-ID

Kör kommandot [AZ Key Vault Key Create][az-keyvault-key-create] för att skapa en nyckel i nyckel valvet.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Anteckna nyckelns ID i kommandot utdata `kid` . Du använder det här ID: t i nästa steg:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Välj nyckel-ID med eller utan nyckel version

För enkelhetens skull kan du lagra det format du väljer för nyckel-ID: t i $keyID-miljövariabeln. Du kan använda ett nyckel-ID med en version eller nyckel utan version.

#### <a name="manual-key-rotation---key-id-with-version"></a>Manuell nyckel rotation – nyckel-ID med version

När den används för att kryptera ett register med en kundhanterad nyckel, tillåter den här nyckeln endast manuell nyckel rotation i Azure Container Registry.

I det här exemplet lagras nyckelns `kid` egenskap:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatisk nyckel rotation-nyckel-ID utelämna version 

När den här nyckeln används för att kryptera ett register med en kundhanterad nyckel, aktiverar den här nyckeln automatisk nyckel rotation när en ny nyckel version identifieras i Azure Key Vault.

I det här exemplet tas versionen bort från nyckelns `kid` egenskap:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Skapa ett register med kundhanterad nyckel

Kör kommandot [AZ ACR Create][az-acr-create] för att skapa ett register i Premium service-nivån och aktivera kundhanterad nyckel. Skicka det hanterade identitets-ID: t och nyckel-ID: t som tidigare fanns i miljövariabler:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visa krypterings status

Om du vill visa om register kryptering med en kundhanterad nyckel är aktive rad kör du kommandot [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

Beroende på vilken nyckel som används för att kryptera registret liknar utdata följande:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status&quot;: &quot;enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Aktivera kundhanterad nyckel – Portal

### <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Skapa en användardefinierad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i Azure Portal. Anvisningar finns i [skapa en användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Du kan använda identitetens namn i senare steg.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Skapa användare tilldelad identitet i Azure Portal":::

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Anvisningar för hur du skapar ett nyckel valv finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

När du skapar ett nyckel valv för en kundhanterad nyckel, går du till fliken **grundläggande** och aktiverar inställningen för att **ta bort skydd** . Den här inställningen hjälper till att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valv borttagningar.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Skapa nyckel valv i Azure Portal":::

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den.

1. Navigera till ditt nyckel valv.
1. Välj **Inställningar**  >  **åtkomst principer > + Lägg till åtkomst princip**.
1. Välj **nyckel behörigheter** och välj **Hämta**, **unwrap Key** och **wrap Key**.
1. I **Välj huvud** namn väljer du resurs namnet för din användarspecifika hanterade identitet.  
1. Välj **Lägg till** och välj sedan **Spara**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Skapa åtkomst princip för nyckel valv":::

Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) för att tilldela behörigheter till identiteten för att få åtkomst till nyckel valvet. Tilldela till exempel krypterings rollen Key Vault krypterings tjänst till identiteten.

1. Navigera till ditt nyckel valv.
1. Välj **åtkomst kontroll (IAM)**  >  **+ Lägg** till  >  **Lägg till roll tilldelning**.
1. I fönstret **Lägg till roll tilldelning** :
    1. Välj **Key Vault användar rollen krypterings tjänst kryptering** . 
    1. Tilldela åtkomst till **användare som tilldelats en hanterad identitet**.
    1. Välj resurs namnet för din användarspecifika hanterade identitet och välj **Spara**.

### <a name="create-key-optional"></a>Skapa nyckel (valfritt)

Du kan också skapa en nyckel i nyckel valvet som används för att kryptera registret. Följ dessa steg om du vill välja en specifik nyckel version som en kundhanterad nyckel. 

1. Navigera till ditt nyckel valv.
1. Välj **Inställningar**  >  **nycklar**.
1. Välj **+ generera/importera** och ange ett unikt namn för nyckeln.
1. Acceptera återstående standardvärden och välj **skapa**.
1. När du har skapat väljer du nyckeln och väljer sedan den aktuella versionen. Kopiera **nyckel-ID** : t för nyckel versionen.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Välj **skapa en resurs**  >  **behållare**  >  **container Registry**.
1. På fliken **grundläggande** , Välj eller skapa en resurs grupp och ange ett register namn. I **SKU** väljer du **Premium**.
1. På fliken **kryptering** i **kundhanterad nyckel** väljer du **aktive rad**.
1. I **identitet** väljer du den hanterade identitet som du skapade.
1. I **kryptering** väljer du något av följande:
    * Välj **Välj från Key Vault** och välj ett befintligt nyckel valv och nyckel, eller **skapa ett nytt**. Den nyckel du väljer är icke-versions aktive ras och aktiverar automatisk nyckel rotation.
    * Välj **Ange nyckel-URI** och ange en nyckel identifierare direkt. Du kan ange antingen en versions nyckel-URI (för en nyckel som måste roteras manuellt) eller en icke-versions nyckel-URI (som aktiverar automatisk nyckel rotation). 
1. På fliken **kryptering** väljer du **Granska + skapa**.
1. Välj **skapa** för att distribuera register instansen.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Skapa ett krypterat register i Azure Portal":::

Om du vill se krypterings status för registret i portalen går du till registret. Under **Inställningar** väljer du  **kryptering**.

## <a name="enable-customer-managed-key---template"></a>Aktivera kundhanterad nyckel-mall

Du kan också använda en Resource Manager-mall för att skapa ett register och aktivera kryptering med en kundhanterad nyckel.

Följande mall skapar ett nytt behållar register och en användardefinierad hanterad identitet. Kopiera följande innehåll till en ny fil och spara det med ett fil namn, till exempel `CMKtemplate.json` .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Följ stegen i föregående avsnitt för att skapa följande resurser:

* Nyckel valv, identifierat med namn
* Nyckel valv nyckel, identifierad efter nyckel-ID

Kör följande [AZ distribution Group Create][az-deployment-group-create] -kommando för att skapa registret med hjälp av föregående mallfil. Ange ett nytt register namn och namn på den hanterade identiteten, samt namnet på nyckel valvet och nyckel-ID: t som du skapade.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Visa krypterings status

Om du vill visa status för register kryptering kör du kommandot [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Använda registret

När du har aktiverat en kundhanterad nyckel i ett register kan du utföra samma register åtgärder som du utför i ett register som inte är krypterat med en kundhanterad nyckel. Du kan till exempel autentisera med registret och push Docker-avbildningar. Se exempel kommandon i [push och hämta en avbildning](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotations nyckel

Uppdatera nyckel versionen i Azure Key Vault eller skapa en ny nyckel och uppdatera sedan registret för att kryptera data med hjälp av nyckeln. Du kan utföra dessa steg med hjälp av Azure CLI eller i portalen.

När du roterar en nyckel anger du vanligt vis samma identitet som när du skapar registret. Du kan också konfigurera en ny användardefinierad identitet för nyckel åtkomst eller aktivera och ange registrets systemtilldelade identitet.

> [!NOTE]
> Se till att den identitet som krävs för åtkomst till nyckel [valvet](#enable-key-vault-access) har angetts för den identitet du konfigurerar.

### <a name="update-key-version"></a>Uppdatera nyckel version

Ett vanligt scenario är att uppdatera den nyckel version som används som en kundhanterad nyckel. Beroende på hur register krypteringen konfigureras, uppdateras den Kundhanterade nyckeln i Azure Container Registry automatiskt eller måste uppdateras manuellt.

### <a name="azure-cli"></a>Azure CLI

Använd [AZ Key Vault Key][az-keyvault-key] commands för att skapa eller hantera nyckel valv nycklar. Om du vill skapa en ny nyckel version kör du kommandot [AZ Key Vault Key Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Nästa steg beror på hur register krypteringen är konfigurerad:

* Om registret är konfigurerat för att identifiera nyckel versions uppdateringar, uppdateras den Kundhanterade nyckeln automatiskt inom 1 timme.

* Om registret är konfigurerat för att kräva manuell uppdatering för en ny nyckel version, kör du kommandot [AZ ACR Encryption rotation-Key][az-acr-encryption-rotate-key] och skickar det nya nyckel-ID: t och identiteten som du vill konfigurera:

Så här uppdaterar du den kund hanterade nyckel versionen manuellt:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> När du kör `az acr encryption rotate-key` kan du antingen skicka ett versions nyckel-ID eller ett nyckel-ID som inte är en version. Om du använder ett icke-versions nyckel-ID konfigureras registret för att automatiskt identifiera senare nyckel versions uppdateringar.

### <a name="portal"></a>Portal

Använd registrets **krypterings** inställningar för att uppdatera nyckel valvet, nyckeln eller identitets inställningarna som används för den Kundhanterade nyckeln.

Om du till exempel vill konfigurera en ny nyckel:

1. I portalen navigerar du till ditt register.
1. Under **Inställningar** väljer du **krypterings**  >  **ändrings nyckel**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Rotera nyckel i Azure Portal":::
1. I **kryptering** väljer du något av följande:
    * Välj **Välj från Key Vault** och välj ett befintligt nyckel valv och nyckel, eller **skapa ett nytt**. Den nyckel du väljer är icke-versions aktive ras och aktiverar automatisk nyckel rotation.
    * Välj **Ange nyckel-URI** och ange en nyckel identifierare direkt. Du kan ange antingen en versions nyckel-URI (för en nyckel som måste roteras manuellt) eller en icke-versions nyckel-URI (som aktiverar automatisk nyckel rotation).
1. Slutför valet av nyckel och välj **Spara**.

## <a name="revoke-key"></a>Återkalla nyckel

Återkalla den Kundhanterade krypterings nyckeln genom att ändra åtkomst principen eller behörigheterna för nyckel valvet eller genom att ta bort nyckeln. Använd exempelvis kommandot [AZ-][az-keyvault-delete-policy] för att ändra åtkomst principen för den hanterade identitet som används i registret:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Att återkalla nyckeln på ett effektivt sätt blockerar åtkomsten till alla register data, eftersom registret inte har åtkomst till krypterings nyckeln. Om åtkomst till nyckeln är aktive rad eller om den borttagna nyckeln återställs, kommer ditt register att välja nyckeln så att du kan komma åt krypterade register data igen.

## <a name="advanced-scenario-key-vault-firewall"></a>Avancerat scenario: Key Vault brand vägg

Du kanske vill lagra krypterings nyckeln med hjälp av ett befintligt Azure Key Vault som kon figurer ATS med en [Key Vault-brandvägg](../key-vault/general/network-security.md), som nekar offentlig åtkomst och bara tillåter privat slut punkt eller valda virtuella nätverk. 

I det här scenariot ska du först skapa en ny användardefinierad identitet, nyckel valv och behållar register som är krypterad med en kundhanterad nyckel med hjälp av [Azure CLI](#enable-customer-managed-key---cli), [portalen](#enable-customer-managed-key---portal)eller [mallen](#enable-customer-managed-key---template). Detaljerade anvisningar finns i föregående avsnitt i den här artikeln.
   > [!NOTE]
   > Det nya nyckel valvet distribueras utanför brand väggen. Den används endast tillfälligt för att lagra den Kundhanterade nyckeln.

När du har skapat registret fortsätter du med följande steg. Informationen finns i följande avsnitt.

1. Aktivera registrets tilldelade identitet.
1. Ge de systemtilldelade identitets behörigheterna åtkomst till nycklar i nyckel valvet som är begränsade med Key Vault brand väggen.
1. Se till att Key Vault brand väggen tillåter att betrodda tjänster kringgås. För närvarande kan ett Azure Container Registry bara kringgå brand väggen när den använder sin system-hanterade identitet. 
1. Rotera den Kundhanterade nyckeln genom att välja en i nyckel valvet som är begränsat till Key Vault brand väggen.
1. När de inte längre behövs kan du ta bort nyckel valvet som har skapats utanför brand väggen.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Steg 1 – aktivera registrets systemtilldelade identitet

1. I portalen navigerar du till ditt register.
1. Välj **Inställningar**  >   **identitet**.
1. Under **systemtilldelat**, ställer du in **status** **på på**. Välj **Spara**.
1. Kopiera identitetens **objekt-ID** .

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Steg 2 – bevilja tilldelad identitets åtkomst till ditt nyckel valv

1. I portalen navigerar du till ditt nyckel valv.
1. Välj **Inställningar**  >  **åtkomst principer > + Lägg till åtkomst princip**.
1. Välj **nyckel behörigheter** och välj **Hämta**, **unwrap Key** och **wrap Key**.
1. Välj **Välj huvud namn** och Sök efter objekt-ID: t för den systemtilldelade hanterade identiteten eller namnet på registret.  
1. Välj **Lägg till** och välj sedan **Spara**.

### <a name="step-3---enable-key-vault-bypass"></a>Steg 3 – Aktivera kringgå nyckel valv

För att få åtkomst till ett nyckel valv som kon figurer ATS med en Key Vault brand vägg, måste registret kringgå brand väggen. Kontrol lera att nyckel valvet har kon figurer ATS för att tillåta åtkomst av [betrodda tjänster](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry är en av de betrodda tjänsterna.

1. I portalen navigerar du till ditt nyckel valv.
1. Välj **Inställningar**  >  **nätverk**.
1. Bekräfta, uppdatera eller Lägg till inställningar för virtuellt nätverk. Detaljerade anvisningar finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](../key-vault/general/network-security.md).
1. I **Tillåt Microsoft-betrodda tjänster att kringgå den här brand väggen väljer du** **Ja**. 

### <a name="step-4---rotate-the-customer-managed-key"></a>Steg 4 – rotera den Kundhanterade nyckeln

När du har slutfört föregående steg roterar du till en nyckel som lagras i nyckel valvet bakom en brand vägg.

1. I portalen navigerar du till ditt register.
1. Under **Inställningar** väljer du **krypterings**  >  **ändrings nyckel**.
1. I **identitet** väljer du **tilldelat system**.
1. Välj **Välj från Key Vault** och välj namnet på nyckel valvet bakom en brand vägg.
1. Välj en befintlig nyckel eller **skapa en ny**. Den nyckel du väljer är icke-versions aktive ras och aktiverar automatisk nyckel rotation.
1. Slutför valet av nyckel och välj **Spara**.

## <a name="troubleshoot"></a>Felsöka

### <a name="removing-managed-identity"></a>Tar bort hanterad identitet


Om du försöker ta bort en användare som tilldelats eller tilldelats en hanterad identitet från ett register som används för att konfigurera kryptering, kan ett fel meddelande visas som liknar följande:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Du kan inte heller ändra krypterings nyckeln (rotera). Lösnings stegen beror på vilken typ av identitet som används för kryptering.

**Användare tilldelad identitet**

Om det här problemet uppstår med en användardefinierad identitet måste du först tilldela om identiteten med hjälp av det GUID som visas i fel meddelandet. Exempel:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
När du har ändrat nyckeln och tilldelat en annan identitet kan du ta bort den ursprungliga användarens identitet.

**Systemtilldelad identitet**

Om det här problemet uppstår med en tilldelad identitet [skapar du ett support ärende för Azure](https://azure.microsoft.com/support/create-ticket/) för att få hjälp att återställa identiteten.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md).
* Lär dig mer om åtkomst principer och hur du [skyddar åtkomsten till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
