---
title: Distribuera behållar instansen per GitHub-åtgärd
description: Konfigurera en GitHub-åtgärd som automatiserar stegen för att bygga, skicka och distribuera en behållar avbildning till Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 270aa69e74045a48a3567ca74b722ca48a030654
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591641"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurera en GitHub-åtgärd för att skapa en containerinstans

[GitHub-åtgärder](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) är en uppsättning funktioner i GitHub för att automatisera dina arbets flöden för program utveckling på samma plats som du lagrar kod och samarbetar om pull-begäranden och-problem.

Använd åtgärden [distribuera för att Azure Container instances](https://github.com/azure/aci-deploy) GitHub för att automatisera distributionen av en enskild behållare till Azure Container instances. Med åtgärden kan du ange egenskaper för en behållar instans som liknar dem i kommandot [AZ container Create][az-container-create] .

Den här artikeln visar hur du konfigurerar ett arbets flöde i en GitHub-lagrings platsen som utför följande åtgärder:

* Bygg en avbildning från en Dockerfile
* Push-överför avbildningen till ett Azure Container Registry
* Distribuera behållar avbildningen till en Azure Container instance

Den här artikeln visar två sätt att konfigurera arbets flödet:

* [Konfigurera GitHub-arbetsflöde](#configure-github-workflow) – skapa ett arbets flöde i en GitHub-lagrings platsen med hjälp av åtgärden distribuera till Azure Container instances och andra åtgärder.  
* [Använd CLI-tillägg](#use-deploy-to-azure-extension) – Använd `az container app up` kommandot i tillägget [distribuera till Azure](https://github.com/Azure/deploy-to-azure-cli-extension) i Azure CLI. Med det här kommandot effektiviseras skapandet av GitHub-arbetsflöde och distributions steg.

> [!IMPORTANT]
> GitHub-åtgärden för Azure Container Instances är för närvarande en för hands version. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Krav

* **GitHub-konto** – skapa ett konto på https://github.com om du inte redan har ett.
* **Azure CLI** – du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra Azure CLI-stegen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].
* **Azure Container Registry** – om du inte har ett kan du skapa ett Azure Container Registry på Basic-nivån med hjälp av [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)eller andra metoder. Anteckna resurs gruppen som används för distributionen, som används för GitHub-arbetsflödet.

## <a name="set-up-repo"></a>Konfigurera lagrings platsen

* I exemplen i den här artikeln använder du GitHub för att förgrena följande lagrings plats: https://github.com/Azure-Samples/acr-build-helloworld-node

  Den här lagrings platsen innehåller en Dockerfile och källfiler för att skapa en behållar avbildning av en liten webbapp.

  ![Skärmbild av knappen Förgrening (markerad) i GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Se till att åtgärder är aktiverade för din lagrings plats. Navigera till din förgrenade lagrings plats och välj **Inställningar**  >  **åtgärder**. I **Åtgärds behörigheter** kontrollerar du att **Aktivera lokala och tredje parts åtgärder för den här lagrings platsen** är markerade.

## <a name="configure-github-workflow"></a>Konfigurera GitHub-arbetsflöde

### <a name="create-service-principal-for-azure-authentication"></a>Skapa tjänstens huvud namn för Azure-autentisering

I GitHub-arbetsflödet måste du ange Azure-autentiseringsuppgifter för att autentisera till Azure CLI. I följande exempel skapas ett huvud namn för tjänsten med deltagar rollen som är begränsad till resurs gruppen för behållar registret.

Börja med att hämta resurs-ID för resurs gruppen. Ersätt namnet på gruppen i följande [AZ Group show][az-group-show] -kommando:

```azurecli
$groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Använd [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] för att skapa tjänstens huvud namn:

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

Spara JSON-utdata eftersom det används i ett senare steg. Anteckna också `clientId` , som du måste uppdatera tjänstens huvud namn i nästa avsnitt.

### <a name="update-service-principal-for-registry-authentication"></a>Uppdatera tjänstens huvud namn för autentisering av registret

Uppdatera autentiseringsuppgifterna för Azure-tjänstens huvud namn för att tillåta push-och pull-åtkomst till behållar registret. Det här steget gör att GitHub-arbetsflödet kan använda tjänstens huvud namn för att [autentisera med ditt behållar register](../container-registry/container-registry-auth-service-principal.md) och för att skicka och ta emot en Docker-avbildning. 

Hämta resurs-ID för behållar registret. Ersätt namnet på ditt register i följande [AZ ACR show][az-acr-show] -kommando:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Använd [AZ roll tilldelning skapa][az-role-assignment-create] för att tilldela AcrPush-rollen, som ger push-och pull-åtkomst till registret. Ersätt klient-ID: t för tjänstens huvud namn:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Spara autentiseringsuppgifter till GitHub lagrings platsen

1. I GitHub-användargränssnittet navigerar du till din förgrenade lagrings plats och väljer **Inställningar**  >  **hemligheter**. 

1. Välj **Lägg till en ny hemlighet** för att lägga till följande hemligheter:

|Hemlighet  |Värde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Hela JSON-utdata från steget för att skapa tjänst objekt |
|`REGISTRY_LOGIN_SERVER`   | Inloggnings Server namnet för registret (alla gemener). Exempel: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`Från JSON-utdata från det att tjänstens huvud namn skapas       |
|`REGISTRY_PASSWORD`     |  `clientSecret`Från JSON-utdata från det att tjänstens huvud namn skapas |
| `RESOURCE_GROUP` | Namnet på den resurs grupp som du använde för att omfånget av tjänstens huvud namn |

### <a name="create-workflow-file"></a>Skapa arbets flödes fil

1. I GitHub-användargränssnittet väljer du **åtgärder**  >  **nytt arbets flöde**.
1. Välj **Konfigurera ett arbets flöde själv**.
1. I **Redigera ny fil** klistrar du in följande yaml-innehåll för att skriva över exempel koden. Godkänn standard namnet `main.yml` eller ange ett fil namn som du väljer.
1. Välj **Start commit (om** du vill, ange korta och utökade beskrivningar av ditt genomförande) och välj sedan **genomför ny fil**.

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

### <a name="validate-workflow"></a>Verifiera arbets flöde

När du har bekräftat arbets flödes filen utlöses arbets flödet. Gå till **Åtgärds**  >  **arbets flöden** för att granska arbets flödets förlopp. 

![Visa arbets flödes förlopp](./media/container-instances-github-action/github-action-progress.png)

Se [hantera en arbets flödes körning](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) för information om hur du visar status och resultat för varje steg i arbets flödet. Om arbets flödet inte slutförs, se [Visa loggar för att diagnostisera fel](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures).

När arbets flödet har slutförts hämtar du information om behållar instansen med namnet *ACI-fråga* genom att köra kommandot [AZ container show][az-container-show] . Ersätt namnet på din resurs grupp: 

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

När instansen har allokerats navigerar du till behållarens FQDN i webbläsaren för att visa den webbapp som körs.

![Köra webb program i webbläsare](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Använd distribuera till Azure-tillägg

Du kan också använda [tillägget distribuera till Azure](https://github.com/Azure/deploy-to-azure-cli-extension) i Azure CLI för att konfigurera arbets flödet. `az container app up`Kommandot i tillägget använder indataparametrar från dig för att konfigurera ett arbets flöde för distribution till Azure Container instances. 

Arbets flödet som skapats av Azure CLI liknar det arbets flöde som du kan [Skapa manuellt med GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Ytterligare krav

Förutom [kraven](#prerequisites) och [lagrings platsen-installationen](#set-up-repo) för det här scenariot måste du installera  **tillägget distribuera till Azure** för Azure CLI.

Kör kommandot [AZ Extension Add][az-extension-add] för att installera tillägget:

```azurecli
az extension add \
  --name deploy-to-azure
```

Information om hur du hittar, installerar och hanterar tillägg finns i [använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Kör `az container app up`

För att köra kommandot [AZ container app up][az-container-app-up] anger du minst:

* Namnet på ditt Azure Container Registry, till exempel för *registret*
* URL: en till din GitHub-lagrings platsen, till exempel `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Exempel-kommando:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Kommando förlopp

* När du uppmanas till det anger du dina GitHub-autentiseringsuppgifter eller anger en GitHub-Pat ( [personal Access token](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) ) som har *lagrings platsen* och *användar* omfång för att autentisera med ditt GitHub-konto. Om du anger GitHub-autentiseringsuppgifter skapar kommandot en PAT åt dig. Följ ytterligare prompter för att konfigurera arbets flödet.

* Kommandot skapar lagrings platsen hemligheter för arbets flödet:

  * Autentiseringsuppgifter för tjänstens huvud namn för Azure CLI
  * Autentiseringsuppgifter för åtkomst till Azure Container Registry

* När kommandot utför arbets flödes filen till din lagrings platsen utlöses arbets flödet. 

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

Om du vill visa arbets flödes status och resultat för varje steg i GitHub-ANVÄNDARGRÄNSSNITTET, se [hantera en arbets flödes körning](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

### <a name="validate-workflow"></a>Verifiera arbets flöde

Arbets flödet distribuerar en Azure Container instance med bas namnet för din GitHub-lagrings platsen, i det här fallet *ACR-build-HelloWorld-Node*. När arbets flödet har slutförts hämtar du information om behållar instansen med namnet *ACR-build-HelloWorld-Node* genom att köra kommandot [AZ container show][az-container-show] . Ersätt namnet på din resurs grupp: 

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

När instansen har allokerats navigerar du till behållarens FQDN i webbläsaren för att visa den webbapp som körs.

## <a name="clean-up-resources"></a>Rensa resurser

Stoppa behållarinstansen med kommandot [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Om du vill ta bort resurs gruppen och alla resurser i den kör du kommandot [AZ Group Delete][az-group-delete] :

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

Bläddra i [GitHub Marketplace](https://github.com/marketplace?type=actions) efter fler åtgärder för att automatisera ditt utvecklings arbets flöde


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
