---
title: Distribuera containeravbildning från Azure Container Registry
description: Lär dig hur du distribuerar containrar Azure Container Instances genom att hämta containeravbildningar från ett Azure-containerregister.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7aba107ac58538245c16f581afa2c493f546ca01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786953"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure Container Instances från Azure Container Registry

[Azure Container Registry är](../container-registry/container-registry-intro.md) en Azure-baserad, hanterad containerregistertjänst som används för att lagra privata Docker-containeravbildningar. Den här artikeln beskriver hur du hämtar containeravbildningar som lagras i ett Azure-containerregister när du distribuerar till Azure Container Instances. Ett rekommenderat sätt att konfigurera registeråtkomst är att skapa ett Azure Active Directory för tjänstens huvudnamn och lösenord och lagra inloggningsuppgifterna i ett Azure-nyckelvalv.

## <a name="prerequisites"></a>Förutsättningar

**Azure-containerregister:** Du behöver ett Azure-containerregister – och minst en containeravbildning i registret – för att slutföra stegen i den här artikeln. Om du behöver ett register kan du [gå till Skapa ett containerregister med hjälp av Azure CLI.](../container-registry/container-registry-get-started-azure-cli.md)

**Azure CLI:** I kommandoradsexempel i den här artikeln används [Azure CLI](/cli/azure/) och formateras för Bash-gränssnittet. Du kan [installera Azure CLI lokalt](/cli/azure/install-azure-cli) eller använda [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Begränsningar

* Du kan inte autentisera för att Azure Container Registry hämta avbildningar [](container-instances-managed-identity.md) under distributionen av containergruppen med hjälp av en hanterad identitet som konfigurerats i samma containergrupp.
* Du kan inte hämta avbildningar från [Azure Container Registry](../container-registry/container-registry-vnet.md) distribueras till en Azure-Virtual Network just nu.

## <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

I ett produktionsscenario där du ger åtkomst till "huvudlösa" tjänster och program rekommenderar vi att du konfigurerar registeråtkomst med hjälp av [tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md). Med ett huvudnamn för tjänsten kan du tillhandahålla [rollbaserad åtkomstkontroll i Azure (Azure RBAC) till](../container-registry/container-registry-roles.md) dina containeravbildningar. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

Azure Container Registry ytterligare [autentiseringsalternativ.](../container-registry/container-registry-authentication.md)

I följande avsnitt skapar du ett Azure-nyckelvalv och ett huvudnamn för tjänsten och lagrar autentiseringsuppgifterna för tjänstens huvudnamn i valvet.

### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](../key-vault/general/overview.md), skapar du ett med Azure CLI och följande kommandon.

Uppdatera variabeln med namnet på en befintlig resursgrupp där du vill skapa `RES_GROUP` nyckelvalvet och med `ACR_NAME` namnet på containerregistret. Av utrymmes skäl förutsätter kommandona i den här artikeln att alla dina register-, nyckelvalvs- och containerinstanser skapas i samma resursgrupp.

 Ange ett namn för det nya nyckelvalvet i `AKV_NAME` . Valvnamnet måste vara unikt i Azure och måste vara mellan 3 och 24 alfanumeriska tecken, börja med en bokstav, sluta med en bokstav eller siffra och får inte innehålla efterföljande bindestreck.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Skapa nu ett huvudnamn för tjänsten och lagra dess autentiseringsuppgifter i nyckelvalvet.

Följande kommando använder [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] för att skapa tjänstens huvudnamn och [az keyvault secret set][az-keyvault-secret-set] för att lagra lösenordet för tjänstens huvudnamn i valvet. 

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argumentet `--role` i föregående kommando konfigurerar huvudnamnet för tjänsten med rollen *acrpull*, vilket endast ger den hämtningsåtkomst till registret. Om du vill bevilja både sändnings- och hämtningsåtkomst ändrar du argumentet `--role` till *acrpush*.

Lagra sedan *appId* för tjänstens huvudnamn i valvet, vilket är det **användarnamn** som du skickar till Azure Container Registry för autentisering.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure-nyckelvalv och lagrat två hemligheter i det:

* `$ACR_NAME-pull-usr`: ID för tjänstens huvudnamn som ska användas som containerregistrets **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenord för tjänstens huvudnamn som ska användas som containerregistrets **lösenord**.

Nu kan du referera till dessa hemligheter efter namn när du eller dina program och tjänster hämtar avbildningar från registret.

## <a name="deploy-container-with-azure-cli"></a>Distribuera containrar med Azure CLI

Nu när autentiseringsuppgifterna för tjänstens huvudnamn lagras i Azure Key Vault hemligheter kan dina program och tjänster använda dem för att komma åt ditt privata register.

Hämta först registrets inloggningsservernamn med hjälp av [kommandot az acr show.][az-acr-show] Inloggningsserverns namn är gemener och liknar `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Kör kommandot [az container create][az-container-create] för att distribuera en behållarinstans. Kommandot använder autentiseringsuppgifterna för tjänstens huvudnamn som lagras i Azure Key Vault för att autentisera till ditt containerregister, och förutsätter att du tidigare har [push-push-pushat avbildningen aci-helloworld](container-instances-quickstart.md) till registret. Uppdatera `--image` värdet om du vill använda en annan avbildning från registret.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Värdet måste vara unikt i Azure, så föregående kommando lägger till ett slumptal i `--dns-name-label` containerns DNS-namnetikett. Utdata från kommandot visar containerns fullständiga domännamn (FQDN), till exempel:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

När containern har startats kan du gå till dess FQDN i webbläsaren för att kontrollera att programmet körs.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuera med Azure Resource Manager mall

Du kan ange egenskaperna för ditt Azure-containerregister i en Azure Resource Manager mall genom att inkludera `imageRegistryCredentials` egenskapen i containergruppens definition. Du kan till exempel ange autentiseringsuppgifterna för registret direkt:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Fullständiga inställningar för containergrupper finns i Resource Manager [mallreferensen](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Mer information om hur du refererar Azure Key Vault hemligheter i en Resource Manager mall finns i Använda Azure Key Vault för att skicka säkert [parametervärde under distributionen.](../azure-resource-manager/templates/key-vault-parameter.md)

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure Portal

Om du underhåller containeravbildningar i ett Azure-containerregister kan du enkelt skapa en container i Azure Container Instances med hjälp av Azure Portal. När du använder portalen för att distribuera en containerinstans från ett containerregister måste du aktivera registrets [administratörskonto](../container-registry/container-registry-authentication.md#admin-account). Administratörskontot är utformat för att en enskild användare ska kunna komma åt registret, främst i testsyfte. 

1. I Azure Portal navigerar du till containerregistret.

1. Bekräfta att administratörskontot är aktiverat genom att välja **Åtkomstnycklar** och under **Administratörsanvändare väljer** du **Aktivera.**

1. Välj **Lagringsplatsen** och välj sedan den lagringsplats som du vill distribuera från, högerklicka på taggen för containeravbildningen som du vill distribuera och välj **Kör instans.**

    !["Kör instans" Azure Container Registry i Azure Portal][acr-runinstance-contextmenu]

1. Ange ett namn för containern och ett namn för resursgruppen. Du kan också ändra standardvärdena om du vill.

    ![Menyn Skapa för Azure Container Instances][acr-create-deeplink]

1. När distributionen är klar kan du navigera till containergruppen från meddelandefönstret för att hitta dess IP-adress och andra egenskaper.

    ![Informationsvy för Azure Container Instances containergrupp][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure Container Registry autentisering finns i [Autentisera med ett Azure-containerregister.](../container-registry/container-registry-authentication.md)

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
