---
title: Automatiserad publicering för kontinuerlig integrering och leverans
description: Lär dig hur du publicerar för kontinuerlig integrering och leverans automatiskt.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366848"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatiserad publicering för kontinuerlig integrering och leverans

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod för att testa varje ändring av kodbasen automatiskt och så snart som möjligt kontinuerlig leverans följer testerna som sker under kontinuerlig integrering och skickar ändringar till ett mellanlagrings-eller produktions system.

I Azure Data Factory innebär kontinuerlig integrering och leverans (CI/CD) flytt av Data Factory-pipelines från en miljö (utveckling, testning, produktion) till en annan. Azure Data Factory använder [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md) för att lagra konfigurationen av dina olika ADF-enheter (pipelines, data uppsättningar, data flöden osv.). Det finns två föreslagna metoder för att flytta en data fabrik till en annan miljö:

- Automatiserad distribution med hjälp av Data Factorys integrering med [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ladda upp en Resource Manager-mall manuellt med hjälp av Data Factory UX-integrering med Azure Resource Manager.

Mer information finns i [kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).

I den här artikeln fokuserar vi på förbättringarna för kontinuerlig distribution och funktionen automatiserad publicering för CI/CD.

## <a name="continuous-deployment-improvements"></a>Förbättringar av kontinuerlig distribution

Funktionen "automatiserad publicering" använder mall-funktionerna *verifiera alla* och exportera *Azure Resource Manager (arm)* från ADF-UX och gör det möjligt att använda logiken via ett offentligt tillgängligt NPM-paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . På så sätt kan du programmatiskt utlösa dessa åtgärder i stället för att behöva gå till ADF-ANVÄNDARGRÄNSSNITTET och göra en knapp klickning. Detta ger din CI/CD-pipeline en sann, kontinuerlig integrerings upplevelse.

### <a name="current-cicd-flow"></a>Aktuellt CI/CD-flöde

1. Varje användare gör ändringar i sina privata grenar.
2. Push to Master är förbjudet, användarna måste skapa ett PR-huvud för att göra ändringar.
3. Användare måste läsa in ADF UI och klicka på Publicera för att distribuera ändringar till Data Factory och generera ARM-mallarna i publicerings grenen.
4. DevOps release pipeline har kon figurer ATS för att skapa en ny version och distribuera ARM-mallen varje gången en ny ändring skickas till publicerings grenen.

![Aktuellt CI/CD-flöde](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuellt steg

I det aktuella CI/CD-flödet är UX det förmedlande gränssnittet för att skapa ARM-mallen. en användare måste därför gå till ADF UI och manuellt Klicka på Publicera för att starta skapande av ARM-mallen och släppa den i publicerings grenen, som är en Hack.

### <a name="the-new-cicd-flow"></a>Det nya CI/CD-flödet

1. Varje användare gör ändringar i sina privata grenar.
2. Push to Master är förbjudet, användarna måste skapa ett PR-huvud för att göra ändringar.
3. **Azure DevOps pipeline Bygg utlöses varje gång ett nytt genomförande görs till Master, validerar resurserna och genererar en ARM-mall som en artefakt om verifieringen lyckas.**
4. DevOps release pipeline har kon figurer ATS för att skapa en ny version och distribuera ARM-mallen varje gång en ny version är tillgänglig. 

![Nytt CI/CD-flöde](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Vad har ändrats?

- Nu har vi en build-process med en DevOps build-pipeline.
- I build-pipeline används ADFUtilities NPM-paket, som validerar alla resurser och genererar ARM-mallarna (enkla och länkade mallar).
- Bygg pipelinen ansvarar för att verifiera ADF-resurser och generera ARM-mallen i stället för ADF-gränssnittet (knappen publicera).
- DevOps versions definition kommer nu att använda den nya bygg pipelinen i stället för git-artefakten.

> [!NOTE]
> Du kan fortsätta att använda befintlig mekanism (adf_publish gren) eller använda det nya flödet. Båda stöds. 

## <a name="package-overview"></a>Paketöversikt

Det finns två kommandon som är tillgängliga i paketet:
- Exportera ARM-mall
- Verifiera

### <a name="export-arm-template"></a>Exportera ARM-mall

Kör NPM kör starta export <rootFolder> <factoryId> [outputFolder] för att exportera arm-mallen med hjälp av resurser i en specifik mapp. Det här kommandot kör en verifierings kontroll och innan du genererar ARM-mallen. Nedan visas ett exempel:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- FactoryId är ett obligatoriskt fält som representerar Data Factory-resurs-ID: t i formatet: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/factories/ <dfName> ".
- OutputFolder är en valfri parameter som anger den relativa sökvägen för att spara den genererade ARM-mallen.
 
> [!NOTE]
> ARM-mallen som genererades har inte publicerats till `Live` versionen av fabriken. Distributionen bör göras med en CI/CD-pipeline. 
 

### <a name="validate"></a>Verifiera

Kör NPM kör Start validate <rootFolder> <factoryId> för att validera alla resurser i en specifik mapp. Nedan visas ett exempel:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- FactoryId är ett obligatoriskt fält som representerar Data Factory-resurs-ID: t i formatet: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Skapa en Azure-pipeline

NPM-paket kan användas på olika sätt, men en av de främsta fördelarna förbrukas via en [Azure-pipeline](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). I varje sammanslagning till samarbets grenen kan en pipeline utlösas som först validerar all kod och sedan exporterar ARM-mallen till en [versions artefakt](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) som kan utnyttjas av en versions pipeline. **Hur det skiljer sig från den aktuella CI/CD-processen är att du pekar din versions pipeline i den här artefakten i stället för den befintliga `adf_publish` grenen.**

Kom igång genom att följa stegen nedan:

1.  Öppna ett Azure DevOps-projekt och gå till "pipeliner". Välj "ny pipeline".

    ![Ny pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Välj den lagrings plats där du vill spara YAML-skriptet för pipelinen. Vi rekommenderar att du sparar den i en *build* -mapp inom samma lagrings plats för dina ADF-resurser. Se till att det finns en **package.jspå** filen i lagrings platsen och innehåller paket namnet (se exemplet nedan).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Välj *Start-pipeline*. Om du har överfört eller sammanfogat YAML-filen (som visas i exemplet nedan) kan du också peka direkt vid den och redigera den. 

    ![Starten pipeline](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Ange i YAML-koden. Vi rekommenderar att du tar YAML-filen och använder den som en start punkt.
5.  Spara och kör. Om du använder YAML aktive ras den varje gång "Main"-grenen uppdateras.

## <a name="next-steps"></a>Nästa steg

Läs mer om kontinuerlig integrering och leverans i Data Factory: 

- [Kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).
