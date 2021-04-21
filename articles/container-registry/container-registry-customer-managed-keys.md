---
title: Kryptera register med en kund hanterad nyckel
description: Lär dig mer om kryptering i vila av ditt Azure-containerregister och hur du krypterar ditt Premium-register med en kund hanterad nyckel som lagras i Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 9ec32e32d187a3db07f023c78efbd301ef578cbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817043"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Kryptera registret med en kund hanterad nyckel

När du lagrar avbildningar och andra artefakter i ett Azure-containerregister krypterar Azure automatiskt registerinnehållet i vila med [tjänst hanterade nycklar.](../security/fundamentals/encryption-models.md) Du kan komplettera standardkryptering med ytterligare ett krypteringslager med hjälp av en nyckel som du skapar och hanterar i Azure Key Vault (en kundhanterade nyckel). Den här artikeln beskriver steg för steg hur du använder Azure CLI, Azure Portal eller en Resource Manager mall.

Kryptering på serversidan med kund hanterade nycklar stöds via integrering med [Azure Key Vault](../key-vault/general/overview.md): 

* Du kan skapa egna krypteringsnycklar och lagra dem i ett nyckelvalv eller använda Azure Key Vault-API:er för att generera nycklar. 
* Med Azure Key Vault kan du också granska nyckelanvändningen.
* Azure Container Registry stöder automatisk rotation av registerkrypteringsnycklar när en ny nyckelversion är tillgänglig i Azure Key Vault. Du kan också rotera krypteringsnycklar för registret manuellt.

Den här funktionen är tillgänglig på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry tjänstnivåer](container-registry-skus.md).


## <a name="things-to-know"></a>Saker att känna till

* För närvarande kan du bara aktivera en kund hanterad nyckel när du skapar ett register. När du aktiverar nyckeln konfigurerar du en användar *tilldelad hanterad* identitet för åtkomst till nyckelvalvet.
* När du har aktivera kryptering med en kund hanterad nyckel i ett register kan du inte inaktivera krypteringen.  
* Azure Container Registry stöder endast RSA- eller RSA-HSM-nycklar. Elliptic Curve-nycklar stöds inte för närvarande.
* [Innehållsförtroende](container-registry-content-trust.md) stöds för närvarande inte i ett register som krypterats med en kund hanterad nyckel.
* I ett register som är krypterat med en kund hanterad nyckel bevaras [körningsloggar för ACR-uppgifter](container-registry-tasks-overview.md) för närvarande bara i 24 timmar. Om du behöver behålla loggar under en längre period kan du gå till vägledningen för att [exportera och lagra aktivitetskörningsloggar.](container-registry-tasks-logs.md#alternative-log-storage)


> [!IMPORTANT]
> Om du planerar att lagra registerkrypteringsnyckeln i ett befintligt Azure-nyckelvalv som nekar offentlig åtkomst och endast tillåter privata slutpunkter eller valda virtuella nätverk krävs ytterligare konfigurationssteg. Se [Avancerat scenario: Key Vault brandväggen](#advanced-scenario-key-vault-firewall) i den här artikeln.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatisk eller manuell uppdatering av nyckelversioner

En viktig faktor för säkerheten i ett register som krypterats med en kund hanterad nyckel är hur ofta du uppdaterar (roterar) krypteringsnyckeln. Din organisation kan ha efterlevnadsprinciper som kräver regelbunden uppdatering av [nyckelversioner](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) som lagras i Azure Key Vault när de används som kund hanterade nycklar. 

När du konfigurerar registerkryptering med en kund hanterad nyckel har du två alternativ för att uppdatera nyckelversionen som används för kryptering:

* **Uppdatera automatiskt** nyckelversionen – Om du vill uppdatera en kund hanterad nyckel automatiskt när en ny version är tillgänglig i Azure Key Vault utelämnar du nyckelversionen när du aktiverar registerkryptering med en kund hanterad nyckel. När ett register krypteras med en nyckel som inte är versionshantering Azure Container Registry regelbundet nyckelvalvet efter en ny nyckelversion och uppdaterar den kund hanterade nyckeln inom en timme. Azure Container Registry använder automatiskt den senaste versionen av nyckeln.

* **Uppdatera nyckelversionen manuellt –** Om du vill använda en specifik version av en nyckel för registerkryptering anger du den nyckelversionen när du aktiverar registerkryptering med en kund hanterad nyckel. När ett register krypteras med en viss nyckelversion använder Azure Container Registry den versionen för kryptering tills du manuellt roterar den kund hanterade nyckeln.

Mer information finns i Choose key ID with or without key version (Välj nyckel-ID med eller utan [nyckelversion)](#choose-key-id-with-or-without-key-version) och Update key version (Uppdatera [nyckelversion)](#update-key-version)senare i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-stegen i den här artikeln behöver du Azure CLI version 2.2.0 eller senare eller Azure Cloud Shell. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Aktivera kund hanterad nyckel – CLI

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du [kommandot az group create][az-group-create] för att skapa en resursgrupp för att skapa nyckelvalvet, containerregistret och andra nödvändiga resurser.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användar tilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) med [kommandot az identity create.][az-identity-create] Den här identiteten används av ditt register för att komma åt Key Vault tjänsten.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Notera följande värden i kommandoutdata: `id` och `principalId` . Du behöver dessa värden i senare steg för att konfigurera registeråtkomst till nyckelvalvet.

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

För enkelhetens skull lagrar du dessa värden i miljövariabler:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa ett nyckelvalv med [az keyvault create för][az-keyvault-create] att lagra en kund hanterad nyckel för registerkryptering. 

Inställningen för mjuk borttagning **aktiveras** som standard automatiskt i ett nytt nyckelvalv. Om du vill förhindra dataförlust som orsakas av oavsiktliga borttagningar av nyckel- eller nyckelvalv aktiverar **du även inställningen rensningsskydd.**

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

För användning i senare steg hämtar du resurs-ID:t för nyckelvalvet:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckelvalv

Konfigurera en princip för nyckelvalvet så att identiteten kan komma åt det. I följande [az keyvault set-policy-kommando][az-keyvault-set-policy] skickar du huvudnamns-ID:t för den hanterade identitet som du skapade, som lagrades tidigare i en miljövariabel. Ange nyckelbehörigheter **för att** hämta , **unwrapKey** och **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) att tilldela behörigheter till identiteten för åtkomst till nyckelvalvet. Du kan till exempel tilldela Key Vault Crypto Service Encryption-rollen till identiteten med hjälp av [kommandot az role assignment create:](/cli/azure/role/assignment#az_role_assignment_create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Skapa nyckel och hämta nyckel-ID

Kör kommandot [az keyvault key create][az-keyvault-key-create] för att skapa en nyckel i nyckelvalvet.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Anteckna nyckelns ID i kommandoutdata, `kid` . Du använder detta ID i nästa steg:

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

### <a name="choose-key-id-with-or-without-key-version"></a>Välj nyckel-ID med eller utan nyckelversion

Lagra det format du väljer för nyckel-ID:t i $keyID miljövariabeln. Du kan använda ett nyckel-ID med en version eller en nyckel utan en version.

#### <a name="manual-key-rotation---key-id-with-version"></a>Manuell nyckelrotation – nyckel-ID med version

När den används för att kryptera ett register med en kund hanterad nyckel tillåter den här nyckeln endast manuell nyckelrotation i Azure Container Registry.

I det här exemplet lagras nyckelns `kid` -egenskap:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatisk nyckelrotation – nyckel-ID utesluter version 

När den används för att kryptera ett register med en kund hanterad nyckel aktiverar den här nyckeln automatisk nyckelrotation när en ny nyckelversion identifieras i Azure Key Vault.

Det här exemplet tar bort versionen från nyckelns `kid` -egenskap:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Skapa ett register med kund hanterad nyckel

Kör kommandot [az acr create][az-acr-create] för att skapa ett register på Premium-tjänstnivån och aktivera den kund hanterade nyckeln. Skicka det hanterade identitets-ID:t och nyckel-ID:t som lagrades tidigare i miljövariabler:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visa krypteringsstatus

Om du vill visa om registerkryptering med en kund hanterad nyckel är aktiverat kör du [kommandot az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name>
```

Beroende på vilken nyckel som används för att kryptera registret ser utdata ut ungefär så här:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Aktivera kund hanterad nyckel – portalen

### <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Skapa en användar tilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i Azure Portal. Anvisningar finns i Skapa [en användar tilldelad identitet.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

Du använder identitetens namn i senare steg.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Skapa användar tilldelad identitet i Azure Portal":::

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Anvisningar för hur du skapar ett nyckelvalv finns [i Snabbstart: Skapa ett nyckelvalv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

När du skapar ett nyckelvalv för en kund hanterad nyckel går du till fliken **Grundläggande** inställningar och aktiverar **inställningen Rensa** skydd. Den här inställningen förhindrar dataförlust som orsakas av oavsiktliga borttagningar av nycklar eller nyckelvalv.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Skapa nyckelvalv i Azure Portal":::

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckelvalv

Konfigurera en princip för nyckelvalvet så att identiteten kan komma åt det.

1. Gå till ditt nyckelvalv.
1. Välj **Inställningar**  >  **Åtkomstprinciper > +Lägg till åtkomstprincip**.
1. Välj **Nyckelbehörigheter** och **välj Hämta,** **Packa upp nyckel** och **Radbryt nyckel.**
1. I **Välj huvudnamn** väljer du resursnamnet för den användar tilldelade hanterade identiteten.  
1. Välj **Lägg** till och sedan **Spara.**

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Skapa åtkomstprincip för nyckelvalv":::

Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) att tilldela behörigheter till identiteten för åtkomst till nyckelvalvet. Du kan till exempel tilldela Key Vault krypteringsrollen för kryptografitjänsten till identiteten.

1. Gå till ditt nyckelvalv.
1. Välj **Åtkomstkontroll (IAM)**  >  **+ Lägg till lägg** till  >  **rolltilldelning**.
1. I fönstret **Lägg till rolltilldelning:**
    1. Välj **Key Vault krypteringsanvändarrollen kryptografitjänst.** 
    1. Tilldela åtkomst till användar **tilldelad hanterad identitet**.
    1. Välj resursnamnet för den användar tilldelade hanterade identiteten och välj **Spara.**

### <a name="create-key-optional"></a>Skapa nyckel (valfritt)

Du kan också skapa en nyckel i nyckelvalvet som ska användas för att kryptera registret. Följ dessa steg om du vill välja en specifik nyckelversion som en kund hanterad nyckel. 

1. Gå till ditt nyckelvalv.
1. Välj **Inställningsnycklar.**  >  
1. Välj **+Generate/Import (Generera/importera)** och ange ett unikt namn för nyckeln.
1. Acceptera de återstående standardvärdena och välj **Skapa.**
1. När du har skapat den väljer du nyckeln och sedan den aktuella versionen. Kopiera **nyckelidentifieraren** för nyckelversionen.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Välj **Skapa en resurs**  >  **Containrar**  >  **Container Registry**.
1. På fliken **Grundläggande inställningar** väljer eller skapar du en resursgrupp och anger ett registernamn. I **SKU** väljer du **Premium**.
1. På fliken **Kryptering** går du till **Kund-hanterad nyckel och** väljer **Aktiverad.**
1. I **Identitet** väljer du den hanterade identitet som du skapade.
1. I **Kryptering** väljer du något av följande:
    * Välj **Välj från Key Vault** och välj ett befintligt nyckelvalv och en nyckel, eller Skapa **ny.** Den nyckel du väljer är inte versionshantering och aktiverar automatisk nyckelrotation.
    * Välj **Ange nyckel-URI** och ange en nyckelidentifierare direkt. Du kan ange antingen en versionsindelade nyckel-URI (för en nyckel som måste roteras manuellt) eller en nyckel-URI som inte är versionshantering (som aktiverar automatisk nyckelrotation). 
1. På fliken **Kryptering** väljer du **Granska + skapa**.
1. Välj **Skapa** för att distribuera registerinstansen.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Skapa krypterat register i Azure Portal":::

Om du vill se krypteringsstatusen för ditt register i portalen går du till registret. Under **Inställningar** väljer du **Kryptering.**

## <a name="enable-customer-managed-key---template"></a>Aktivera kund hanterad nyckel – mall

Du kan också använda en Resource Manager för att skapa ett register och aktivera kryptering med en kund hanterad nyckel.

Följande mall skapar ett nytt containerregister och en användar tilldelad hanterad identitet. Kopiera följande innehåll till en ny fil och spara det med ett filnamn, till exempel `CMKtemplate.json` .

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

* Nyckelvalv, identifierat med namn
* Nyckelvalvsnyckel, identifierad med nyckel-ID

Kör följande az [deployment group create-kommando][az-deployment-group-create] för att skapa registret med hjälp av föregående mallfil. Där det anges anger du ett nytt registernamn och ett hanterat identitetsnamn, samt det nyckelvalvsnamn och nyckel-ID som du skapade.

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

### <a name="show-encryption-status"></a>Visa krypteringsstatus

Om du vill visa status för registerkryptering kör du [kommandot az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Använda registret

När du har aktiverar en kund hanterad nyckel i ett register kan du utföra samma registeråtgärder som du utför i ett register som inte är krypterad med en kund hanterad nyckel. Du kan till exempel autentisera med registret och skicka Docker-avbildningar. Se exempelkommandon i [Push och hämta en avbildning](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotera nyckel

Uppdatera nyckelversionen i Azure Key Vault skapa en ny nyckel och uppdatera sedan registret för att kryptera data med hjälp av nyckeln. Du kan utföra dessa steg med hjälp av Azure CLI eller i portalen.

När du roterar en nyckel anger du vanligtvis samma identitet som användes när du skapade registret. Du kan också konfigurera en ny användar tilldelad identitet för nyckelåtkomst eller aktivera och ange registrets system tilldelade identitet.

> [!NOTE]
> Kontrollera att den nödvändiga [nyckelvalvsåtkomsten](#enable-key-vault-access) har angetts för den identitet som du konfigurerar för nyckelåtkomst.

### <a name="update-key-version"></a>Uppdatera nyckelversion

Ett vanligt scenario är att uppdatera versionen av nyckeln som används som en kund hanterad nyckel. Beroende på hur registerkrypteringen konfigureras uppdateras den kund hanterade nyckeln i Azure Container Registry uppdateras automatiskt eller måste uppdateras manuellt.

### <a name="azure-cli"></a>Azure CLI

Använd [az keyvault key commands][az-keyvault-key] för att skapa eller hantera dina nyckelvalvsnycklar. Skapa en ny nyckelversion genom att köra [kommandot az keyvault key][az-keyvault-key-create] create:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Nästa steg beror på hur registerkrypteringen konfigureras:

* Om registret är konfigurerat för att identifiera uppdateringar av nyckelversion uppdateras den kund hanterade nyckeln automatiskt inom en timme.

* Om registret är konfigurerat för att kräva manuell uppdatering för en ny nyckelversion kör du [kommandot az acr encryption rotate-key][az-acr-encryption-rotate-key] och skicka det nya nyckel-ID:t och den identitet som du vill konfigurera:

Så här uppdaterar du den kund hanterade nyckelversionen manuellt:

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
> När du kör `az acr encryption rotate-key` kan du skicka antingen ett versions-ID eller ett nyckel-ID som inte är versionsversion. Om du använder ett nyckel-ID som inte är versionsversion konfigureras registret för att automatiskt identifiera senare uppdateringar av nyckelversionen.

### <a name="portal"></a>Portalen

Använd registrets **krypteringsinställningar för** att uppdatera de nyckelvalvs-, nyckel- eller identitetsinställningar som används för den kund hanterade nyckeln.

Så här konfigurerar du till exempel en ny nyckel:

1. Navigera till registret i portalen.
1. Under **Inställningar** väljer du **Krypteringsändringsnyckel**  >  .

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Rotera nyckeln i Azure Portal":::
1. I **Kryptering** väljer du något av följande:
    * Välj **Välj från Key Vault** och välj ett befintligt nyckelvalv och en nyckel, eller Skapa **ny**. Den nyckel som du väljer är inte versionshantering och aktiverar automatisk nyckelrotation.
    * Välj **Ange nyckel-URI** och ange en nyckelidentifierare direkt. Du kan ange antingen en versionsindelade nyckel-URI (för en nyckel som måste roteras manuellt) eller en nyckel-URI som inte är versionshantering (som aktiverar automatisk nyckelrotation).
1. Slutför nyckelmarkeringen och välj **Spara.**

## <a name="revoke-key"></a>Återkalla nyckel

Återkalla den kund hanterade krypteringsnyckeln genom att ändra åtkomstprincipen eller behörigheterna för nyckelvalvet eller genom att ta bort nyckeln. Använd till exempel kommandot [az keyvault delete-policy][az-keyvault-delete-policy] för att ändra åtkomstprincipen för den hanterade identiteten som används av registret:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Om nyckeln återkallas blockeras åtkomsten till alla registerdata, eftersom registret inte kan komma åt krypteringsnyckeln. Om åtkomst till nyckeln är aktiverad eller om den borttagna nyckeln har återställts väljer registret nyckeln så att du kan komma åt krypterade registerdata igen.

## <a name="advanced-scenario-key-vault-firewall"></a>Avancerat scenario: Key Vault brandvägg

Du kanske vill lagra krypteringsnyckeln med hjälp av ett befintligt Azure-nyckelvalv som konfigurerats med [en Key Vault-brandvägg](../key-vault/general/network-security.md), som nekar offentlig åtkomst och endast tillåter privata slutpunkter eller valda virtuella nätverk. 

I det här scenariot skapar du först en ny användartilldelade identitet, nyckelvalv och containerregister som krypterats med en kund hanterad nyckel med hjälp av [Azure CLI,](#enable-customer-managed-key---cli) [portalen](#enable-customer-managed-key---portal)eller [mallen](#enable-customer-managed-key---template). Detaljerade anvisningar finns i föregående avsnitt i den här artikeln.
   > [!NOTE]
   > Det nya nyckelvalvet distribueras utanför brandväggen. Den används bara tillfälligt för att lagra den kund hanterade nyckeln.

När registret har skapats fortsätter du med följande steg. Informationen finns i följande avsnitt.

1. Aktivera registrets system tilldelade identitet.
1. Ge den system tilldelade identiteten behörighet att komma åt nycklar i nyckelvalvet som är begränsad med Key Vault brandväggen.
1. Se till att Key Vault brandväggen tillåter förbikoppling av betrodda tjänster. För närvarande kan ett Azure-containerregister endast kringgå brandväggen när dess system hanterade identitet används. 
1. Rotera den kund hanterade nyckeln genom att välja en i nyckelvalvet som är begränsat med Key Vault brandväggen.
1. När nyckelvalvet som skapades utanför brandväggen inte längre behövs kan du ta bort det.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Steg 1 – Aktivera registrets system tilldelade identitet

1. Navigera till registret i portalen.
1. Välj **Inställningar**  >   **Identitet.**
1. Under **System assigned (System** **tilldelad) anger du Status** **till På**. Välj **Spara**.
1. Kopiera **identitetens objekt-ID.**

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Steg 2 – Bevilja system tilldelad identitet åtkomst till ditt nyckelvalv

1. Navigera till ditt nyckelvalv i portalen.
1. Välj **Inställningar**  >  **Åtkomstprinciper > +Lägg till åtkomstprincip**.
1. Välj **Nyckelbehörigheter** och välj **Hämta,** **Packa upp nyckel** och **Omsluta nyckel.**
1. Välj **Välj huvudnamn** och sök efter objekt-ID:t för den system tilldelade hanterade identiteten eller namnet på ditt register.  
1. Välj **Lägg** till och välj sedan **Spara.**

### <a name="step-3---enable-key-vault-bypass"></a>Steg 3 – Aktivera förbikoppling av nyckelvalv

Om du vill komma åt ett nyckelvalv som konfigurerats Key Vault en brandvägg måste registret kringgå brandväggen. Kontrollera att nyckelvalvet är konfigurerat för att tillåta åtkomst av alla [betrodda tjänster.](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) Azure Container Registry är en av de betrodda tjänsterna.

1. Navigera till ditt nyckelvalv i portalen.
1. Välj **Inställningar**  >  **Nätverk.**
1. Bekräfta, uppdatera eller lägg till inställningar för virtuellt nätverk. Detaljerade anvisningar finns i [Konfigurera Azure Key Vault brandväggar och virtuella nätverk](../key-vault/general/network-security.md).
1. I **Tillåt att Microsofts betrodda tjänster kringgår brandväggen** väljer du **Ja.** 

### <a name="step-4---rotate-the-customer-managed-key"></a>Steg 4 – Rotera den kund hanterade nyckeln

När du har slutfört föregående steg roterar du till en nyckel som lagras i nyckelvalvet bakom en brandvägg.

1. Navigera till registret i portalen.
1. Under **Inställningar** väljer du **Krypteringsändringsnyckel**  >  .
1. I **Identitet** väljer du **System tilldelad**.
1. Välj **Välj Key Vault** och välj namnet på nyckelvalvet som finns bakom en brandvägg.
1. Välj en befintlig nyckel eller **Skapa ny.** Den nyckel som du väljer är inte versionshantering och aktiverar automatisk nyckelrotation.
1. Slutför nyckelmarkeringen och välj **Spara.**

## <a name="troubleshoot"></a>Felsöka

### <a name="removing-managed-identity"></a>Ta bort hanterad identitet


Om du försöker ta bort en användar tilldelad eller system tilldelad hanterad identitet från ett register som används för att konfigurera kryptering kan ett felmeddelande visas som liknar följande:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Du kan inte heller ändra (rotera) krypteringsnyckeln. Lösningsstegen beror på vilken typ av identitet som används för kryptering.

**Användar tilldelad identitet**

Om det här problemet uppstår med en användartilldelade identitet omtilldelar du först identiteten med hjälp av det GUID som visas i felmeddelandet. Exempel:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
När du har ändrat nyckeln och tilldelat en annan identitet kan du ta bort den ursprungliga användar tilldelade identiteten.

**System tilldelad identitet**

Om det här problemet uppstår med en system tilldelad identitet skapar du [en Azure-supportbiljett](https://azure.microsoft.com/support/create-ticket/) för att få hjälp med att återställa identiteten.


## <a name="next-steps"></a>Nästa steg

* Läs mer om [kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md).
* Läs mer om åtkomstprinciper och hur du [skyddar åtkomsten till ett nyckelvalv.](../key-vault/general/security-features.md)


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
