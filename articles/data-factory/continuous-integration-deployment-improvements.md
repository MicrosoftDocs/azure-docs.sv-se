---
title: Automatiserad publicering för kontinuerlig integrering och leverans
description: Lär dig hur du publicerar för kontinuerlig integrering och leverans automatiskt.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b2c48fcc11feaec3efc0acab283609181b92a3dc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780471"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatiserad publicering för kontinuerlig integrering och leverans

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod som testar varje ändring av kodbasen automatiskt. Så tidigt som möjligt följer den kontinuerliga leveransen de tester som sker under kontinuerlig integrering och skickar ändringar till ett mellanlagrings-eller produktions system.

I Azure Data Factory innebär kontinuerlig integrering och kontinuerlig leverans (CI/CD) flytt av Data Factory pipelines från en miljö, t. ex. utveckling, testning och produktion, till en annan. Data Factory använder [Azure Resource Manager mallar (arm-mallar)](../azure-resource-manager/templates/overview.md) för att lagra konfigurationen för dina olika Data Factory entiteter, till exempel pipelines, data uppsättningar och data flöden.

Det finns två föreslagna metoder för att flytta en data fabrik till en annan miljö:

- Automatiserad distribution med hjälp av integrering av Data Factory med [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ladda upp en ARM-mall manuellt med hjälp av Data Factory User Experience integration med Azure Resource Manager.

Mer information finns i [kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).

Den här artikeln fokuserar på förbättringarna för kontinuerlig distribution och funktionen automatiserad publicering för CI/CD.

## <a name="continuous-deployment-improvements"></a>Förbättringar av kontinuerlig distribution

Funktionen för automatisk publicering använder mallarna **verifiera alla** och **Exportera arm** -funktioner från den Data Factory användar upplevelsen och gör det möjligt att använda logiken via ett offentligt tillgängligt NPM-paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Därför kan du program mässigt utlösa dessa åtgärder i stället för att behöva gå till Data Factory gränssnittet och välja en knapp manuellt. Den här funktionen kommer att ge dina CI/CD-pipeliner en sann, kontinuerlig integrerings upplevelse.

### <a name="current-cicd-flow"></a>Aktuellt CI/CD-flöde

1. Varje användare gör ändringar i sina privata grenar.
1. Push to Master är inte tillåtet. Användarna måste skapa en pull-begäran för att göra ändringar.
1. Användare måste läsa in Data Factory gränssnittet och välja **publicera** för att distribuera ändringar till Data Factory och generera arm-mallarna i publicerings grenen.
1. DevOps release-pipeline har kon figurer ATS för att skapa en ny version och distribuera ARM-mallen varje gången en ny ändring skickas till publicerings grenen.

![Diagram som visar det aktuella CI/CD-flödet.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuellt steg

I det aktuella CI/CD-flödet är användar upplevelsen den mellanhand för att skapa ARM-mallen. Därför måste en användare gå till Data Factory användar gränssnitt och manuellt välja **publicera** för att starta arm-mallen och ta bort den i publicerings grenen.

### <a name="the-new-cicd-flow"></a>Det nya CI/CD-flödet

1. Varje användare gör ändringar i sina privata grenar.
1. Push to Master är inte tillåtet. Användarna måste skapa en pull-begäran för att göra ändringar.
1. Azure DevOps pipeline build utlöses varje gång ett nytt genomförande görs till Master. Den verifierar resurserna och genererar en ARM-mall som en artefakt om verifieringen lyckas.
1. DevOps release-pipeline har kon figurer ATS för att skapa en ny version och distribuera ARM-mallen varje gång en ny version är tillgänglig.

![Diagram som visar det nya CI/CD-flödet.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Vad har ändrats?

- Nu har vi en build-process som använder en DevOps build-pipeline.
- I pipeline för build används ADFUtilities NPM-paketet som validerar alla resurser och genererar ARM-mallarna. Dessa mallar kan vara enkla och länkade.
- Bygg pipelinen ansvarar för att verifiera Data Factory resurser och generera ARM-mallen i stället för Data Factory UI (knappen **publicera** ).
- DevOps versions definition kommer nu att använda den nya bygg pipelinen i stället för git-artefakten.

> [!NOTE]
> Du kan fortsätta att använda den befintliga mekanismen, som är `adf_publish` grenen, eller så kan du använda det nya flödet. Båda stöds.

## <a name="package-overview"></a>Paketöversikt

Det finns två kommandon som är tillgängliga i paketet:

- Exportera ARM-mall
- Verifiera

### <a name="export-arm-template"></a>Exportera ARM-mall

Kör `npm run start export <rootFolder> <factoryId> [outputFolder]` för att exportera arm-mallen med hjälp av resurser i en specifik mapp. Det här kommandot kör också en verifierings kontroll innan du genererar ARM-mallen. Här är ett exempel:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- `FactoryId` är ett obligatoriskt fält som representerar Data Factory resurs-ID i formatet `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` är en valfri parameter som anger den relativa sökvägen för att spara den genererade ARM-mallen.
 
> [!NOTE]
> ARM-mallen som genererades har inte publicerats till den Live-versionen av fabriken. Distributionen bör göras med en CI/CD-pipeline.
 
### <a name="validate"></a>Verifiera

Kör `npm run start validate <rootFolder> <factoryId>` för att validera alla resurser i en specifik mapp. Här är ett exempel:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- `FactoryId` är ett obligatoriskt fält som representerar Data Factory resurs-ID i formatet `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Skapa en Azure-pipeline

NPM-paket kan användas på olika sätt, men en av de främsta fördelarna förbrukas via [Azure pipeline](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). I varje sammanslagning till samarbets grenen kan en pipeline utlösas som först validerar all kod och sedan exporterar ARM-mallen till en [versions artefakt](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) som kan utnyttjas av en versions pipeline. Hur det skiljer sig från den aktuella CI/CD-processen är att du *pekar din versions pipeline i den här artefakten i stället för den befintliga `adf_publish` grenen*.

Kom igång genom att följa dessa steg:

1.  Öppna ett Azure DevOps-projekt och gå till **pipeliner**. Välj **Ny pipeline**.

    ![Skärm bild som visar knappen Ny pipeline.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Välj den lagrings plats där du vill spara YAML-skriptet för pipelinen. Vi rekommenderar att du sparar den i en build-mapp i samma databas som dina Data Factory-resurser. Se till att det finns en *package.jspå* filen i lagrings platsen som innehåller paket namnet, som du ser i följande exempel:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Välj **Start-pipeline**. Om du har laddat upp eller sammanfogat YAML-filen, som du ser i följande exempel, kan du också peka direkt vid den och redigera den.

    ![Skärm bild som visar start pipelinen.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Ange din YAML-kod. Vi rekommenderar att du använder YAML-filen som utgångs punkt.
1.  Spara och kör. Om du använde YAML aktive ras den varje gång som huvud grenen uppdateras.

## <a name="next-steps"></a>Nästa steg

Läs mer om kontinuerlig integrering och leverans i Data Factory:

- [Kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).
