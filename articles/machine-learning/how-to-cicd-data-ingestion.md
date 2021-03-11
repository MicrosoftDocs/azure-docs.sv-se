---
title: DevOps i en pipeline för datainmatning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder DevOps-metoder för att skapa en pipeline för data inmatning för att förbereda data med hjälp av Azure Data Factory och Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: e8a8b952d917db3a7eefd2e0371d41287c5be944
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612481"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps i en pipeline för datainmatning

I de flesta fall är en lösning för data inmatning en sammansättning av skript, tjänst anrop och en pipeline som dirigerar alla aktiviteter. I den här artikeln får du lära dig hur du tillämpar DevOps-metoder i utvecklings livs cykeln för en gemensam pipeline för data inmatning som förbereder data för maskin inlärnings modell träning. Pipelinen skapas med följande Azure-tjänster:

* __Azure Data Factory__: läser rå data och dirigerar förberedelse av data.
* __Azure Databricks__: kör en python-anteckningsbok som transformerar data.
* __Azure-pipeliner__: automatiserar en kontinuerlig integrerings-och utvecklings process.

## <a name="data-ingestion-pipeline-workflow"></a>Pipeline-arbetsflöde för data inmatning

Pipelinen för data inmatning implementerar följande arbets flöde:

1. Rå data läses in i en Azure Data Factory (ADF) pipeline.
1. ADF-pipeline skickar data till ett Azure Databricks kluster som kör en python-anteckningsbok för att transformera data.
1. Data lagras i en BLOB-behållare där de kan användas av Azure Machine Learning för att träna en modell.

![pipeline-arbetsflöde för data inmatning](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Översikt över kontinuerlig integrering och leverans

Precis som med många program varu lösningar finns det ett team (till exempel data tekniker) som arbetar med den. De samarbetar och delar samma Azure-resurser, till exempel Azure Data Factory, Azure Databricks och Azure Storage konton. Samlingen av dessa resurser är en utvecklings miljö. Data teknikerna bidrar till samma käll kods bas.

En kontinuerlig integrering och ett leverans system automatiserar processen med att skapa, testa och leverera (distribuera) lösningen. Processen för kontinuerlig integrering (CI) utför följande aktiviteter:

* Assemblerar koden
* Kontrollerar den med kod kvalitets test
* Kör enhets test
* Genererar artefakter som testad kod och Azure Resource Manager mallar

Processen för kontinuerlig leverans (CD) distribuerar artefakterna till de efterföljande miljöerna.

![cicd data inmatnings diagram](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Den här artikeln visar hur du automatiserar CI-och CD-processer med [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Käll kontroll hantering

Käll kontroll hantering krävs för att spåra ändringar och aktivera samarbete mellan team medlemmar.
Koden skulle till exempel lagras i en Azure-DevOps, GitHub-eller GitLab-lagringsplats. Samarbets flödet baseras på en förgrenings modell. Till exempel [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Käll kod för python Notebook

Data tekniker fungerar med käll koden för python Notebook antingen lokalt i en IDE (till exempel [Visual Studio Code](https://code.visualstudio.com)) eller direkt i arbets ytan Databricks. När kod ändringarna har slutförts slås de samman till lagrings platsen efter en förgrenings princip.

> [!TIP] 
> Vi rekommenderar att du lagrar koden i `.py` filer i stället för i `.ipynb` Jupyter Notebook format. Det förbättrar kodens läsbarhet och aktiverar automatisk kod kvalitets kontroller i CI-processen.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory käll kod

Käll koden för Azure Data Factory pipelines är en samling JSON-filer som genereras av en Azure Data Factory arbets yta. Vanligt vis arbetar data teknikerna med en visuell designer i Azure Data Factory-arbetsytan i stället för med källfilerna direkt. 

Information om hur du konfigurerar arbets ytan för att använda en lagrings plats för käll kontroll finns i [Redigera med Azure databaser git-integrering](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>Kontinuerlig integrering (CI)

Det ultimata målet för den kontinuerliga integrerings processen är att samla in den gemensamma gruppen från käll koden och förbereda den för distributionen till de efterföljande miljöerna. Precis som med käll kods hanteringen är den här processen annorlunda för python-anteckningsboken och Azure Data Factory pipelines. 

### <a name="python-notebook-ci"></a>CI Notebook CI

CI-processen för python-Anteckningsbokarna hämtar koden från samarbets grenen (till exempel ***Master** _ eller _ *_utvecklar_* *) och utför följande aktiviteter:
* Kod luddfri
* Enhetstestning
* Spara koden som en artefakt

Följande kodfragment visar implementeringen av de här stegen i en Azure DevOps ***yaml*** -pipeline:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

Pipelinen använder [flake8](https://pypi.org/project/flake8/) för att göra python-koden luddfri. Den kör enhets test som definierats i käll koden och publicerar de luddfri och test resultaten så att de är tillgängliga på skärmen för Azure pipeline-körning:

![luddfri enhets test](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Om installationen av en luddfri enhet och enhet lyckas, kommer pipelinen att kopiera käll koden till artefakt lagrings platsen som ska användas av de efterföljande distributions stegen.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

CI-processen för en Azure Data Factory pipeline är en Flask hals för en pipeline för data inmatning. Det finns ingen kontinuerlig integrering. En distributions bara artefakt för Azure Data Factory är en samling Azure Resource Manager mallar. Det enda sättet att skapa dessa mallar är att klicka på knappen ***publicera*** i arbets ytan Azure Data Factory.

1. Data teknikerna kopplar käll koden från deras funktions grenar till samarbets grenen, till exempel ***Master** _ eller _ *_utvecklar_* *. 
1. Någon med de beviljade behörigheterna klickar på knappen ***publicera*** för att skapa Azure Resource Manager mallar från käll koden i samarbets grenen. 
1. Arbets ytan validerar pipelines (Tänk på den vid rad-och enhets testning), genererar Azure Resource Manager mallar (Tänk på den när de skapar) och sparar de genererade mallarna till en teknisk gren ***adf_publish*** i samma kod lagrings plats (Tänk på den när det gäller publicering av artefakter). Den här grenen skapas automatiskt av Azure Data Factory-arbetsytan. 

Mer information om den här processen finns i [kontinuerlig integrering och leverans i Azure Data Factory](../data-factory/continuous-integration-deployment.md).

Det är viktigt att se till att de genererade Azure Resource Manager-mallarna är miljö oberoende. Det innebär att alla värden som kan skilja sig mellan olika miljöer är parametrized. Azure Data Factory är tillräckligt smart för att exponera de flesta av dessa värden som parametrar. I följande mall visas till exempel anslutnings egenskaperna till en Azure Machine Learning arbets yta som parametrar:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Men du kanske vill visa dina anpassade egenskaper som inte hanteras av Azure Data Factory arbets ytan som standard. I scenariot för den här artikeln anropar en Azure Data Factory pipeline en python-anteckningsbok som bearbetar data. Antecknings boken accepterar en parameter med namnet på en indata-fil.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Det här namnet skiljer sig för ***dev** _, _*_frågor och svar_*_, _*_UAT_*_ och _*_produktions_*_ miljöer. I en komplex pipeline med flera aktiviteter kan det finnas flera anpassade egenskaper. Det är en bra idé att samla in alla dessa värden på en plats och definiera dem som pipeline _ *_variabler_* *:

![Skärm bild som visar en antecknings bok som heter PrepareData och M L kör pipelinen som kallas M L kör pipeline överst med fliken variabler som valts nedan med alternativet att lägga till nya variabler, var och en med ett namn, typ och standardvärde.](media/how-to-cicd-data-ingestion/adf-variables.png)

Pipeline-aktiviteterna kan referera till pipelines-variablerna när de faktiskt använder dem:

![Skärm bild som visar en antecknings bok som heter PrepareData och M L kör pipelinen som kallas M L kör pipeline överst med fliken Inställningar som du valt nedan.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory arbets ytan exponerar ***inte*** pipeline-variabler som standardvärden för Azure Resource Manager-mallar. Arbets ytan använder [standard mal len Parameterisering](../data-factory/continuous-integration-deployment.md#default-parameterization-template) som avgör vilka pipeline-egenskaper som ska visas som Azure Resource Manager mallparametrar. Om du vill lägga till pipeline-variabler i listan uppdaterar du `"Microsoft.DataFactory/factories/pipelines"` avsnittet i [standard Parameterisering-mallen](../data-factory/continuous-integration-deployment.md#default-parameterization-template) med följande kodfragment och placerar resultatet JSON-filen i roten i källmappen:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Detta tvingar Azure Data Factory arbets ytan att lägga till variablerna i parameter listan när användaren klickar på knappen ***publicera*** :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Värdena i JSON-filen är standardvärden som kon figurer ATS i pipeline-definitionen. De förväntas åsidosättas med mål miljöns värden när Azure Resource Manager mal len distribueras.

## <a name="continuous-delivery-cd"></a>Kontinuerlig leverans (CD)

Den kontinuerliga leverans processen tar artefakterna och distribuerar dem till den första mål miljön. Det säkerställer att lösningen fungerar genom att köra tester. Om det lyckas fortsätter den till nästa miljö. 

CD-pipeline för Azure består av flera steg som representerar miljöerna. Varje steg innehåller [distributioner](/azure/devops/pipelines/process/deployment-jobs) och [jobb](/azure/devops/pipelines/process/phases?tabs=yaml) som utför följande steg:

* Distribuera en python-anteckningsbok till Azure Databricks arbets yta
* Distribuera en Azure Data Factory pipeline 
* Köra en pipeline
* Kontrol lera resultatet av data inmatningen

Pipeline-stegen kan konfigureras med [godkännanden](/azure/devops/pipelines/process/approvals?tabs=check-pass) och [portar](/azure/devops/pipelines/release/approvals/gates) som ger ytterligare kontroll över hur distributions processen utvecklas genom en kedja av miljöer.

### <a name="deploy-a-python-notebook"></a>Distribuera en python-anteckningsbok

Följande kodfragment definierar en Azure pipeline- [distribution](/azure/devops/pipelines/process/deployment-jobs) som kopierar en python-anteckningsbok till ett Databricks-kluster:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Artefakterna som skapas av CI kopieras automatiskt till distributions agenten och är tillgängliga i `$(Pipeline.Workspace)` mappen. I det här fallet refererar distributions aktiviteten till `di-notebooks` artefakten som innehåller python-anteckningsboken. I den här [distributionen](/azure/devops/pipelines/process/deployment-jobs) används [Databricks Azure DevOps-tillägget](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) för att kopiera Notebook-filerna till Databricks-arbetsytan.

`Deploy_to_QA`Fasen innehåller en referens till `devops-ds-qa-vg` variabel gruppen som definierats i Azure DevOps-projektet. Stegen i det här steget avser variablerna från den här variabel gruppen (till exempel `$(DATABRICKS_URL)` och `$(DATABRICKS_TOKEN)` ). Idén är att nästa steg (t. ex. `Deploy_to_UAT` ) fungerar med samma variabel namn som definierats i en egen UAT variabel grupp.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Distribuera en Azure Data Factory pipeline

En distributions bara artefakt för Azure Data Factory är en Azure Resource Manager mall. Den kommer att distribueras med ***distributions uppgiften för Azure-resurs gruppen*** , eftersom den visas i följande kodfragment:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Värdet för parametern data filename kommer från `$(DATA_FILE_NAME)` variabeln som definierats i variabel gruppen för frågor och svar. På samma sätt kan alla parametrar som definierats i ***ARMTemplateForFactory.js*** åsidosättas. Om de inte är det används standardvärdena.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Kör pipelinen och kontrol lera resultatet av data inmatningen

Nästa steg är att se till att den distribuerade lösningen fungerar. Följande jobb definition kör en Azure Data Factory pipeline med ett PowerShell- [skript](https://github.com/microsoft/DataOps/tree/master/adf/utils) och kör en python-anteckningsbok i ett Azure Databricks-kluster. Antecknings boken kontrollerar om data har matats in korrekt och validerar resultat data filen med `$(bin_FILE_NAME)` namnet.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Den sista aktiviteten i jobbet kontrollerar resultatet av Notebook-körningen. Om det returnerar ett fel anges statusen för körningen av pipelinen till misslyckades.

## <a name="putting-pieces-together"></a>Placera ihop delar

Den fullständiga CI/CD-pipeline för Azure består av följande steg:
* CI
* Distribuera till frågor och svar
    * Distribuera till Databricks + distribuera till ADF
    * Integrations test

Det innehåller ett antal ***distribuera** _-steg som motsvarar antalet mål miljöer som du har. Varje _ *_Deploy_**-steg innehåller två [distributioner](/azure/devops/pipelines/process/deployment-jobs) som körs parallellt och ett [jobb](/azure/devops/pipelines/process/phases?tabs=yaml) som körs efter distributionen för att testa lösningen i miljön.

En exempel implementering av pipelinen monteras i följande ***yaml*** -kodfragment:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Nästa steg

* [Källkontroll i Azure Data Factory](../data-factory/source-control.md)
* [Kontinuerlig integrering och leverans i Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps för Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)