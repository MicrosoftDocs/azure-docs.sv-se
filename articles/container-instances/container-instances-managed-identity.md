---
title: Aktivera hanterad identitet i containergrupp
description: Lär dig hur du aktiverar en hanterad identitet i Azure Container Instances som kan autentisera med andra Azure-tjänster
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764017"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Använda hanterade identiteter med Azure Container Instances

Använd [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att köra kod i Azure Container Instances som interagerar med andra Azure-tjänster – utan att underhålla några hemligheter eller autentiseringsuppgifter i koden. Funktionen tillhandahåller en Azure Container Instances med en automatiskt hanterad identitet i Azure Active Directory.

I den här artikeln får du lära dig mer om hanterade identiteter i Azure Container Instances och:

> [!div class="checklist"]
> * Aktivera en användar-tilldelad eller system tilldelad identitet i en containergrupp
> * Bevilja identiteten åtkomst till ett Azure-nyckelvalv
> * Använda den hanterade identiteten för att få åtkomst till ett nyckelvalv från en container som körs

Anpassa exemplen för att aktivera och använda identiteter i Azure Container Instances få åtkomst till andra Azure-tjänster. De här exemplen är interaktiva. I praktiken skulle dock dina containeravbildningar köra kod för att få åtkomst till Azure-tjänster.
 
> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA). För närvarande stöds hanterade identiteter på Azure Container Instances endast med Linux-containrar och ännu inte med Windows-containrar.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

Använd en hanterad identitet i en container som körs för att autentisera till alla tjänster som stöder [Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) utan att hantera autentiseringsuppgifter i din containerkod. För tjänster som inte stöder AD-autentisering kan du lagra hemligheter i ett Azure-nyckelvalv och använda den hanterade identiteten för att få åtkomst till nyckelvalvet för att hämta autentiseringsuppgifter. Mer information om hur du använder en hanterad identitet finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

 När du skapar en containergrupp aktiverar du en eller flera hanterade identiteter genom att ange egenskapen [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Du kan också aktivera eller uppdatera hanterade identiteter när en containergrupp körs– endera åtgärden gör att containergruppen startas om. Om du vill ange identiteter i en ny eller befintlig containergrupp använder du Azure CLI, en Resource Manager mall, en YAML-fil eller något annat Azure-verktyg. 

Azure Container Instances har stöd för båda typerna av hanterade Azure-identiteter: användar-tilldelade och system-tilldelade. I en containergrupp kan du aktivera en system tilldelad identitet, en eller flera användar tilldelade identiteter eller båda typerna av identiteter. Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [översikten](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Använda en hanterad identitet

Om du vill använda en hanterad identitet måste identiteten beviljas åtkomst till en eller flera Azure-tjänstresurser (till exempel en webbapp, ett nyckelvalv eller ett lagringskonto) i prenumerationen. Att använda en hanterad identitet i en container som körs liknar att använda en identitet på en virtuell Azure-dator. Se vägledningen för virtuella datorer för att använda [en token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [Azure PowerShell, Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)eller [Azure-SDK:erna](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Begränsningar

* För närvarande kan du inte använda en hanterad identitet i en containergrupp som distribuerats till ett virtuellt nätverk.
* Du kan inte använda en hanterad identitet för att hämta en avbildning Azure Container Registry när du skapar en containergrupp. Identiteten är bara tillgänglig i en container som körs.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure-nyckelvalv

I exemplen i den här artikeln används en hanterad identitet i Azure Container Instances åtkomst till en Azure Key Vault-hemlighet. 

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* med följande [az group create](/cli/azure/group#az_group_create)-kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az keyvault create](/cli/azure/keyvault#az_keyvault_create) för att skapa ett nyckelvalv. Se till att ange ett unikt nyckelvalvsnamn. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Lagra en exempelhemlighet i nyckelvalvet med kommandot [az keyvault secret set:](/cli/azure/keyvault/secret#az_keyvault_secret_set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Fortsätt med följande exempel för att få åtkomst till nyckelvalvet med hjälp av antingen en användar tilldelad eller system tilldelad hanterad identitet i Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exempel 1: Använda en användar tilldelad identitet för åtkomst till Azure Key Vault

### <a name="create-an-identity"></a>Skapa en identitet

Skapa först en identitet i din prenumeration med hjälp av [kommandot az identity](/cli/azure/identity#az_identity_create) create. Du kan använda samma resursgrupp som används för att skapa nyckelvalvet eller använda ett annat.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Om du vill använda identiteten i följande steg använder du kommandot [az identity show](/cli/azure/identity#az_identity_show) för att lagra identitetens id för tjänstens huvudnamn och resurs-ID i variabler.

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Bevilja användar tilldelad identitet åtkomst till nyckelvalvet

Kör följande az [keyvault set-policy-kommando](/cli/azure/keyvault) för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan den användar tilldelade identiteten hämta hemligheter från nyckelvalvet:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Aktivera användar tilldelad identitet i en containergrupp

Kör följande az [container create-kommando](/cli/azure/container#az_container_create) för att skapa en containerinstans baserat på Microsofts `azure-cli` avbildning. Det här exemplet innehåller en grupp med en container som du kan använda interaktivt för att köra Azure CLI för att få åtkomst till andra Azure-tjänster. I det här avsnittet används endast det grundläggande operativsystemet. Ett exempel på hur du använder Azure CLI i containern finns i [Enable system-assigned identity on a container group](#enable-system-assigned-identity-on-a-container-group). 

Parametern `--assign-identity` skickar den användar tilldelade hanterade identiteten till gruppen. Det långvariga kommandot håller containern igång. I det här exemplet används samma resursgrupp som användes för att skapa nyckelvalvet, men du kan ange ett annat.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera dess status med [kommandot az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Avsnittet `identity` i utdata ser ut ungefär så här och visar att identiteten har angetts i containergruppen. Under `principalID` är `userAssignedIdentities` tjänstens huvudnamn för den identitet som du skapade i Azure Active Directory:

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

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Använda användar tilldelad identitet för att hämta en hemlighet från nyckelvalvet

Nu kan du använda den hanterade identiteten i containerinstansen som körs för att få åtkomst till nyckelvalvet. Starta först ett bash-gränssnitt i containern:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i containern. Kör följande kommando för att få en åtkomsttoken Azure Active Directory använda för att autentisera till nyckelvalvet:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Utdata:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Om du vill lagra åtkomsttoken i en variabel som ska användas i efterföljande kommandon för autentisering kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Använd nu åtkomsttoken för att autentisera till nyckelvalvet och läsa en hemlighet. Ersätt namnet på nyckelvalvet i URL:en (*https: \/ /mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret liknar följande och visar hemligheten. I koden parsar du dessa utdata för att hämta hemligheten. Använd sedan hemligheten i en efterföljande åtgärd för att få åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exempel 2: Använda en system tilldelad identitet för åtkomst till Azure Key Vault

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Aktivera system tilldelad identitet i en containergrupp

Kör följande az [container create-kommando](/cli/azure/container#az_container_create) för att skapa en containerinstans baserat på Microsofts `azure-cli` avbildning. Det här exemplet innehåller en grupp med en container som du kan använda interaktivt för att köra Azure CLI för att få åtkomst till andra Azure-tjänster. 

Parametern `--assign-identity` utan ytterligare värde aktiverar en system tilldelad hanterad identitet i gruppen. Identiteten är begränsad till containergruppens resursgrupp. Det långvariga kommandot håller containern igång. I det här exemplet används samma resursgrupp som användes för att skapa nyckelvalvet, som finns i identitetens omfång.

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

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera dess status med [kommandot az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Avsnittet `identity` i utdata ser ut ungefär så här och visar att en system tilldelad identitet skapas i Azure Active Directory:

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

Ange en variabel till värdet för `principalId` (id:t för tjänstens huvudnamn) för identiteten, som ska användas i senare steg.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Bevilja containergrupp åtkomst till nyckelvalvet

Kör följande az [keyvault set-policy-kommando](/cli/azure/keyvault) för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan den system hanterade identiteten hämta hemligheter från nyckelvalvet:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Använda en containergruppsidentitet för att hämta en hemlighet från nyckelvalvet

Nu kan du använda den hanterade identiteten för att komma åt nyckelvalvet i containerinstansen som körs. Starta först ett bash-gränssnitt i containern:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i containern. Logga först in på Azure CLI med den hanterade identiteten:

```bash
az login --identity
```

Hämta hemligheten från nyckelvalvet från containern som körs:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Värdet för hemligheten hämtas:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivera hanterad identitet med Resource Manager mall

Om du vill aktivera en hanterad identitet i en [containergrupp med Resource Manager en mall](container-instances-multi-container-group.md)anger du egenskapen för objektet med ett `identity` `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` -objekt. Följande kodfragment visar egenskapen `identity` som konfigurerats för olika scenarier. Se Resource Manager [mallreferensen](/azure/templates/microsoft.containerinstance/containergroups). Ange minst `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Användar tilldelad identitet

En användar tilldelad identitet är ett resurs-ID i formuläret:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Du kan aktivera en eller flera användar tilldelade identiteter.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>System tilldelad identitet

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>System- och användar-tilldelade identiteter

I en containergrupp kan du aktivera både en system tilldelad identitet och en eller flera användar tilldelade identiteter.

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

Om du vill aktivera en hanterad identitet i en containergrupp som distribueras med en [YAML-fil](container-instances-multi-container-yaml.md)inkluderar du följande YAML.
Ange minst `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Användar tilldelad identitet

En användar tilldelad identitet är ett resurs-ID för formuläret 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Du kan aktivera en eller flera användar tilldelade identiteter.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>System tilldelad identitet

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>System- och användar tilldelade identiteter

I en containergrupp kan du aktivera både en system tilldelad identitet och en eller flera användar tilldelade identiteter.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hanterade identiteter i Azure Container Instances och hur du:

> [!div class="checklist"]
> * Aktivera en användar tilldelad eller system tilldelad identitet i en containergrupp
> * Bevilja identiteten åtkomst till ett Azure-nyckelvalv
> * Använda den hanterade identiteten för att få åtkomst till ett nyckelvalv från en container som körs

* Läs mer om [hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/index.yml)

* Se ett [Azure Go SDK-exempel på](https://medium.com/@samkreter/c98911206328) hur du använder en hanterad identitet för att få åtkomst till ett nyckelvalv Azure Container Instances.
