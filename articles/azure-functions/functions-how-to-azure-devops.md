---
title: Uppdatera funktionsappens kod kontinuerligt med Azure DevOps
description: Lär dig hur du ställer in en Azure DevOps-pipeline som är Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762217"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Kontinuerlig leverans med hjälp av Azure DevOps

Du kan automatiskt distribuera funktionen till en Azure Functions med hjälp av [Azure Pipelines](/azure/devops/pipelines/).

Du har två alternativ för att definiera din pipeline:

- **YAML-fil:** En YAML-fil beskriver pipelinen. Filen kan ha ett avsnitt med byggsteg och ett lanseringsavsnitt. YAML-filen måste finnas på samma lagringsplatsen som appen.
- **Mall:** Mallar är färdiga uppgifter som skapar eller distribuerar din app.

## <a name="yaml-based-pipeline"></a>YAML-baserad pipeline

Om du vill skapa en YAML-baserad pipeline skapar du först appen och distribuerar sedan appen.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure Pipelines beror på appens programmeringsspråk. Varje språk har specifika byggsteg som skapar en distributionsartefakt. En distributionsartefakt används för att distribuera funktionsappen i Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Du kan använda följande exempel för att skapa en YAML-fil för att skapa en .NET-app:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Du kan använda följande exempel för att skapa en YAML-fil för att skapa en JavaScript-app:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Du kan använda något av följande exempel för att skapa en YAML-fil för att skapa en app för en specifik Python-version. Python stöds endast för funktionsappar som körs i Linux.

**Version 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Version 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Du kan använda följande exempel för att skapa en YAML-fil för att paketera en PowerShell-app. PowerShell stöds endast för Windows Azure Functions.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Distribuera din app

Du måste inkludera något av följande YAML-exempel i YAML-filen, beroende på värdoperativsystemet.

#### <a name="windows-function-app"></a>Windows-funktionsapp

Du kan använda följande kodfragment för att distribuera en Windows-funktionsapp:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux-funktionsapp

Du kan använda följande kodfragment för att distribuera en Linux-funktionsapp:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Mallbaserad pipeline

Mallar i Azure DevOps är fördefinierade grupper av uppgifter som skapar eller distribuerar en app.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure Pipelines beror på appens programmeringsspråk. Varje språk har specifika byggsteg som skapar en distributionsartefakt. En distributionsartefakt används för att uppdatera funktionsappen i Azure.

Om du vill använda inbyggda byggmallar väljer du  Använd den klassiska redigeraren för att skapa en pipeline med hjälp av designermallar när du skapar en ny bygg-pipeline.

![Välj den klassiska redigeraren för Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

När du har konfigurerat källkoden för din kod söker du efter Azure Functions skapa mallar. Välj den mall som matchar ditt appspråk.

![Välj en Azure Functions skapa mall](media/functions-how-to-azure-devops/build-templates.png)

I vissa fall har byggartefakter en specifik mappstruktur. Du kan behöva markera kryssrutan **Prepend root folder name to archive paths (Förberedelserotmappens** namn för att arkivera sökvägar).

![Alternativet för att förbereda rotmappens namn](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-appar

Om JavaScript-appen är beroende av inbyggda Windows-moduler måste du uppdatera agentpoolversionen till **Hosted VS2017**.

![Uppdatera agentpoolens version](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuera din app

När du skapar en ny lanseringspipeline söker du efter Azure Functions nya publiceringsmallen.

![Sök efter den Azure Functions publiceringsmallen](media/functions-how-to-azure-devops/release-template.png)

Distribution till ett distributionsfack stöds inte i publiceringsmallen.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Skapa en bygg-pipeline med hjälp av Azure CLI

Om du vill skapa en bygg-pipeline i Azure använder du `az functionapp devops-pipeline create` [kommandot](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create). Bygg-pipelinen skapas för att skapa och släppa eventuella kodändringar som görs i din lagringsplatsen. Kommandot genererar en ny YAML-fil som definierar bygg- och lanseringspipelinen och sedan sparar den till din lagringsplatsen. Förutsättningarna för det här kommandot beror på var koden finns.

- Om din kod finns i GitHub:

    - Du måste ha **skrivbehörighet** för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

    - Du måste ha behörighet att skapa en personlig åtkomsttoken (PAT) för GitHub som har tillräcklig behörighet. Mer information finns i [Behörighetskrav för GitHub PAT.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Du måste ha behörighet att genomföra till main-grenen i GitHub-lagringsplatsen så att du kan genomföra den automatiskt genererade YAML-filen.

- Om din kod finns i Azure Repos:

    - Du måste ha **skrivbehörighet** för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

## <a name="next-steps"></a>Nästa steg

- Granska Azure Functions [översikt.](functions-overview.md)
- Läs [översikten över Azure DevOps.](/azure/devops/pipelines/)