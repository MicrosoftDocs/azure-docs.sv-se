---
title: Installera & använda Azure Machine Learning CLI
description: Lär dig hur du använder Azure CLI-tillägget för ML för att skapa & hantera resurser som din arbetsyta, datalager, datauppsättningar, pipelines, modeller och distributioner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8e912e141ab769f3121fcccdecb9c3952920572f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516220"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installera & använda CLI-tillägget för Azure Machine Learning


Den Azure Machine Learning CLI är ett tillägg till [Azure CLI](/cli/azure/), ett plattformsoberoende kommandoradsgränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning. Det gör att du kan automatisera dina maskininlärningsaktiviteter. Följande lista innehåller några exempel på åtgärder som du kan utföra med CLI-tillägget:

+ Köra experiment för att skapa maskininlärningsmodeller

+ Registrera maskininlärningsmodeller för kundanvändning

+ Paketera, distribuera och spåra livscykeln för dina maskininlärningsmodeller

CLI ersätter inte Azure Machine Learning SDK. Det är ett kompletterande verktyg som är optimerat för att hantera mycket parametriserade uppgifter som passar bra för automatisering.

## <a name="prerequisites"></a>Förutsättningar

* Om du vill använda CLI måste du ha en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet **från din lokala** miljö behöver du Azure [CLI](/cli/azure/install-azure-cli).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), nås CLI via webbläsaren och finns i molnet.

## <a name="full-reference-docs"></a>Fullständiga referensdokument

Hitta de [fullständiga referensdokumenten för azure-cli-ml-tillägget för Azure CLI.](/cli/azure/ml/)

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Cloud Shell autentiserar dig automatiskt med det konto som du loggar in på din Azure-prenumeration.

Du kan autentisera till din Azure-prenumeration från CLI på flera olika sätt. Det mest grundläggande är att autentisera interaktivt med hjälp av en webbläsare. Om du vill autentisera interaktivt öppnar du en kommandorad eller terminal och använder följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommandoraden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Andra metoder för autentisering finns i [Logga in med Azure CLI.](/cli/azure/authenticate-azure-cli)

## <a name="install-the-extension"></a>Installera tillägget

Tillägget installeras automatiskt första gången du försöker använda ett kommando som börjar med `az ml` .

## <a name="update-the-extension"></a>Uppdatera tillägget

Om du vill Machine Learning CLI-tillägget använder du följande kommando:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Ta bort tillägget

Om du vill ta bort CLI-tillägget använder du följande kommando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resurshantering

Följande kommandon visar hur du använder CLI för att hantera resurser som används av Azure Machine Learning.

+ Om du inte redan har en, skapar du en resursgrupp:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Skapa en Azure Machine Learning arbetsyta:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Mer information finns i [az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create).

+ Koppla en arbetsytekonfiguration till en mapp för att aktivera CLI-kontextuell medvetenhet.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Det här kommandot skapar `.azureml` en underkatalog som innehåller exempel på runconfig- och conda-miljöfiler. Den innehåller också en `config.json` fil som används för att kommunicera med Azure Machine Learning arbetsyta.

    Mer information finns i [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).

+ Koppla en Azure-blobcontainer som ett datalager.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Mer information finns i [az ml datastore attach-blob](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Ladda upp filer till ett datalager.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Mer information finns i [az ml datastore upload](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload).

+ Koppla ett AKS-kluster som ett beräkningsmål.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Mer information finns i [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Beräkningskluster

+ Skapa ett nytt hanterat beräkningskluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Skapa ett nytt hanterat beräkningskluster med hanterad identitet

  + Användartilldelad hanterad identitet

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Systemtilldelad hanterad identitet

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Lägg till en hanterad identitet i ett befintligt kluster:

    + Användartilldelad hanterad identitet
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Systemtilldelad hanterad identitet

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Mer information finns i [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Beräkninsinstans
Hantera beräkningsinstanser.  I alla exemplen nedan är namnet på beräkningsinstansen **cpu**

+ Skapa en ny beräkningsinstans.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Mer information finns i [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Stoppa en beräkningsinstans.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Mer information finns i [az ml computetarget computeinstance stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

+ Starta en beräkningsinstans.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Mer information finns i [az ml computetarget computeinstance start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

+ Starta om en beräkningsinstans.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Mer information finns i [az ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Ta bort en beräkningsinstans.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Mer information finns i [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Köra experiment

* Starta en körning av experimentet. När du använder det här kommandot anger du namnet på runconfig-filen (texten före .runconfig om du tittar på filsystemet) mot \* parametern -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Kommandot `az ml folder attach` skapar en `.azureml` underkatalog som innehåller två runconfig-exempelfiler. 
    >
    > Om du har ett Python-skript som skapar ett körningskonfigurationsobjekt programmatiskt kan du använda [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) för att spara det som en runconfig-fil.
    >
    > Det fullständiga runconfig-schemat finns i den här [JSON-filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schemat dokumenterar sig själv genom nyckeln `description` för varje objekt. Dessutom finns det uppräkningar för möjliga värden och ett mallfragment i slutet.

    Mer information finns i [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script).

* Visa en lista över experiment:

    ```azurecli-interactive
    az ml experiment list
    ```

    Mer information finns i [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>HyperDrive-körning

Du kan använda HyperDrive med Azure CLI för att utföra parameterjusteringskörningar. Skapa först en HyperDrive-konfigurationsfil i följande format. Mer [information om parametrar för justering av hyperparametrar](how-to-tune-hyperparameters.md) finns i artikeln Finjustera hyperparametrar.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Lägg till den här filen tillsammans med körningskonfigurationsfilerna. Skicka sedan en HyperDrive-körning med:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observera avsnittet *argument* i runconfig och *parameterutrymmet i* HyperDrive-konfiguration. De innehåller kommandoradsargumenten som ska skickas till träningsskriptet. Värdet i runconfig förblir detsamma för varje iteration, medan intervallet i HyperDrive-konfiguration itereras över. Ange inte samma argument i båda filerna.

## <a name="dataset-management"></a>Datauppsättningshantering

Följande kommandon visar hur du arbetar med datauppsättningar i Azure Machine Learning:

+ Registrera en datauppsättning:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Om du vill ha information om formatet för JSON-filen som används för att definiera datauppsättningen använder du `az ml dataset register --show-template` .

    Mer information finns i [az ml dataset register](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register).

+ Visa en lista över alla datauppsättningar på en arbetsyta:

    ```azurecli-interactive
    az ml dataset list
    ```

    Mer information finns i [az ml dataset list](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list).

+ Hämta information om en datauppsättning:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Mer information finns i [az ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show).

+ Avregistrera en datauppsättning:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Mer information finns i [az ml dataset unregister](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Miljöhantering

Följande kommandon visar hur du skapar, registrerar och listar Azure Machine Learning [miljöer för](how-to-configure-environment.md) din arbetsyta:

+ Skapa scaffolding-filer för en miljö:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Mer information finns i [az ml environment scaffold](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrera en miljö:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Mer information finns i [az ml environment register](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register).

+ Lista registrerade miljöer:

    ```azurecli-interactive
    az ml environment list
    ```

    Mer information finns i [az ml environment list](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list).

+ Ladda ned en registrerad miljö:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Mer information finns i [az ml environment download](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schema för miljökonfiguration

Om du använde kommandot genereras en mallfil som kan ändras och användas för att `az ml environment scaffold` `azureml_environment.json` skapa anpassade miljökonfigurationer med CLI. Objektet på den översta nivån mappar löst till [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) klassen i Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

I följande tabell beskrivs varje fält på den översta nivån i JSON-filen, dess typ och en beskrivning. Om en objekttyp är länkad till en klass från Python SDK finns det en lös matchning på 1:1 mellan varje JSON-fält och det offentliga variabelnamnet i Python-klassen. I vissa fall kan fältet mappas till ett konstruktorargument i stället för en klassvariabel. Fältet mappar till `environmentVariables` exempel till `environment_variables` variabeln i klassen [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) .

| JSON-fält | Typ | Description |
|---|---|---|
| `name` | `string` | Namnet på miljön. Starta inte namn med **Microsoft** eller **AzureML**. |
| `version` | `string` | Versionen av miljön. |
| `environmentVariables` | `{string: string}` | En hash-karta över miljövariabelnamn och värden. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)hat definierar Python-miljön och tolken som ska användas på målbearbetningsresursen. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Definierar inställningar för att anpassa Docker-avbildningen som skapats enligt miljöns specifikationer. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | Avsnittet konfigurerar Spark-inställningar. Det används bara när ramverket är inställt på PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Konfigurerar Databricks-biblioteksberoenden. |
| `inferencingStackVersion` | `string` | Anger vilken inferencingstackversion som lagts till i avbildningen. Om du vill undvika att lägga till en inferencingstack lämnar du det här fältet `null` . Giltigt värde: "latest". |

## <a name="ml-pipeline-management"></a>ML-pipelinehantering

Följande kommandon visar hur du arbetar med pipelines för maskininlärning:

+ Skapa en pipeline för maskininlärning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Mer information finns i [az ml pipeline create](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create).

    Mer information om YAML-pipelinefilen finns i Definiera [maskininlärningspipelines i YAML.](reference-pipeline-yaml.md)

+ Kör en pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Mer information finns i [az ml run submit-pipeline](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Mer information om YAML-pipelinefilen finns i Definiera [maskininlärningspipelines i YAML.](reference-pipeline-yaml.md)

+ Schemalägg en pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Mer information finns i [az ml pipeline create-schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Mer information om YAML-filen med pipelineschemat finns i [Definiera maskininlärningspipelines i YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Modellregistrering, profilering, distribution

Följande kommandon visar hur du registrerar en tränad modell och sedan distribuerar den som en produktionstjänst:

+ Registrera en modell med Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Mer information finns i [az ml model register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register).

+ **VALFRITT** Profilera din modell för att få optimala processor- och minnesvärden för distribution.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Mer information finns i [az ml model profile](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuera din modell till AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Mer information om schemat för inferenskonfigurationsfilen finns i [Inference configuration schema](#inferenceconfig).
    
    Mer information om schemat för distributionskonfigurationsfilen finns i [Schema för distributionskonfiguration.](#deploymentconfig)

    Mer information finns i [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Konfigurationsschema för slutsatsledning

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schema för distributionskonfiguration

### <a name="local-deployment-configuration-schema"></a>Konfigurationsschema för lokal distribution

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Konfigurationsschema för Azure Container Instance-distribution 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service schema för distributionskonfiguration

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Nästa steg

* [Kommandoreferens för Machine Learning CLI-tillägget](/cli/azure/ext/azure-cli-ml/ml).

* [Träna och distribuera maskininlärningsmodeller med Hjälp av Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
