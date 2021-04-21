---
title: Anpassad container CI/CD från GitHub Actions
description: Lär dig hur du använder GitHub Actions för att distribuera din anpassade Linux-container till App Service från en CI/CD-pipeline.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789437"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuera en anpassad container till App Service med GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) ger dig flexibiliteten att skapa ett automatiserat arbetsflöde för programutveckling. Med [åtgärden Azure Web Deploy](https://github.com/Azure/webapps-deploy)kan du automatisera arbetsflödet för att distribuera anpassade containrar till App Service [med](overview.md) GitHub Actions.

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen på lagringsplatsen. Den här definitionen innehåller de olika stegen och parametrarna som finns i arbetsflödet.

För ett Azure App Service containerarbetsflöde har filen tre avsnitt:

|Avsnitt  |Uppgifter  |
|---------|---------|
|**Autentisering** | 1. Hämta ett huvudnamn för tjänsten eller publicera profil. <br /> 2. Skapa en GitHub-hemlighet. |
|**Skapa** | 1. Skapa miljön. <br /> 2. Skapa containeravbildningen. |
|**Distribuera** | 1. Distribuera containeravbildningen. |

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnadsfritt.](https://github.com/join) Du måste ha kod på en GitHub-lagringsplats för att kunna distribuera till Azure App Service. 
- Ett fungerande containerregister och en Azure App Service för containrar. I det här exemplet används Azure Container Registry. Se till att slutföra den fullständiga distributionen för Azure App Service för containrar. Till skillnad från vanliga webbappar har webbappar för containrar inte någon standardlandningssida. Publicera containern för att få ett fungerande exempel.
    - [Lär dig hur du skapar ett Node.js-program med Docker, push-distribuerar containeravbildningen till ett register och sedan distribuerar avbildningen till Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Det rekommenderade sättet att autentisera med Azure App Services för GitHub Actions är med en publiceringsprofil. Du kan också autentisera med ett huvudnamn för tjänsten, men processen kräver fler steg. 

Spara dina autentiseringsuppgifter för publiceringsprofilen eller tjänstens huvudnamn som [en GitHub-hemlighet](https://docs.github.com/en/actions/reference/encrypted-secrets) för att autentisera med Azure. Du kommer åt hemligheten i arbetsflödet. 

# <a name="publish-profile"></a>[Publicera profil](#tab/publish-profile)

En publiceringsprofil är autentiseringsuppgifter på appnivå. Konfigurera din publiceringsprofil som en GitHub-hemlighet. 

1. Gå till apptjänsten i Azure Portal. 

1. På sidan **Översikt** väljer du **Hämta publiceringsprofil.**

    > [!NOTE]
    > Från och med oktober 2020 behöver Linux-webbappar appinställningen inställd `WEBSITE_WEBDEPLOY_USE_SCM` på innan filen laddas `true` **ned.** Det här kravet kommer att tas bort i framtiden. Se [Konfigurera en App Service-app i Azure Portal](./configure-common.md)för att lära dig hur du konfigurerar vanliga webbappinställningar.  

1. Spara den hämtade filen. Du använder innehållet i filen för att skapa en GitHub-hemlighet.

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/service-principal)

Du kan skapa ett [huvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för tjänsten [med kommandot az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i Azure [CLI.](/cli/azure/) Kör det här kommandot [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja **knappen Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

I exemplet ersätter du platshållarna med ditt prenumerations-ID, resursgruppens namn och appnamnet. Utdata är ett JSON-objekt med autentiseringsuppgifter för rolltilldelning som ger åtkomst till din App Service app. Kopiera det här JSON-objektet för senare tillfälle.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minsta möjliga åtkomst. Omfånget i föregående exempel är begränsat till den App Service appen och inte hela resursgruppen.

---
## <a name="configure-the-github-secret-for-authentication"></a>Konfigurera GitHub-hemligheten för autentisering

# <a name="publish-profile"></a>[Publicera profil](#tab/publish-profile)

I [GitHub](https://github.com/)bläddrar du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

Om du [vill använda autentiseringsuppgifter på](#generate-deployment-credentials)appnivå klistrar du in innehållet i den nedladdade publiceringsprofilfilen i hemlighetens värdefält. Ge hemligheten namnet `AZURE_WEBAPP_PUBLISH_PROFILE` .

När du konfigurerar ditt GitHub-arbetsflöde använder du `AZURE_WEBAPP_PUBLISH_PROFILE` åtgärden för att distribuera Azure Web App. Exempel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/service-principal)

I [GitHub](https://github.com/)bläddrar du till din lagringsplats och **väljer Inställningar > Hemligheter > Lägg till en ny hemlighet**.

Om du vill använda autentiseringsuppgifter på användarnivå klistrar du in hela JSON-utdata från Azure [CLI-kommandot](#generate-deployment-credentials)i hemlighetens värdefält. Ge hemligheten namnet som `AZURE_CREDENTIALS` .

När du konfigurerar arbetsflödesfilen senare använder du hemligheten som indata `creds` för azure-inloggningsåtgärden. Exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurera GitHub-hemligheter för ditt register

Definiera hemligheter som ska användas med åtgärden Docker-inloggning. Exemplet i det här dokumentet använder Azure Container Registry för containerregistret. 

1. Gå till containern i Azure Portal eller Docker och kopiera användarnamnet och lösenordet. Du hittar det Azure Container Registry användarnamnet och lösenordet i Azure Portal under **Inställningar**  >  **Åtkomstnycklar** för ditt register. 

2. Definiera en ny hemlighet för registrets användarnamn med namnet `REGISTRY_USERNAME` . 

3. Definiera en ny hemlighet för registerlösenordet med namnet `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Skapa containeravbildningen

I följande exempel visas en del av arbetsflödet som skapar en Node.JS Docker-avbildning. Använd [Docker-inloggning](https://github.com/azure/docker-login) för att logga in på ett privat containerregister. I det här Azure Container Registry används samma åtgärd för andra register. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Du kan också använda [Docker-inloggning](https://github.com/azure/docker-login) för att logga in på flera containerregister samtidigt. Det här exemplet innehåller två nya GitHub-hemligheter för autentisering med docker.io. Exemplet förutsätter att det finns en Dockerfile på registrets rotnivå. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Distribuera till en App Service container

Om du vill distribuera avbildningen till en anpassad container App Service använder du `azure/webapps-deploy@v2` åtgärden . Den här åtgärden har sju parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **appnamn** | (Krävs) Namnet på App Service appen | 
| **publicera profil** | (Valfritt) Gäller för Web Apps(Windows och Linux) och Web App Containers (linux). Scenario med flera containrar stöds inte. Filinnehåll för \* publiceringsprofil (.publishsettings) med Web Deploy-hemligheter | 
| **facknamn** | (Valfritt) Ange en befintlig plats förutom produktionsplatsen |
| **Paket** | (Valfritt) Gäller endast webbapp: Sökväg till paket eller mapp. \*.zip, \* .war, \* .jar eller en mapp som ska distribueras |
| **Bilder** | (Krävs) Gäller endast webappcontainrar: Ange namnet på den eller de fullständigt kvalificerade containeravbildningarna. Till exempel "myregistry.azurecr.io/nginx:latest" eller "python:3.7.2-alpine/". För en app med flera containrar kan flera containeravbildningsnamn anges (avgränsade med flera linjer) |
| **configuration-file** | (Valfritt) Gäller endast webappcontainrar: sökvägen till Docker-Compose fil. Bör vara en fullständigt kvalificerad sökväg eller relativ till standardarbetskatalogen. Krävs för appar med flera containrar. |
| **startup-command** | (Valfritt) Ange startkommandot. Till exempel dotnet run eller dotnet filename.dll |

# <a name="publish-profile"></a>[Publicera profil](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Nästa steg

Du hittar vår uppsättning åtgärder grupperade i olika lagringsplatsen på GitHub, där var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Arbetsflöden för åtgärder som ska distribueras till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Startarbetsflöden](https://github.com/actions/starter-workflows)