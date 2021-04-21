---
title: Distribuera containerinstansen med GitHub-åtgärd
description: Konfigurera en GitHub-åtgärd som automatiserar steg för att skapa, push-distribuera och distribuera en containeravbildning till Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: e6a4d9ecff292d79f132f933c36b0030e04f4efa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771307"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurera en GitHub-åtgärd för att skapa en containerinstans

[GitHub Actions](https://docs.github.com/en/actions) är en uppsättning funktioner i GitHub som automatiserar arbetsflöden för programutveckling på samma plats som du lagrar kod och samarbetar kring pull-begäranden och problem.

Använd åtgärden [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub (Distribuera till gitHub) för att automatisera distributionen av en enskild container till Azure Container Instances. Med åtgärden kan du ange egenskaper för en containerinstans som liknar dem i [kommandot az container create.][az-container-create]

Den här artikeln visar hur du ställer in ett arbetsflöde på en GitHub-lagringsplatsen som utför följande åtgärder:

* Skapa en avbildning från en Dockerfile
* Push-skicka avbildningen till ett Azure-containerregister
* Distribuera containeravbildningen till en Azure-containerinstans

Den här artikeln visar två sätt att konfigurera arbetsflödet:

* [Konfigurera GitHub-arbetsflöde](#configure-github-workflow) – Skapa ett arbetsflöde på en GitHub-lagringsplatsen med åtgärden Azure Container Instances distribuera till lagringsplatsen och andra åtgärder.  
* [Använd CLI-tillägg](#use-deploy-to-azure-extension) – `az container app up` Använd kommandot i tillägget Distribuera till [Azure](https://github.com/Azure/deploy-to-azure-cli-extension) i Azure CLI. Det här kommandot effektiviserar skapandet av GitHub-arbetsflödet och distributionsstegen.

> [!IMPORTANT]
> GitHub-åtgärden för Azure Container Instances är för närvarande i förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

* **GitHub-konto** – Skapa ett https://github.com konto på om du inte redan har ett.
* **Azure CLI** – Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra Azure CLI-stegen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].
* **Azure-containerregister** – Om du inte har ett kan du skapa ett Azure-containerregister på Basic-nivån med hjälp av [Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [Azure Portal](../container-registry/container-registry-get-started-portal.md)eller andra metoder. Anteckna resursgruppen som används för distributionen, som används för GitHub-arbetsflödet.

## <a name="set-up-repo"></a>Konfigurera lagringsplatsen

* I exemplen i den här artikeln använder du GitHub för att förfördeka följande lagringsplats: https://github.com/Azure-Samples/acr-build-helloworld-node

  Den här lagringsplatsen innehåller en Dockerfile och källfiler för att skapa en containeravbildning av en liten webbapp.

  ![Skärmbild av knappen Förgrening (markerad) i GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Se till att Åtgärder är aktiverat för din lagringsplats. Gå till den förked lagringsplatsen och välj **Inställningar**  >  **Åtgärder.** I **Åtgärdsbehörigheter** ser du till **att Aktivera lokala åtgärder och åtgärder från tredje part för den här lagringsplatsen** har valts.

## <a name="configure-github-workflow"></a>Konfigurera GitHub-arbetsflöde

### <a name="create-service-principal-for-azure-authentication"></a>Skapa tjänstens huvudnamn för Azure-autentisering

I GitHub-arbetsflödet måste du ange azure-autentiseringsuppgifter för att autentisera till Azure CLI. I följande exempel skapas ett huvudnamn för tjänsten med rollen Deltagare begränsad till resursgruppen för containerregistret.

Hämta först resurs-ID:t för resursgruppen. Ersätt namnet på din grupp i följande [az group show-kommando:][az-group-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Använd [az ad sp create-for-rbac för][az-ad-sp-create-for-rbac] att skapa tjänstens huvudnamn:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Utdata liknar följande:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Spara JSON-utdata eftersom de används i ett senare steg. Anteckna också , som `clientId` du behöver uppdatera tjänstens huvudnamn i nästa avsnitt.

### <a name="update-service-principal-for-registry-authentication"></a>Uppdatera tjänstens huvudnamn för registerautentisering

Uppdatera autentiseringsuppgifterna för Azure-tjänstens huvudnamn för att tillåta push- och pull-åtkomst till containerregistret. Det här steget gör att GitHub-arbetsflödet kan använda tjänstens huvudnamn för att autentisera med [ditt containerregister](../container-registry/container-registry-auth-service-principal.md) och för att push-pusha och hämta en Docker-avbildning. 

Hämta resurs-ID:t för containerregistret. Ersätt namnet på registret med följande [az acr show-kommando:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Använd [az role assignment create][az-role-assignment-create] för att tilldela rollen AcrPush, som ger push- och pull-åtkomst till registret. Ersätt klient-ID:t för tjänstens huvudnamn:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Spara autentiseringsuppgifter på GitHub-lagringsplatsen

1. I GitHub-användargränssnittet navigerar du till din förked lagringsplats och väljer **Inställningar**  >  **Hemligheter.** 

1. Välj **Lägg till en ny hemlighet** för att lägga till följande hemligheter:

|Hemlighet  |Värde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Hela JSON-utdata från steget för att skapa tjänstens huvudnamn |
|`REGISTRY_LOGIN_SERVER`   | Inloggningsservernamnet för registret (endast gemener). Exempel: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  från `clientId` JSON-utdata från skapandet av tjänstens huvudnamn       |
|`REGISTRY_PASSWORD`     |  från `clientSecret` JSON-utdata från skapandet av tjänstens huvudnamn |
| `RESOURCE_GROUP` | Namnet på den resursgrupp som du använde för att begränsa omfånget för tjänstens huvudnamn |

### <a name="create-workflow-file"></a>Skapa arbetsflödesfil

1. I GitHub-användargränssnittet väljer du **Åtgärder**  >  **Nytt arbetsflöde.**
1. Välj **Konfigurera ett arbetsflöde själv.**
1. I **Redigera ny fil** klistrar du in följande YAML-innehåll för att skriva över exempelkoden. Acceptera standardfilnamnet `main.yml` eller ange ett filnamn som du väljer.
1. Välj **Starta genomförande,** ange korta och utökade beskrivningar av din commit (Starta genomförande) och välj **Commit new file (Genomför ny fil).**

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Verifiera arbetsflöde

När du har arkiverat arbetsflödesfilen utlöses arbetsflödet. Om du vill granska arbetsflödets förlopp går du **till**  >  **Åtgärdsarbetsflöden.** 

![Visa arbetsflödesförloppet](./media/container-instances-github-action/github-action-progress.png)

Se [Visa arbetsflödeskörningshistorik](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) för information om hur du visar status och resultat för varje steg i arbetsflödet. Om arbetsflödet inte slutförs läser du Visa loggar [för att diagnostisera fel.](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)

När arbetsflödet har slutförts hämtar du information om containerinstansen med namnet *aci-sampleapp genom* att köra [kommandot az container show.][az-container-show] Ersätt namnet på resursgruppen: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Utdata liknar följande:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

När instansen har etablerats går du till containerns FQDN i webbläsaren för att visa webbappen som körs.

![Köra webbappen i webbläsaren](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Använda deploy to Azure extension (Distribuera till Azure-tillägg)

Du kan också använda tillägget [Distribuera till Azure i](https://github.com/Azure/deploy-to-azure-cli-extension) Azure CLI för att konfigurera arbetsflödet. Kommandot `az container app up` i tillägget tar indataparametrar från dig för att konfigurera ett arbetsflöde för distribution till Azure Container Instances. 

Arbetsflödet som skapas av Azure CLI liknar det arbetsflöde som du kan [skapa manuellt med Hjälp av GitHub.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>Ytterligare krav

Förutom kraven och [lagringsplatsens](#prerequisites) [konfiguration](#set-up-repo) för det här scenariot måste du installera tillägget  **Distribuera till Azure** för Azure CLI.

Kör kommandot [az extension add][az-extension-add] för att installera tillägget:

```azurecli
az extension add \
  --name deploy-to-azure
```

Information om hur du söker efter, installerar och hanterar tillägg finns i [Använda tillägg med Azure CLI.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>Kör `az container app up`

Om du vill [köra kommandot az container app up][az-container-app-up] anger du minst:

* Namnet på ditt Azure-containerregister, till exempel *myregistry*
* URL:en till din GitHub-lagringsplatsen, till exempel `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Exempel-kommando:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Kommandoförloppet

* När du uppmanas till det anger du dina autentiseringsuppgifter för  GitHub  eller anger en personlig åtkomsttoken för [GitHub](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) som har lagringsplatsen och användaromfång för autentisering med ditt GitHub-konto. Om du anger autentiseringsuppgifter för GitHub skapar kommandot en PAT åt dig. Följ ytterligare uppmaningar för att konfigurera arbetsflödet.

* Kommandot skapar lagringsplatsens hemligheter för arbetsflödet:

  * Autentiseringsuppgifter för tjänstens huvudnamn för Azure CLI
  * Autentiseringsuppgifter för åtkomst till Azure-containerregistret

* När kommandot har arkiverat arbetsflödesfilen på din lagringsplatsen utlöses arbetsflödet. 

Utdata liknar följande:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Information om hur du visar arbetsflödesstatus och resultat för varje steg i GitHub-användargränssnittet finns i [Visa körningshistorik för arbetsflöden.](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)

### <a name="validate-workflow"></a>Verifiera arbetsflöde

Arbetsflödet distribuerar en Azure-containerinstans med basnamnet för din GitHub-lagringsplatsen, i det här fallet *acr-build-helloworld-node*. När arbetsflödet har slutförts hämtar du information om containerinstansen med namnet *acr-build-helloworld-node* genom att köra [kommandot az container show.][az-container-show] Ersätt namnet på resursgruppen: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Utdata liknar följande:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

När instansen har etablerats går du till containerns FQDN i webbläsaren för att visa webbappen som körs.

## <a name="clean-up-resources"></a>Rensa resurser

Stoppa behållarinstansen med kommandot [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Om du vill ta bort resursgruppen och alla resurser i den kör du [kommandot az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

Bläddra till [GitHub Marketplace om](https://github.com/marketplace?type=actions) du vill ha fler åtgärder för att automatisera ditt utvecklingsarbetsflöde


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
