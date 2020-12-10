---
title: Anpassad CI/CD för behållare från GitHub-åtgärder
description: Lär dig hur du använder GitHub-åtgärder för att distribuera en anpassad Linux-behållare till App Service från en CI/CD-pipeline.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: ae587b9501c9c68600ff880744d311ba966923ed
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008035"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuera en anpassad behållare för att App Service med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions) ger dig flexibiliteten att bygga ett automatiserat arbets flöde för program utveckling. Med [åtgärden Azure Web Deploy](https://github.com/Azure/webapps-deploy)kan du automatisera arbets flödet för att distribuera anpassade behållare till [App Service](overview.md) med GitHub-åtgärder.

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna i arbets flödet.

För ett arbets flöde för Azure App Service container har filen tre delar:

|Avsnitt  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. Hämta ett huvud namn för tjänsten eller en publicerings profil. <br /> 2. skapa en GitHub-hemlighet. |
|**Skapa** | 1. skapa miljön. <br /> 2. Bygg behållar avbildningen. |
|**Distribuera** | 1. distribuera behållar avbildningen. |

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join). Du måste ha kod i en GitHub-lagringsplats för att distribuera till Azure App Service. 
- Ett arbets behållar register och en Azure App Service app för behållare. I det här exemplet används Azure Container Registry. Se till att slutföra den fullständiga distributionen till Azure App Service för behållare. Till skillnad från vanliga webbappar har webbappar för behållare inte en standard-landnings sida. Publicera behållaren så att den har ett arbets exempel.
    - [Lär dig hur du skapar ett behållar Node.js program med hjälp av Docker, push-överför behållar avbildningen till ett register och distribuera avbildningen till Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution

Det rekommenderade sättet att autentisera med Azure App tjänster för GitHub-åtgärder är med en publicerings profil. Du kan också autentisera med ett huvud namn för tjänsten, men processen kräver fler steg. 

Spara dina autentiseringsuppgifter för din publicerings profil eller tjänstens huvud namn som [GitHub-hemlighet](https://docs.github.com/en/actions/reference/encrypted-secrets) för att autentisera med Azure. Du kommer att få åtkomst till hemligheten i ditt arbets flöde. 

# <a name="publish-profile"></a>[Publicera profil](#tab/publish-profile)

En publicerings profil är en autentiseringsuppgift på program nivå. Konfigurera din publicerings profil som GitHub-hemlighet. 

1. Gå till App Service i Azure Portal. 

1. På sidan **Översikt** väljer du **Hämta publicerings profil**.

    > [!NOTE]
    > Från och med oktober 2020 behöver Linux-webbapparna appens inställning som är `WEBSITE_WEBDEPLOY_USE_SCM` inställd på `true` **innan filen laddas ned**. Detta krav kommer att tas bort i framtiden. Mer information om hur du konfigurerar gemensamma inställningar för webb program finns [i Konfigurera en app service-app i Azure Portal](/azure/app-service/configure-common).  

1. Spara den hämtade filen. Du använder filens innehåll för att skapa en GitHub-hemlighet.

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/service-principal)

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

I exemplet ersätter du plats hållarna med ditt prenumerations-ID, resurs grupp namn och app-namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app. Kopiera det här JSON-objektet för senare.

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
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfånget i föregående exempel är begränsat till den särskilda App Service-appen och inte hela resurs gruppen.

---
## <a name="configure-the-github-secret-for-authentication"></a>Konfigurera GitHub-hemligheten för autentisering

# <a name="publish-profile"></a>[Publicera profil](#tab/publish-profile)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för program nivå](#generate-deployment-credentials)klistrar du in innehållet i den hämtade publicerings profil filen i fältet hemligt värde. Namnge hemligheten `AZURE_WEBAPP_PUBLISH_PROFILE` .

När du konfigurerar ditt GitHub-arbetsflöde använder du `AZURE_WEBAPP_PUBLISH_PROFILE` åtgärden för att distribuera Azure Web App. Exempel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Tjänstens huvudnamn](#tab/service-principal)

I [GitHub](https://github.com/), bläddra i din lagrings plats, välj **inställningar > hemligheter > Lägg till en ny hemlighet**.

Om du vill använda [autentiseringsuppgifter för användar nivå](#generate-deployment-credentials)klistrar du in hela JSON-utdata från Azure CLI-kommandot till fältet hemligt värde. Ge hemligheten namnet som `AZURE_CREDENTIALS` .

När du konfigurerar arbets flödes filen senare använder du hemligheten för indata `creds` från åtgärden för Azure-inloggning. Exempel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurera GitHub-hemligheter för registret

Definiera hemligheter som ska användas med Docker inloggnings åtgärden. Exemplet i det här dokumentet använder Azure Container Registry för behållar registret. 

1. Gå till din behållare i Azure Portal eller Docker och kopiera användar namn och lösen ord. Du kan hitta Azure Container Registry användar namn och lösen ord i Azure Portal under **Inställningar**  >  **åtkomst nycklar** för registret. 

2. Definiera en ny hemlighet för registrets användar namn med namnet `REGISTRY_USERNAME` . 

3. Definiera en ny hemlighet för register lösen ordet med namnet `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Bygg behållar avbildningen

I följande exempel visas en del av arbets flödet som skapar en Node.JS Docker-avbildning. Använd [Docker-inloggning](https://github.com/azure/docker-login) för att logga in på ett privat behållar register. I det här exemplet används Azure Container Registry men samma åtgärd fungerar för andra register. 


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

Du kan också använda [Docker-inloggning](https://github.com/azure/docker-login) för att logga in på flera behållar register samtidigt. Det här exemplet innehåller två nya GitHub-hemligheter för autentisering med docker.io. Exemplet förutsätter att det finns en Dockerfile på rot nivån i registret. 

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

## <a name="deploy-to-an-app-service-container"></a>Distribuera till en App Service-behållare

Om du vill distribuera avbildningen till en anpassad behållare i App Service använder du `azure/webapps-deploy@v2` åtgärden. Den här åtgärden har sju parametrar:

| **Parameter**  | **Förklaring**  |
|---------|---------|
| **App-Name** | Kunna Namnet på App Service-appen | 
| **publicera – profil** | Valfritt Gäller för Web Apps (Windows och Linux) och Web App-behållare (Linux). Scenario för flera behållare stöds inte. Publicera profil \* fil innehåll (. publishsettings) med webb distributions hemligheter | 
| **plats namn** | Valfritt Ange en befintlig plats förutom produktions platsen |
| **paketfilerna** | Valfritt Gäller endast för webbappen: sökväg till paket eller mapp. \*. zip, \* . War, \* . jar eller en mapp för distribution |
| **avbildningar** | Kunna Gäller endast för webb program behållare: Ange det fullständigt kvalificerade behållar avbildnings namnet. Till exempel "myregistry.azurecr.io/nginx:latest" eller "python: 3.7.2-alpina/". För en app med flera behållare kan du ange flera behållar avbildnings namn (flera rader separerade) |
| **konfiguration – fil** | Valfritt Gäller endast för webbappens behållare: sökvägen till Docker-Compose-filen. Måste vara en fullständigt kvalificerad sökväg eller i förhållande till standard arbets katalogen. Krävs för appar med flera behållare. |
| **Start kommando** | Valfritt Ange start kommandot. För t. ex. dotNet-körning eller dotNet-filename.dll |

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

Du hittar vår uppsättning åtgärder grupperade i olika databaser på GitHub, var och en innehåller dokumentation och exempel som hjälper dig att använda GitHub för CI/CD och distribuera dina appar till Azure.

- [Åtgärder arbets flöden att distribuera till Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-inloggning](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker-inloggning/utloggning](https://github.com/Azure/docker-login)

- [Händelser som utlöser arbetsflöden](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s-distribution](https://github.com/Azure/k8s-deploy)

- [Start arbets flöden](https://github.com/actions/starter-workflows)
