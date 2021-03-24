---
title: Distribuera modeller med anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en anpassad Docker-bas avbildning för att distribuera Azure Machine Learning-modeller. Medan Azure Machine Learning tillhandahåller en standard bas avbildning åt dig, kan du också använda en egen bas avbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: fb6d9a1a1ad341763c205a11b7a6a9acafda1ac4
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889747"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuera en modell med en anpassad Docker-bas avbildning

Lär dig hur du använder en anpassad Docker-bas avbildning när du distribuerar utbildade modeller med Azure Machine Learning.

Azure Machine Learning kommer att använda en Standards bas Docker-avbildning om ingen anges. Du kan hitta den angivna Docker-avbildningen som används med `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Du kan också använda Azure Machine Learning __miljöer__ för att välja en enskild bas avbildning eller använda en anpassad som du anger.

En bas avbildning används som start punkt när en avbildning skapas för en distribution. Det tillhandahåller underliggande operativ system och komponenter. Distributions processen lägger sedan till ytterligare komponenter, till exempel din modell, Conda-miljö och andra till gångar, till avbildningen.

Normalt skapar du en anpassad bas avbildning när du vill använda Docker för att hantera dina beroenden, bibehålla bättre kontroll över komponent versioner eller spara tid under distributionen. Du kanske också vill installera program vara som krävs av din modell, där installations processen tar lång tid. Installation av program varan när du skapar bas avbildningen innebär att du inte behöver installera den för varje distribution.

> [!IMPORTANT]
> När du distribuerar en modell kan du inte åsidosätta kärn komponenter som webb server eller IoT Edge-komponenter. Dessa komponenter tillhandahåller en känd arbets miljö som testas och stöds av Microsoft.

> [!WARNING]
> Microsoft kanske inte kan hjälpa till att felsöka problem som orsakas av en anpassad avbildning. Om du stöter på problem kan du uppmanas att använda standard avbildningen eller någon av de avbildningar som Microsoft tillhandahåller för att se om problemet är speciellt för din avbildning.

Det här dokumentet är uppdelat i två delar:

* Skapa en anpassad bas avbildning: ger information till administratörer och DevOps för att skapa en anpassad avbildning och konfigurera autentisering till en Azure Container Registry med hjälp av Azure CLI och Machine Learning CLI.
* Distribuera en modell med hjälp av en anpassad bas avbildning: ger information till data vetenskaps-och DevOps/ML-tekniker med anpassade avbildningar när du distribuerar en utbildad modell från python SDK eller ML CLI.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
* [Azure CLI](/cli/azure/install-azure-cli).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* En [Azure Container Registry](../container-registry/index.yml) eller ett annat Docker-register som är tillgängligt på Internet.
* Stegen i det här dokumentet förutsätter att du är van vid att skapa och använda ett __konfigurations objekt för konfigurations__ objekt som en del av modell distributionen. Mer information finns i [var du distribuerar och hur](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Skapa en anpassad bas avbildning

Informationen i det här avsnittet förutsätter att du använder en Azure Container Registry för att lagra Docker-avbildningar. Använd följande check lista när du planerar att skapa anpassade avbildningar för Azure Machine Learning:

* Kommer du att använda Azure Container Registry som skapats för arbets ytan Azure Machine Learning eller en fristående Azure Container Registry?

    När du använder avbildningar som lagras i __behållar registret för arbets ytan__ behöver du inte autentisera till registret. Autentiseringen hanteras av arbets ytan.

    > [!WARNING]
    > Azure Container Registry för din arbets yta __skapas första gången du tränar eller distribuerar en modell__ med hjälp av arbets ytan. Om du har skapat en ny arbets yta, men inte tränat eller skapat någon modell, så finns det inga Azure Container Registry för arbets ytan.

    När du använder avbildningar som lagras i ett __fristående behållar register__ måste du konfigurera ett huvud namn för tjänsten som har minst Läs behörighet. Du anger sedan tjänstens huvud namns-ID (username) och lösen ord för alla som använder avbildningar från registret. Undantaget är om du gör att behållar registret är offentligt tillgängligt.

    Information om hur du skapar en privat Azure Container Registry finns i [skapa ett privat container Registry](../container-registry/container-registry-get-started-azure-cli.md).

    Information om hur du använder tjänstens huvud namn med Azure Container Registry finns [Azure Container Registry autentisering med tjänstens huvud namn](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry-och avbildnings information: Ange avbildnings namnet för alla som behöver använda det. En bild `myimage` som heter, lagrad i ett register med namnet `myregistry` , refereras till som `myregistry.azurecr.io/myimage` när du använder avbildningen för modell distribution

### <a name="image-requirements"></a>Avbildningskrav

Azure Machine Learning stöder endast Docker-avbildningar som tillhandahåller följande program vara:
* Ubuntu 16,04 eller senare.
* Conda 4.5. # eller senare.
* Python 3.6 +.

Om du vill använda data uppsättningar måste du installera paketet libsäkring-dev. Se också till att installera alla användar utrymmes paket som du kan behöva.

Azure ML upprätthåller en uppsättning processor-och GPU-grundavbildningar som publiceras till Microsoft Container Registry som du kan använda (eller referens) i stället för att skapa en egen anpassad avbildning. Om du vill se Dockerfiles för dessa avbildningar kan du läsa mer i [Azure/azureml-containers GitHub-](https://github.com/Azure/AzureML-Containers) lagringsplatsen.

För GPU-avbildningar erbjuder Azure ML för närvarande både cuda9 och cuda10 Base-avbildningar. De viktigaste beroendena som är installerade i de här bas avbildningarna är:

| Beroenden | IntelMPI-CPU | OpenMPI-CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| MPI | intelmpi = = 2018.3.222 |openmpi = = 3.1.2 |intelmpi = = 2018.3.222| openmpi = = 3.1.2 |
| CUDA | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU-avbildningarna skapas från Ubuntu 16.04. GPU-avbildningarna för cuda9 är byggda från NVIDIA/CUDA: 9.0-cudnn7-devel-Ubuntu 16.04. GPU-avbildningarna för cuda10 är byggda från NVIDIA/CUDA: 10.0-cudnn7-devel-Ubuntu 16.04.
<a id="getname"></a>

> [!IMPORTANT]
> När du använder anpassade Docker-avbildningar, rekommenderar vi att du fäster paket versioner för att bättre garantera reproducerbarhet.

### <a name="get-container-registry-information"></a>Hämta information om container registret

I det här avsnittet får du lära dig hur du hämtar namnet på Azure Container Registry för Azure Machine Learning arbets ytan.

> [!WARNING]
> Azure Container Registry för din arbets yta __skapas första gången du tränar eller distribuerar en modell__ med hjälp av arbets ytan. Om du har skapat en ny arbets yta, men inte tränat eller skapat någon modell, så finns det inga Azure Container Registry för arbets ytan.

Om du redan har tränat eller distribuerat modeller med hjälp av Azure Machine Learning har ett behållar register skapats för din arbets yta. Använd följande steg för att hitta namnet på det här behållar registret:

1. Öppna ett nytt gränssnitt eller kommando tolk och Använd följande kommando för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Följ anvisningarna för att autentisera till prenumerationen.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Använd följande kommando för att Visa behållar registret för arbets ytan. Ersätt `<myworkspace>` med namnet på din Azure Machine Learning-arbetsyta. Ersätt `<resourcegroup>` med Azure-resurs gruppen som innehåller din arbets yta:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Den information som returneras liknar följande text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`Värdet är namnet på Azure Container Registry för din arbets yta.

### <a name="build-a-custom-base-image"></a>Bygg en anpassad bas avbildning

Stegen i det här avsnittet beskriver hur du skapar en anpassad Docker-avbildning i din Azure Container Registry. Exempel-dockerfiles finns i [Azure/azureml-containers](https://github.com/Azure/AzureML-Containers) GitHub lagrings platsen).

1. Skapa en ny textfil med namnet `Dockerfile` och Använd följande text som innehåll:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Använd följande för att autentisera till Azure Container Registry från ett gränssnitt eller kommando tolken. Ersätt `<registry_name>` med namnet på det behållar register som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Om du vill ladda upp Dockerfile och bygga den använder du följande kommando. Ersätt `<registry_name>` med namnet på det behållar register som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > I det här exemplet används en-tagg för `:v1` avbildningen. Om ingen tagg anges används en-tagg `:latest` .

    Under skapande processen strömmas information tillbaka till kommando raden. Om versionen lyckas visas ett meddelande som liknar följande text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Mer information om hur du skapar avbildningar med en Azure Container Registry finns i [skapa och köra en behållar avbildning med hjälp av Azure Container Registry uppgifter](../container-registry/container-registry-quickstart-task-cli.md)

Mer information om hur du överför befintliga avbildningar till en Azure Container Registry finns i [skicka din första avbildning till ett privat Docker-behållarobjekt](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Använd en anpassad bas avbildning

Om du vill använda en anpassad avbildning behöver du följande information:

* __Avbildningens namn__. Till exempel `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` är sökvägen till en enkel Docker-avbildning från Microsoft.

    > [!IMPORTANT]
    > För anpassade avbildningar som du har skapat, se till att ta med alla Taggar som användes med avbildningen. Om din bild till exempel skapades med en speciell tagg, till exempel `:v1` . Om du inte använde en speciell tagg när du skapade avbildningen användes en-tagg `:latest` .

* Om avbildningen finns i ett __privat lager__ behöver du följande information:

    * Register __adressen__. Till exempel `myregistry.azureecr.io`.
    * Ett __användar namn__ och __lösen ord__ för tjänstens huvud namn som har Läs behörighet till registret.

    Om du inte har den här informationen kan du prata med administratören för den Azure Container Registry som innehåller din avbildning.

### <a name="publicly-available-base-images"></a>Offentligt tillgängliga bas avbildningar

Microsoft tillhandahåller flera Docker-avbildningar på en offentligt tillgänglig lagrings plats som kan användas med stegen i det här avsnittet:

| Bild | Beskrivning |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Kärn avbildning för Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Innehåller ONNX runtime för CPU inferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Innehåller ONNX Runtime och CUDA för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Innehåller ONNX Runtime och TensorRT för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Innehåller ONNX Runtime och Open för utformning av Intel <sup></sup> vision Accelerator baserat på Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Innehåller ONNX Runtime och Open för Intel <sup></sup> Movidius<sup>TM</sup> USB-käppar |

Mer information om ONNX för körnings bas avbildningar finns i [avsnittet ONNX runtime Dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) i GitHub lagrings platsen.

> [!TIP]
> Eftersom dessa bilder är offentligt tillgängliga behöver du inte ange någon adress, användar namn eller lösen ord när du använder dem.

Mer information finns i [Azure Machine Learning containers](https://github.com/Azure/AzureML-Containers) -lagringsplatsen på GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Använd en avbildning med Azure Machine Learning SDK

Om du vill använda en avbildning som lagras i **Azure Container Registry för din arbets yta**, eller ett **behållar register som är offentligt tillgängligt**, anger [du följande](/python/api/azureml-core/azureml.core.environment.environment) miljöattribut:

+ `docker.enabled=True`
+ `docker.base_image`: Ange till registret och sökvägen till avbildningen.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Om du vill använda en avbildning från ett __privat behållar register__ som inte finns i din arbets yta, måste du använda `docker.base_image_registry` för att ange adressen till lagrings platsen och ett användar namn och lösen ord:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Du måste lägga till azureml-default med version >= 1.0.45 som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. Du måste också ange inferencing_stack_version egenskapen för miljön till "senaste", så installeras vissa apt-paket som krävs av webb tjänsten. 

När du har definierat miljön använder du den med ett [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) -objekt för att definiera den miljö för miljön och webb tjänsten som ska köras.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Nu kan du fortsätta med distributionen. Följande kodfragment skulle till exempel distribuera en webb tjänst lokalt med hjälp av konfigurations konfigurationen och den anpassade avbildningen:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om distribution finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Använd en avbildning med Machine Learning CLI

> [!IMPORTANT]
> För närvarande kan Machine Learning CLI använda bilder från Azure Container Registry för din arbets yta eller offentligt tillgängliga databaser. Det går inte att använda bilder från fristående privata register.

Innan du distribuerar en modell med hjälp av Machine Learning CLI skapar du en [miljö](/python/api/azureml-core/azureml.core.environment.environment) som använder den anpassade avbildningen. Skapa sedan en konfigurations fil för härledning som hänvisar till miljön. Du kan också definiera miljön direkt i konfigurations filen för konfigurations filen. Följande JSON-dokument visar hur du refererar till en avbildning i ett offentligt behållar register. I det här exemplet definieras miljön som infogad:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Den här filen används med `az ml model deploy` kommandot. `--ic`Parametern används för att ange konfigurations filen för konfigurations filen.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Mer information om hur du distribuerar en modell med hjälp av ML CLI finns i avsnittet "modell registrering, profilering och distribution" i CLI- [tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artikel.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [var du distribuerar och hur](how-to-deploy-and-where.md).
* Lär dig hur du [tränar och distribuerar maskin inlärnings modeller med Azure-pipeliner](/azure/devops/pipelines/targets/azure-machine-learning).