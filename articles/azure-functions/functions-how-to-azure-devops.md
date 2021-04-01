---
title: Uppdatera funktionens app-kod kontinuerligt med Azure DevOps
description: Lär dig hur du konfigurerar en Azure DevOps-pipeline som är riktad mot Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563423"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Kontinuerlig leverans med hjälp av Azure-DevOps

Du kan distribuera din funktion automatiskt till en Azure Functions-app med hjälp av [Azure-pipeliner](/azure/devops/pipelines/).

Du har två alternativ för att definiera din pipeline:

- **Yaml-fil**: en yaml-fil beskriver pipelinen. Filen kan ha ett build-steg-avsnitt och en version. YAML-filen måste finnas i samma lagrings platsen som appen.
- **Mall**: mallar är färdiga uppgifter som skapar eller distribuerar din app.

## <a name="yaml-based-pipeline"></a>YAML-baserad pipeline

Skapa en YAML-baserad pipeline genom att först skapa din app och sedan distribuera appen.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure-pipeliner beror på appens programmeringsspråk. Varje språk har specifika build-steg som skapar en distributions artefakt. En distributions artefakt används för att distribuera din Function-app i Azure.

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

Du kan använda något av följande exempel för att skapa en YAML-fil för att skapa en app för en speciell python-version. Python stöds bara för Function-appar som körs på Linux.

**Version 3,7**

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

**Version 3,6**

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

Du kan använda följande exempel för att skapa en YAML-fil för att paketera en PowerShell-app. PowerShell stöds endast för Windows-Azure Functions.

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

Du måste inkludera något av följande YAML-exempel i din YAML-fil, beroende på värd operativ systemet.

#### <a name="windows-function-app"></a>Windows Function-app

Du kan använda följande kodfragment för att distribuera en Windows Function-app:

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

#### <a name="linux-function-app"></a>Linux Function-app

Du kan använda följande kodfragment för att distribuera en Linux Function-app:

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

## <a name="template-based-pipeline"></a>Mall-baserad pipeline

Mallar i Azure DevOps är fördefinierade grupper av aktiviteter som skapar eller distribuerar en app.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure-pipeliner beror på appens programmeringsspråk. Varje språk har specifika build-steg som skapar en distributions artefakt. En distributions artefakt används för att uppdatera din Function-app i Azure.

Om du vill använda inbyggda build-mallar när du skapar en ny versions pipeline väljer du **Använd den klassiska redigeraren** för att skapa en pipeline med hjälp av designer-mallar.

![Välj den klassiska redigeraren för Azure-pipelinen](media/functions-how-to-azure-devops/classic-editor.png)

När du har konfigurerat kodens källa söker du efter Azure Functions skapar mallar. Välj den mall som matchar ditt programmeringsspråk.

![Välj en mall för Azure Functions version](media/functions-how-to-azure-devops/build-templates.png)

I vissa fall har build-artefakter en viss mappstruktur. Du kan behöva Markera kryss rutan **lägga rot Katalog namn för att arkivera sökvägar** .

![Alternativet att lägga namnet på rotmappen](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-appar

Om JavaScript-appen har ett beroende av inbyggda Windows-moduler måste du uppdatera agentens version till **värdbaserade VS2017**.

![Uppdatera agentens pool version](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuera din app

När du skapar en ny versions pipeline kan du söka efter mallen för Azure Functions-versionen.

![Sök efter mallen för Azure Functions-versionen](media/functions-how-to-azure-devops/release-template.png)

Distribution till en distributions plats stöds inte i versions mal len.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Skapa en pipeline för bygge med hjälp av Azure CLI

Använd kommandot för att skapa en pipeline för bygge i Azure `az functionapp devops-pipeline create` [](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Skapa pipeline skapas för att skapa och släppa alla kod ändringar som görs i din lagrings platsen. Kommandot genererar en ny YAML-fil som definierar pipelinen build och release och som sedan allokerar den till din lagrings platsen. Kraven för det här kommandot beror på var koden finns.

- Om din kod är i GitHub:

    - Du måste ha **Skriv** behörighet för din prenumeration.

    - Du måste vara projekt administratör i Azure DevOps.

    - Du måste ha behörighet att skapa en GitHub-PAT (personal Access token) som har tillräcklig behörighet. Mer information finns i [krav för GITHUB Pat-behörighet.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Du måste ha behörighet att bekräfta till huvud grenen i din GitHub-lagringsplats så att du kan spara den automatiskt genererade YAML-filen.

- Om din kod är i Azure databaser:

    - Du måste ha **Skriv** behörighet för din prenumeration.

    - Du måste vara projekt administratör i Azure DevOps.

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Functions översikt](functions-overview.md).
- Granska [Azure DevOps-översikten](/azure/devops/pipelines/).