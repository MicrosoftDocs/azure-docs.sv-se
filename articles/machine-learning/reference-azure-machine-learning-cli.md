---
title: Installera & Använd Azure Machine Learning CLI
description: Lär dig hur du använder Azure CLI-tillägget för ML för att skapa & hantera resurser, till exempel din arbets yta, data uppsättningar, data uppsättningar, pipelines, modeller och distributioner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: a4adb5bff80f1ab216a39fa773e027670b9e6509
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212696"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installera & Använd CLI-tillägget för Azure Machine Learning


Azure Machine Learning CLI är ett tillägg till [Azure CLI](/cli/azure/), ett plattforms oberoende kommando rads gränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning. Det gör att du kan automatisera dina Machine Learning-aktiviteter. Följande lista innehåller några exempel åtgärder som du kan göra med CLI-tillägget:

+ Köra experiment för att skapa maskin inlärnings modeller

+ Registrera maskin inlärnings modeller för kund användning

+ Paketera, distribuera och spåra livs cykeln för dina Machine Learning-modeller

CLI är inte en ersättning för Azure Machine Learning SDK. Det är ett komplett verktyg som är optimerat för att hantera mycket parameterstyrda uppgifter som passar bra för automatisering.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure-prenumeration för att kunna använda CLI. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö** behöver du [Azure CLI](/cli/azure/install-azure-cli).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="full-reference-docs"></a>Fullständiga referens dokument

Hitta [fullständiga referens dokument för Azure-CLI-ml-tillägget för Azure CLI](/cli/azure/ext/azure-cli-ml/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Cloud Shell autentiserar automatiskt dig med det konto som du loggar in i din Azure-prenumeration.

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Den mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. För att autentisera interaktivt, öppna en kommando rad eller Terminal och Använd följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

För andra metoder för autentisering, se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Installera tillägget

Om du vill installera Machine Learning CLI-tillägget använder du följande kommando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Exempel på filer som du kan använda med kommandona nedan hittar du [här](https://aka.ms/azml-deploy-cloud).

När du uppmanas väljer `y` du att installera tillägget.

Kontrol lera att tillägget har installerats genom att använda följande kommando för att visa en lista över ML-Specific-kommandon:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Uppdatera tillägget

Om du vill uppdatera Machine Learning CLI-tillägget använder du följande kommando:

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

+ Om du inte redan har en, skapar du en resurs grupp:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Skapa en Azure Machine Learning arbets yta:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Mer information finns i [skapa en AZ ml-arbetsyta](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create).

+ Koppla en arbets ytans konfiguration till en mapp för att aktivera CLI-kontext medvetenhet.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Det här kommandot skapar en `.azureml` under katalog som innehåller exempel på runconfig-och Conda-miljöfiler. Den innehåller också en `config.json` fil som används för att kommunicera med din Azure Machine Learning-arbetsyta.

    Mer information finns i [AZ ml Folder Attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).

+ Anslut en Azure Blob-behållare som ett data lager.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Mer information finns i [AZ ml data lager Attach-BLOB](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Ladda upp filer till ett data lager.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Mer information finns i [AZ ml data lager uppladdning](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload).

+ Anslut ett AKS-kluster som ett beräknings mål.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Mer information finns i [AZ ml computetarget Attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Beräknings kluster

+ Skapa ett nytt hanterat beräknings kluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Skapa ett nytt hanterat beräknings kluster med hanterad identitet

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

Mer information finns i [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Beräkninsinstans
Hantera beräknings instanser.  I alla exempel nedan är namnet på beräknings instansen **CPU**

+ Skapa en ny computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Mer information finns i [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Stoppa en computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Mer information finns i [AZ ml computetarget computeinstance Stop](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

+ Starta en computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Mer information finns i [AZ ml computetarget computeinstance start](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

+ Starta om en computeinstance.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Mer information finns i [AZ ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Ta bort en computeinstance.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Mer information finns i [AZ ml computetarget Delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Köra experiment

* Starta en körning av experimentet. När du använder det här kommandot anger du namnet på runconfig-filen (texten före \* . runconfig om du tittar på ditt fil system) mot parametern-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Kommandot skapar en `.azureml` under katalog som innehåller två exempel på runconfig-filer. 
    >
    > Om du har ett Python-skript som skapar ett kör konfigurations objekt program mässigt kan du använda [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) för att spara det som en RunConfig-fil.
    >
    > Du hittar det fullständiga runconfig-schemat i den här [JSON-filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schemat är självdokumenterande genom `description` nyckeln för varje objekt. Det finns dessutom uppräkningar för möjliga värden och ett mallkod i slutet.

    Mer information finns i [AZ ml Run Submit-script](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script).

* Visa en lista över experiment:

    ```azurecli-interactive
    az ml experiment list
    ```

    Mer information finns i [AZ ml experiment List](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>HyperDrive körning

Du kan använda HyperDrive med Azure CLI för att utföra parameter justerings körningar. Börja med att skapa en HyperDrive-konfigurations fil i följande format. Se [Justera egenskaper för en modell](how-to-tune-hyperparameters.md) artikel för information om parametrar för parameter justering.

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

Lägg till den här filen tillsammans med konfigurationsfilerna för körning. Skicka sedan en HyperDrive-körning med:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observera avsnittet *argument* i runconfig och *parameter utrymme* i HyperDrive config. De innehåller kommando rads argument som ska skickas till övnings skriptet. Värdet i runconfig förblir detsamma för varje iteration, medan intervallet i HyperDrive-config upprepas. Ange inte samma argument i båda filerna.

## <a name="dataset-management"></a>Hantering av data uppsättningar

Följande kommandon visar hur du arbetar med data uppsättningar i Azure Machine Learning:

+ Registrera en data uppsättning:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Information om formatet på den JSON-fil som används för att definiera data uppsättningen använder du `az ml dataset register --show-template` .

    Mer information finns i [AZ ml data data register](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register).

+ Lista alla data uppsättningar på en arbets yta:

    ```azurecli-interactive
    az ml dataset list
    ```

    Mer information finns i [AZ ml dataset List](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list).

+ Hämta information om en data uppsättning:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Mer information finns i [AZ ml data uppsättning show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show).

+ Avregistrera en data uppsättning:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Mer information finns i [avregistrering av AZ ml-datauppsättning](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Miljöhantering

Följande kommandon visar hur du skapar, registrerar och listar Azure Machine Learning [miljöer](how-to-configure-environment.md) för din arbets yta:

+ Skapa ramverk-filer för en miljö:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Mer information finns i [AZ ml Environment Autogenerera](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrera en miljö:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Mer information finns i [AZ ml miljö register](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register).

+ Lista registrerade miljöer:

    ```azurecli-interactive
    az ml environment list
    ```

    Mer information finns i [AZ ml Environment List](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list).

+ Ladda ned en registrerad miljö:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Mer information finns i [AZ ml-miljö hämtning](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Konfigurations schema för miljö

Om du använde `az ml environment scaffold` kommandot genererar det en mallfil `azureml_environment.json` som kan ändras och används för att skapa anpassade miljö konfigurationer med cli. Objektet på den översta nivån mappas löst till [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) klassen i python SDK. 

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

Följande tabell innehåller information om varje översta fält i JSON-filen, dess typ och en beskrivning. Om en objekt typ är länkad till en klass från python SDK finns det en lös 1:1-matchning mellan varje JSON-fält och namnet på den offentliga variabeln i python-klassen. I vissa fall kan fältet mappas till ett konstruktor-argument i stället för en klass variabel. Till exempel `environmentVariables` mappas fältet till `environment_variables` variabeln i- [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) klassen.

| JSON-fält | Typ | Description |
|---|---|---|
| `name` | `string` | Miljöns namn. Starta inte namn med **Microsoft** eller **azureml**. |
| `version` | `string` | Version av miljön. |
| `environmentVariables` | `{string: string}` | En hash-mappning av miljö variabel namn och-värden. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection?preserve-view=true&view=azure-ml-py)Hat definierar den python-miljö och-tolk som ska användas på mål beräknings resurser. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection?preserve-view=true&view=azure-ml-py) | Definierar inställningar för att anpassa Docker-avbildningen som är byggd för miljöns specifikationer. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection?preserve-view=true&view=azure-ml-py) | I avsnittet konfigureras Spark-inställningar. Den används endast när Framework är inställt på PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection?preserve-view=true&view=azure-ml-py) | Konfigurerar beroenden för Databricks-bibliotek. |
| `inferencingStackVersion` | `string` | Anger den inferencing stack-version som läggs till i avbildningen. Lämna det här fältet för att undvika att lägga till en inferencing-stack `null` . Giltigt värde: "senaste". |

## <a name="ml-pipeline-management"></a>ML pipeline-hantering

Följande kommandon visar hur du arbetar med maskin inlärnings pipeliner:

+ Skapa en pipeline för maskin inlärning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Mer information finns i [AZ ml pipeline Create](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create).

    Mer information om pipeline YAML-filen finns i [definiera pipelines i Machine Learning i yaml](reference-pipeline-yaml.md).

+ Kör en pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Mer information finns i [AZ ml Run Submit-pipeline](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Mer information om pipeline YAML-filen finns i [definiera pipelines i Machine Learning i yaml](reference-pipeline-yaml.md).

+ Schemalägg en pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Mer information finns i [AZ ml pipeline Create-Schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Mer information om YAML-filen för pipelinen finns i [definiera pipelines för maskin inlärning i yaml](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Modell registrering, profilering, distribution

Följande kommandon visar hur du registrerar en utbildad modell och sedan distribuerar den som en produktions tjänst:

+ Registrera en modell med Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Mer information finns i [AZ ml modell register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register).

+ **Valfritt** Profilera din modell för att få optimala processor-och minnes värden för distribution.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Mer information finns i [AZ ml modell Profile](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuera din modell till AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Mer information om schema för konfigurations filen för konfigurations filen finns i [konfigurations schema för energischemat](#inferenceconfig).
    
    Mer information om schemat för distributions konfigurations filen finns i [schema för distributions konfiguration](#deploymentconfig).

    Mer information finns i [AZ ml modell Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schema för konfigurations härledning

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schema för distributions konfiguration

### <a name="local-deployment-configuration-schema"></a>Konfigurations schema för lokal distribution

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Konfigurations schema för distribution av Azure Container instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Konfigurations schema för Azure Kubernetes service-distribution

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Nästa steg

* [Kommando referens för Machine Learning CLI-tillägget](/cli/azure/ext/azure-cli-ml/ml).

* [Träna och distribuera maskin inlärnings modeller med Azure-pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
