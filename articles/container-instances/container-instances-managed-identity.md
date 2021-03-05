---
title: Aktivera hanterad identitet i behållar gruppen
description: Lär dig hur du aktiverar en hanterad identitet i Azure Container Instances som kan autentiseras med andra Azure-tjänster
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a0d029e39122ca7bb858103f4d7f88e2536850d5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198327"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Använda hanterade identiteter med Azure Container Instances

Använd [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att köra kod i Azure Container instances som samverkar med andra Azure-tjänster – utan att behålla några hemligheter eller autentiseringsuppgifter i kod. Funktionen tillhandahåller en Azure Container Instances-distribution med en automatiskt hanterad identitet i Azure Active Directory.

I den här artikeln får du lära dig mer om hanterade identiteter i Azure Container Instances och:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller tilldelad identitet i en behållar grupp
> * Ge identitets åtkomst till ett Azure Key Vault
> * Använd den hanterade identiteten för att få åtkomst till ett nyckel valv från en behållare som körs

Anpassa exemplen för att aktivera och använda identiteter i Azure Container Instances för att få åtkomst till andra Azure-tjänster. Dessa exempel är interaktiva. Men i praktiken skulle behållar avbildningarna köra kod för att få åtkomst till Azure-tjänster.
 
> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA). För närvarande stöds endast hanterade identiteter på Azure Container Instances med Linux-behållare och ännu inte med Windows-behållare.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

Använd en hanterad identitet i en behållare som körs för att autentisera till en [tjänst som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) utan att hantera autentiseringsuppgifter i behållar koden. För tjänster som inte stöder AD-autentisering kan du lagra hemligheter i ett Azure Key Vault och använda den hanterade identiteten för att få åtkomst till nyckel valvet för att hämta autentiseringsuppgifter. Mer information om hur du använder en hanterad identitet finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

 När du skapar en behållar grupp aktiverar du en eller flera hanterade identiteter genom att ange en [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) -egenskap. Du kan också aktivera eller uppdatera hanterade identiteter när en behållar grupp kör-båda åtgärderna gör att behållar gruppen startas om. Om du vill ange identiteter för en ny eller befintlig behållar grupp använder du Azure CLI, en Resource Manager-mall, en YAML-fil eller ett annat Azure-verktyg. 

Azure Container Instances stöder båda typerna av hanterade Azure-identiteter: användar tilldelning och systemtilldelade. I en behållar grupp kan du aktivera en tilldelad identitet, en eller flera användare som tilldelats identiteter eller båda typerna av identiteter. Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översikten](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Använda en hanterad identitet

Om du vill använda en hanterad identitet måste identiteten beviljas åtkomst till en eller flera Azure-tjänst resurser (till exempel en webbapp, ett nyckel valv eller ett lagrings konto) i prenumerationen. Användning av en hanterad identitet i en pågående behållare liknar användningen av en identitet i en virtuell Azure-dator. Se vägledningen för virtuella datorer för att använda en [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL eller Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)eller [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md): er.

### <a name="limitations"></a>Begränsningar

* För närvarande kan du inte använda en hanterad identitet i en behållar grupp som distribuerats till ett virtuellt nätverk.
* Du kan inte använda en hanterad identitet för att hämta en avbildning från Azure Container Registry när du skapar en behållar grupp. Identiteten är endast tillgänglig i en behållare som körs.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

I exemplen i den här artikeln används en hanterad identitet i Azure Container Instances för att få åtkomst till en Azure Key Vault-hemlighet. 

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* med följande [az group create](/cli/azure/group#az-group-create)-kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [AZ-valv](/cli/azure/keyvault#az-keyvault-create) för att skapa ett nyckel valv. Se till att ange ett unikt nyckel valvs namn. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Lagra en exempel hemlighet i nyckel valvet med kommandot [AZ Key Vault Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Fortsätt med följande exempel för att komma åt nyckel valvet med antingen en tilldelad eller systemtilldelad hanterad identitet i Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exempel 1: Använd en användardefinierad identitet för åtkomst till Azure Key Vault

### <a name="create-an-identity"></a>Skapa en identitet

Skapa först en identitet i din prenumeration med kommandot [AZ Identity Create](/cli/azure/identity#az-identity-create) . Du kan använda samma resurs grupp som användes för att skapa nyckel valvet eller använda en annan.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Om du vill använda identiteten i följande steg använder du kommandot [AZ Identity show](/cli/azure/identity#az-identity-show) för att lagra identitetens huvud namn-ID och resurs-ID i variabler.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Bevilja användare tilldelad identitets åtkomst till nyckel valvet

Kör följande [AZ-nyckel valv set-princip](/cli/azure/keyvault) kommando för att ange en åtkomst princip i nyckel valvet. I följande exempel tillåts den användare-tilldelade identiteten att hämta hemligheter från nyckel valvet:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Aktivera användardefinierad identitet i en behållar grupp

Kör följande kommando för [AZ container Create](/cli/azure/container#az-container-create) för att skapa en behållar instans baserad på Microsofts `azure-cli` avbildning. I det här exemplet finns en grupp med en behållare som du kan använda interaktivt för att köra Azure CLI för att få åtkomst till andra Azure-tjänster. I det här avsnittet används endast det grundläggande operativ systemet. Ett exempel på hur du använder Azure CLI i behållaren finns i [Aktivera systemtilldelad identitet i en behållar grupp](#enable-system-assigned-identity-on-a-container-group). 

`--assign-identity`Parametern skickar din användarspecifika hanterade identitet till gruppen. Kommandot med lång körning håller behållaren igång. I det här exemplet används samma resurs grupp som användes för att skapa nyckel valvet, men du kan ange ett annat.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrol lera statusen med kommandot [AZ container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Avsnittet i utdata ser ut ungefär så här, som visar identiteten i behållar gruppen. `principalID`Under `userAssignedIdentities` är tjänstens huvud namn för den identitet som du skapade i Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Använd användardefinierad identitet för att hämta hemlighet från Key Vault

Nu kan du använda den hanterade identiteten inom den pågående behållar instansen för att komma åt nyckel valvet. Starta först ett bash-gränssnitt i behållaren:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Kör följande kommando för att få en åtkomsttoken att använda Azure Active Directory för att autentisera till Key Vault:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Utdata:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Om du vill lagra åtkomsttoken i en variabel som ska användas i efterföljande kommandon för att autentisera kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Använd nu åtkomsttoken för att autentisera till Key Vault och läsa en hemlighet. Se till att du byter namn på nyckel valvet i URL: en (*https: \/ /mykeyvault.Vault.Azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret ser ut ungefär så här, vilket visar hemligheten. I din kod kan du parsa dessa utdata för att hämta hemligheten. Använd sedan hemligheten i en efterföljande åtgärd för att få åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exempel 2: Använd en tilldelad identitet för att få åtkomst till Azure Key Vault

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Aktivera systemtilldelad identitet i en behållar grupp

Kör följande kommando för [AZ container Create](/cli/azure/container#az-container-create) för att skapa en behållar instans baserad på Microsofts `azure-cli` avbildning. I det här exemplet finns en grupp med en behållare som du kan använda interaktivt för att köra Azure CLI för att få åtkomst till andra Azure-tjänster. 

`--assign-identity`Parametern utan ytterligare värde aktiverar en systemtilldelad hanterad identitet i gruppen. Identiteten är begränsad till resurs gruppen för behållar gruppen. Kommandot med lång körning håller behållaren igång. I det här exemplet används samma resurs grupp som användes för att skapa nyckel valvet, vilket är i identitets omfånget.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrol lera statusen med kommandot [AZ container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Avsnittet i utdata ser ut ungefär så här, vilket visar att en systemtilldelad identitet skapas i Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Ange en variabel till värdet för `principalId` (tjänstens huvud namns-ID) för identiteten, som ska användas i senare steg.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Bevilja åtkomst till behållar gruppen till nyckel valvet

Kör följande [AZ-nyckel valv set-princip](/cli/azure/keyvault) kommando för att ange en åtkomst princip i nyckel valvet. I följande exempel kan Systemhanterad identitet Hämta hemligheter från nyckel valvet:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Använd container grupp identitet för att hämta hemlighet från Key Vault

Nu kan du använda den hanterade identiteten för att komma åt nyckel valvet i den pågående behållar instansen. Starta först ett bash-gränssnitt i behållaren:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Logga först in på Azure CLI med hjälp av den hanterade identiteten:

```bash
az login --identity
```

Hämta hemligheten från nyckel valvet från den behållare som körs:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Hemlighetens värde hämtas:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivera hanterad identitet med Resource Manager-mall

Om du vill aktivera en hanterad identitet i en behållar grupp med hjälp av en [Resource Manager-mall](container-instances-multi-container-group.md)anger du `identity` egenskapen för `Microsoft.ContainerInstance/containerGroups` objektet med ett `ContainerGroupIdentity` objekt. Följande kodfragment visar `identity` egenskapen som kon figurer ATS för olika scenarier. Se [referens för Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups). Ange minst `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Användare tilldelad identitet

En användardefinierad identitet är ett resurs-ID för formuläret:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Du kan aktivera en eller flera användare tilldelade identiteter.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Systemtilldelad identitet

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>System-och användarspecifika identiteter

Du kan aktivera både en tilldelad identitet och en eller flera användarspecifika identiteter i en behållar grupp.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Aktivera hanterad identitet med YAML-fil

Om du vill aktivera en hanterad identitet i en behållar grupp som distribueras med en [yaml-fil](container-instances-multi-container-yaml.md), inkluderar du följande yaml.
Ange minst `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Användare tilldelad identitet

En användardefinierad identitet är ett resurs-ID för formuläret 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Du kan aktivera en eller flera användare tilldelade identiteter.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Systemtilldelad identitet

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>System-och användarspecifika identiteter

Du kan aktivera både en tilldelad identitet och en eller flera användarspecifika identiteter i en behållar grupp.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hanterade identiteter i Azure Container Instances och hur du:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller tilldelad identitet i en behållar grupp
> * Ge identitets åtkomst till ett Azure Key Vault
> * Använd den hanterade identiteten för att få åtkomst till ett nyckel valv från en behållare som körs

* Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml).

* Se ett [Azure go SDK-exempel](https://medium.com/@samkreter/c98911206328) för att använda en hanterad identitet för att få åtkomst till ett nyckel valv från Azure Container instances.
