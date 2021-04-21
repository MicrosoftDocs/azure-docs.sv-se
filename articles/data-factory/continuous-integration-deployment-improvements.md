---
title: Automatiserad publicering för kontinuerlig integrering och leverans
description: Lär dig hur du publicerar för kontinuerlig integrering och leverans automatiskt.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 5730b0c7e522f7496f578ffebf716957fcaa56b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788943"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatiserad publicering för kontinuerlig integrering och leverans

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod som testar varje ändring av kodbasen automatiskt. Så tidigt som möjligt följer kontinuerlig leverans de tester som sker under kontinuerlig integrering och push-meddelanden till ett mellanlagrings- eller produktionssystem.

I Azure Data Factory innebär kontinuerlig integrering och kontinuerlig leverans (CI/CD) att Data Factory-pipelines flyttas från en miljö, till exempel utveckling, testning och produktion, till en annan. Data Factory använder [Azure Resource Manager -mallar (ARM-mallar)](../azure-resource-manager/templates/overview.md) för att lagra konfigurationen av dina olika Data Factory-entiteter, till exempel pipelines, datauppsättningar och dataflöden.

Det finns två föreslagna metoder för att befordra en datafabrik till en annan miljö:

- Automatiserad distribution med integrering av Data Factory med [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ladda upp en ARM-mall manuellt med hjälp Data Factory integrering av användarupplevelsen med Azure Resource Manager.

Mer information finns i [Kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).

Den här artikeln fokuserar på kontinuerliga distributionsförbättringar och funktionen för automatisk publicering för CI/CD.

## <a name="continuous-deployment-improvements"></a>Kontinuerliga distributionsförbättringar

Funktionen för automatisk publicering använder funktionerna **Verifiera** alla och Exportera **ARM-mallar** från Data Factory-användarupplevelsen och gör logiken tillgänglig via ett offentligt tillgängligt npm-paket. [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) Därför kan du programmatiskt utlösa dessa åtgärder i stället för att behöva gå till Data Factory och välja en knapp manuellt. Den här funktionen ger dina CI/CD-pipelines en verklig kontinuerlig integreringsupplevelse.

### <a name="current-cicd-flow"></a>Aktuellt CI/CD-flöde

1. Varje användare gör ändringar i sina privata grenar.
1. Push till master tillåts inte. Användarna måste skapa en pull-begäran för att göra ändringar.
1. Användarna måste läsa Data Factory användargränssnitt  och välja Publicera för att distribuera ändringar Data Factory och generera ARM-mallarna i publiceringsgrenen.
1. DevOps-lanseringspipelinen konfigureras för att skapa en ny version och distribuera ARM-mallen varje gång en ny ändring skickas till publiceringsgrenen.

![Diagram som visar det aktuella CI/CD-flödet.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuellt steg

I det aktuella CI/CD-flödet är användarupplevelsen mellanhanden för att skapa ARM-mallen. Därför måste en användare gå till Data Factory användargränssnitt och  manuellt välja Publicera för att starta ARM-mallgenereringen och släppa den i publiceringsgrenen.

### <a name="the-new-cicd-flow"></a>Det nya CI/CD-flödet

1. Varje användare gör ändringar i sina privata grenar.
1. Push till master tillåts inte. Användarna måste skapa en pull-begäran för att göra ändringar.
1. Bygget av Azure DevOps-pipelinen utlöses varje gång en ny commit görs till master. Den validerar resurserna och genererar en ARM-mall som en artefakt om valideringen lyckas.
1. DevOps-lanseringspipelinen konfigureras för att skapa en ny version och distribuera ARM-mallen varje gång en ny version är tillgänglig.

![Diagram som visar det nya CI/CD-flödet.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Vad har ändrats?

- Nu har vi en byggprocess som använder en DevOps-bygg-pipeline.
- Bygg-pipelinen använder NPM-paketet ADFUtilities, som validerar alla resurser och genererar ARM-mallarna. Dessa mallar kan vara enkla och länkade.
- Bygg-pipelinen ansvarar för att verifiera Data Factory och generera ARM-mallen i stället för Data Factory användargränssnitt **(knappen** Publicera).
- DevOps-releasedefinitionen kommer nu att använda den här nya bygg-pipelinen i stället för Git-artefakten.

> [!NOTE]
> Du kan fortsätta att använda den befintliga mekanismen, som är `adf_publish` -grenen, eller så kan du använda det nya flödet. Båda stöds.

## <a name="package-overview"></a>Paketöversikt

Två kommandon är för närvarande tillgängliga i paketet:

- Exportera ARM-mall
- Verifiera

### <a name="export-arm-template"></a>Exportera ARM-mall

Kör `npm run start export <rootFolder> <factoryId> [outputFolder]` för att exportera ARM-mallen med hjälp av resurserna i en viss mapp. Det här kommandot kör också en valideringskontroll innan ARM-mallen genereras. Här är ett exempel:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- `FactoryId` är ett obligatoriskt fält som representerar Data Factory resurs-ID i formatet `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` är en valfri parameter som anger den relativa sökvägen för att spara den genererade ARM-mallen.
 
> [!NOTE]
> ARM-mallen som genereras publiceras inte till den levande versionen av fabriken. Distributionen bör göras med hjälp av en CI/CD-pipeline.
 
### <a name="validate"></a>Verifiera

Kör `npm run start validate <rootFolder> <factoryId>` för att verifiera alla resurser i en viss mapp. Här är ett exempel:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` är ett obligatoriskt fält som representerar var Data Factory resurserna finns.
- `FactoryId` är ett obligatoriskt fält som representerar Data Factory resurs-ID i formatet `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Skapa en Azure-pipeline

NPM-paket kan användas på olika sätt, men en av de främsta fördelarna är att de används via [Azure Pipeline.](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0) Vid varje sammanslagning i din samarbetsgren kan en pipeline utlösas som först validerar all [](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) kod och sedan exporterar ARM-mallen till en byggartefakt som kan användas av en lanseringspipeline. Hur den skiljer sig från den aktuella CI/CD-processen är att du kommer att peka din lanseringspipeline på den *här artefakten i stället för den befintliga `adf_publish` grenen*.

Kom igång genom att följa dessa steg:

1.  Öppna ett Azure DevOps-projekt och gå till **Pipelines**. Välj **Ny pipeline**.

    ![Skärmbild som visar knappen Ny pipeline.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Välj den lagringsplats där du vill spara YAML-skriptet för pipelinen. Vi rekommenderar att du sparar den i en byggmapp i samma lagringsplats för dina Data Factory resurser. Kontrollera att det finns *package.jsfil* på lagringsplatsen som innehåller paketnamnet, som du ser i följande exempel:

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
    
1.  Välj **Startpipeline.** Om du har laddat upp eller sammanfogat YAML-filen, som du ser i följande exempel, kan du också peka direkt på den och redigera den.

    ![Skärmbild som visar Startpipeline.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

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
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Ange din YAML-kod. Vi rekommenderar att du använder YAML-filen som utgångspunkt.
1.  Spara och kör. Om du använde YAML utlöses den varje gång huvudgrenen uppdateras.

## <a name="next-steps"></a>Nästa steg

Läs mer om kontinuerlig integrering och leverans i Data Factory:

- [Kontinuerlig integrering och leverans i Azure Data Factory](continuous-integration-deployment.md).
