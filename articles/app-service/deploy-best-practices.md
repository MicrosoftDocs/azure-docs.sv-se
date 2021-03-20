---
title: Bästa metoder för distribution
description: Lär dig mer om viktiga mekanismer för att distribuera till Azure App Service. Hitta språkspecifika rekommendationer och andra varningar.
keywords: Azure App Service, webbapp, distribuera, distribution, pipeliner, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 3b49177eb592906e3bf84d359699b354f8c87c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185673"
---
# <a name="deployment-best-practices"></a>Metod tips för distribution

Alla utvecklings team har unika krav som kan göra det svårt att implementera en effektiv distributions pipeline på alla moln tjänster. Den här artikeln beskriver de tre huvud komponenterna i distribution till App Service: distributions källor, bygga pipelines och distributions metoder. Den här artikeln beskriver också några metod tips och tips för specifika språk stackar.

## <a name="deployment-components"></a>Distributions komponenter

### <a name="deployment-source"></a>Distributions källa

En distributions källa är platsen för program koden. För produktions program är distributions källan vanligt vis en lagrings plats som är värd för program vara för versions kontroll, till exempel [GitHub, BitBucket eller Azure databaser](deploy-continuous-deployment.md). I utvecklings-och test scenarier kan distributions källan vara [ett projekt på din lokala dator](deploy-local-git.md). App Service stöder även [OneDrive-och Dropbox-mappar](deploy-content-sync.md) som distributions källor. Även om Cloud Folders kan göra det enkelt att komma igång med App Service, rekommenderar vi vanligt vis inte att använda den här källan för produktions program på företags nivå. 

### <a name="build-pipeline"></a>Bygg-pipeline

När du bestämmer dig för en distributions källa är nästa steg att välja en pipeline för bygge. En build-pipeline läser din käll kod från distributions källan och kör en serie steg (till exempel kompilera kod, minifying HTML och Java Script, köra tester och paket komponenter) för att hämta programmet i ett körbara-tillstånd. Specifika kommandon som körs av bygg pipelinen beror på din språks tack. Dessa åtgärder kan utföras på en build-Server, till exempel Azure-pipelines eller som körs lokalt.

### <a name="deployment-mechanism"></a>Distributions mekanism

Distributions mekanismen är den åtgärd som används för att publicera det inbyggda programmet i */Home/site/wwwroot* -katalogen för din webbapp. */Wwwroot* -katalogen är en monterad lagrings plats som delas av alla instanser av din webbapp. När mekanismen för distribution placerar ditt program i den här katalogen, får dina instanser ett meddelande om att synkronisera de nya filerna. App Service stöder följande distributions metoder:

- Kudu-slutpunkter: [kudu](https://github.com/projectkudu/kudu/wiki) är utvecklar produktivitets verktyget öppen källkod som körs som en separat process i Windows app service och som en andra behållare i Linux app service. Kudu hanterar kontinuerliga distributioner och tillhandahåller HTTP-slutpunkter för distribution, till exempel zipdeploy.
- FTP och WebDeploy: om du använder dina [webbplats-eller användarautentiseringsuppgifter](deploy-configure-credentials.md)kan du ladda upp filer [via FTP](deploy-ftp.md) eller WebDeploy. Dessa mekanismer går inte igenom kudu.  

Distributions verktyg som Azure-pipeline, Jenkins och redigerings-plugin-program använder en av dessa distributions mekanismer.

## <a name="use-deployment-slots"></a>Använd distributions platser

När det är möjligt ska du använda [distributions platser](deploy-staging-slots.md) när du distribuerar en ny produktions version. När du använder en standard-App Service-plannivå eller bättre kan du distribuera din app till en mellanlagringsplats, validera dina ändringar och utföra rök test. När du är klar kan du växla mellan mellanlagrings-och produktions platser. Växlings åtgärden ökar de arbets instanser som krävs för att matcha din produktions skala, vilket eliminerar drift stopp.

### <a name="continuously-deploy-code"></a>Distribuera kod kontinuerligt

Om ditt projekt har utsetts till grenar för testning, frågor och mellanlagring, bör var och en av dessa grenar ständigt distribueras till en mellanlagringsplats. (Detta kallas [Gitflow-design](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) På så sätt kan dina intressenter enkelt utvärdera och testa den distribuerade grenen. 

Kontinuerlig distribution ska aldrig aktive ras för produktions platsen. I stället bör produktions grenen (ofta huvud) distribueras till en icke-produktions plats. När du är redo att publicera bas grenen byter du till produktions platsen. Att växla till produktion – i stället för att distribuera till produktion – förhindrar drift stopp och gör att du kan återställa ändringarna genom att växla igen. 

![Diagram som visar flödet mellan dev-, mellanlagrings-och huvud grenarna och de platser som de distribueras till.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Distribuera behållare kontinuerligt

För anpassade behållare från Docker eller andra behållar register, distribuerar du avbildningen till en mellanlagringsplats och byter till produktion för att förhindra stillestånds tid. Automation är mer komplex än kod distribution eftersom du måste push-överföra avbildningen till ett behållar register och uppdatera avbildnings tag gen på webapp.

För varje gren som du vill distribuera till en plats ställer du in Automation för att göra följande vid varje incheckning till grenen.

1. **Bygg och tagga avbildningen**. Som en del av pipeline för bygge, tagga avbildningen med ID för git-incheckning, tidsstämpel eller annan identifierbar information. Vi rekommenderar att du inte använder standard koden "senaste". Annars är det svårt att spåra vilken kod som för närvarande har distribuerats, vilket gör att fel sökningen är mycket svårare.
1. **Skicka den taggade bilden**. När avbildningen har skapats och taggats, skickar pipelinen avbildningen till vårt behållar register. I nästa steg hämtar distributions platsen den taggade avbildningen från behållar registret.
1. **Uppdatera distributions platsen med den nya avbildnings tag gen**. När den här egenskapen uppdateras, startas platsen om automatiskt och den nya behållar avbildningen tas emot.

![Visualisering av plats användning](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Det finns exempel nedan för vanliga automatiserings ramverk.

### <a name="use-azure-devops"></a>Använd Azure-DevOps

App Service har [inbyggd kontinuerlig leverans](deploy-continuous-deployment.md) för behållare via distributions Center. Navigera till din app i [Azure Portal](https://portal.azure.com/) och välj **Deployment Center** under **distribution**. Följ anvisningarna för att välja din databas och gren. Detta konfigurerar en pipeline för DevOps build och release så att den automatiskt skapar, taggar och distribuerar din behållare när nya incheckningar skickas till den valda grenen.

### <a name="use-github-actions"></a>Använda GitHub-åtgärder

Du kan också automatisera behållar distributionen [med GitHub-åtgärder](deploy-container-github-action.md).  Arbets flödes filen nedan skapar och taggar behållaren med commit ID, push-överför den till ett behållar register och uppdaterar den angivna plats platsen med den nya avbildnings tag gen.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Använd andra Automation-leverantörer

Stegen ovan gäller för andra automatiserings verktyg som CircleCI eller Travis CI. Du måste dock använda Azure CLI för att uppdatera distributions facken med nya avbildnings etiketter i det sista steget. Om du vill använda Azure CLI i Automation-skriptet genererar du ett huvud namn för tjänsten med hjälp av följande kommando.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Logga in med i skriptet `az login --service-principal` och ange information om huvud kontot. Sedan kan du använda `az webapp config container set` för att ange behållar namn, tagg, register-URL och lösen ord för registret. Nedan visas några användbara länkar som du kan använda för att skapa din process CI-process.

- [Så här loggar du in på Azure CLI i cirkel CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific överväganden

### <a name="java"></a>Java

Använd kudu [-zipdeploy/](deploy-zip.md) API för att distribuera jar-program och [WARDEPLOY/](deploy-zip.md#deploy-war-file) för krigs appar. Om du använder Jenkins kan du använda dessa API: er direkt i distributions fasen. Mer information finns i [den här artikeln](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Nod

Som standard kör kudu Bygg stegen för Node-programmet ( `npm install` ). Om du använder en build-tjänst, till exempel Azure DevOps, är kudu-versionen onödig. Om du vill inaktivera kudu skapar du en app-inställning `SCM_DO_BUILD_DURING_DEPLOYMENT` med värdet `false` .

### <a name="net"></a>.NET 

Som standard kör kudu Bygg stegen för ditt .NET-program ( `dotnet build` ). Om du använder en build-tjänst, till exempel Azure DevOps, är kudu-versionen onödig. Om du vill inaktivera kudu skapar du en app-inställning `SCM_DO_BUILD_DURING_DEPLOYMENT` med värdet `false` .

## <a name="other-deployment-considerations"></a>Andra distributions överväganden

### <a name="local-cache"></a>Lokalt cacheminne

Azure App Service innehåll lagras på Azure Storage och placeras på ett hållbart sätt som en innehålls resurs. Vissa appar behöver dock bara ett skrivskyddat innehålls lager med hög prestanda som de kan köra med hög tillgänglighet. De här apparna kan dra nytta av [lokal cache](overview-local-cache.md). Local cache rekommenderas inte för innehålls hanterings webbplatser som WordPress.

Använd alltid lokalt cacheminne tillsammans med [distributions platser](deploy-staging-slots.md) för att förhindra stillestånds tid. I [det här avsnittet](overview-local-cache.md#best-practices-for-using-app-service-local-cache) finns information om hur du använder dessa funktioner tillsammans.

### <a name="high-cpu-or-memory"></a>Hög CPU eller minne

Om din App Services plan använder över 90% av tillgänglig processor eller minne kan den underliggande virtuella datorn ha problem med att bearbeta distributionen. När detta inträffar kan du tillfälligt skala upp antalet instanser för att utföra distributionen. När distributionen är färdig kan du returnera instans antalet till det tidigare värdet.

Mer information om bästa praxis finns i [App Service Diagnostics](./overview-diagnostics.md) för att ta reda på vilka metod tips som är lämpliga för din resurs.

- Navigera till din webbapp i [Azure Portal](https://portal.azure.com).
- Klicka på **diagnostisera och lös problem** i det vänstra navigerings fönstret, som öppnar App Service Diagnostics.
- Välj Start sida för **bästa praxis** .
- Klicka på **metod tips för tillgänglighets & prestanda** eller **bästa praxis för optimal konfiguration** för att visa appens aktuella tillstånd med avseende på dessa metod tips.

Du kan också använda den här länken för att direkt öppna App Service diagnostik för din resurs: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .