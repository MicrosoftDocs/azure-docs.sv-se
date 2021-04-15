---
title: Distribuera modeller med anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en anpassad Docker-basavbildning för att distribuera Azure Machine Learning modeller. Även Azure Machine Learning tillhandahåller en standardbasavbildning åt dig, kan du också använda din egen basavbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: f621bb2a7d4543620d22ab85fb8b44752c9989ac
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376264"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuera en modell med hjälp av en anpassad Docker-basavbildning

Lär dig hur du använder en anpassad Docker-basavbildning när du distribuerar tränade modeller med Azure Machine Learning.

Azure Machine Learning använder en docker-standardavbildning om ingen har angetts. Du hittar den specifika Docker-avbildningen som används med `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Du kan också använda Azure Machine Learning __miljöer för__ att välja en specifik basavbildning eller använda en anpassad som du anger.

En basavbildning används som startpunkt när en avbildning skapas för en distribution. Den tillhandahåller det underliggande operativsystemet och komponenterna. Distributionsprocessen lägger sedan till ytterligare komponenter, till exempel din modell, Conda-miljö och andra tillgångar, i avbildningen.

Normalt skapar du en anpassad basavbildning när du vill använda Docker för att hantera dina beroenden, behålla bättre kontroll över komponentversioner eller spara tid under distributionen. Du kanske också vill installera programvara som krävs av din modell, där installationsprocessen tar lång tid. Om du installerar programvaran när du skapar basavbildningen behöver du inte installera den för varje distribution.

> [!IMPORTANT]
> När du distribuerar en modell kan du inte åsidosätta kärnkomponenter, till exempel webbservern eller IoT Edge komponenter. Dessa komponenter tillhandahåller en känd arbetsmiljö som har testats och stöds av Microsoft.

> [!WARNING]
> Microsoft kanske inte kan hjälpa till att felsöka problem som orsakas av en anpassad avbildning. Om du stöter på problem kan du bli ombedd att använda standardavbildningen eller någon av de avbildningar som Microsoft tillhandahåller för att se om problemet är specifikt för din avbildning.

Det här dokumentet är uppdelat i två delar:

* Skapa en anpassad basavbildning: Ger information till administratörer och DevOps om hur du skapar en anpassad avbildning och konfigurerar autentisering till en Azure Container Registry med hjälp av Azure CLI och Machine Learning CLI.
* Distribuera en modell med hjälp av en anpassad basavbildning: Ger information till dataforskare och DevOps/ML-tekniker om hur de använder anpassade avbildningar när de distribuerar en tränad modell från Python SDK eller ML CLI.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [Skapa en arbetsyta.](how-to-manage-workspace.md)
* [Sdk Azure Machine Learning för den här Azure Machine Learning.](/python/api/overview/azure/ml/install) 
* [Azure CLI](/cli/azure/install-azure-cli).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* En [Azure Container Registry](../container-registry/index.yml) eller ett annat Docker-register som är tillgängligt på Internet.
* Stegen i det här dokumentet förutsätter att du är bekant med att skapa och använda ett __konfigurationsobjekt för slutsatsledning__ som en del av modelldistributionen. Mer information finns i [Var du distribuerar och hur](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Skapa en anpassad basavbildning

Informationen i det här avsnittet förutsätter att du använder en Azure Container Registry för att lagra Docker-avbildningar. Använd följande checklista när du planerar att skapa anpassade avbildningar för Azure Machine Learning:

* Kommer du att använda Azure Container Registry som skapats Azure Machine Learning arbetsytan eller en fristående Azure Container Registry?

    När du använder __avbildningar som lagras i containerregistret för__ arbetsytan behöver du inte autentisera till registret. Autentisering hanteras av arbetsytan.

    > [!WARNING]
    > Den Azure Container Registry för din arbetsyta __skapas första gången du tränar eller distribuerar en modell__ med arbetsytan. Om du har skapat en ny arbetsyta, men inte har tränat eller skapat en modell, Azure Container Registry inte finnas för arbetsytan.

    När du använder avbildningar __som lagras i ett__ fristående containerregister måste du konfigurera ett huvudnamn för tjänsten som har minst läsbehörighet. Sedan anger du ID för tjänstens huvudnamn (användarnamn) och lösenord för alla som använder avbildningar från registret. Undantaget är om du gör containerregistret offentligt tillgängligt.

    Information om hur du skapar en Azure Container Registry finns i [Skapa ett privat containerregister.](../container-registry/container-registry-get-started-azure-cli.md)

    Information om hur du använder tjänstens huvudnamn med Azure Container Registry finns [i Azure Container Registry autentisering med tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry och bildinformation: Ange avbildningens namn till alla som behöver använda den. En avbildning med namnet , som lagras i ett register med namnet , refereras till som när du `myimage` `myregistry` använder `myregistry.azurecr.io/myimage` avbildningen för modelldistribution

### <a name="image-requirements"></a>Avbildningskrav

Azure Machine Learning stöder endast Docker-avbildningar som tillhandahåller följande programvara:
* Ubuntu 16.04 eller högre.
* Conda 4.5.# eller högre.
* Python 3.6+.

Om du vill använda datauppsättningar installerar du paketet libfuse-dev. Se även till att installera eventuella användarutrymmespaket som du kan behöva.

Azure ML har en uppsättning CPU- och GPU-basavbildningar som publicerats till Microsoft Container Registry som du kan använda (eller referera till) i stället för att skapa en egen anpassad avbildning. Om du vill se Dockerfiles för dessa avbildningar kan du gå till [GitHub-lagringsplatsen Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

För GPU-avbildningar erbjuder Azure ML för närvarande både cuda9- och cuda10-basavbildningar. De viktigaste beroendena som installeras i dessa basavbildningar är:

| Beroenden | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| Mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| Cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| etl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU-avbildningarna är byggda från ubuntu16.04. GPU-avbildningarna för cuda9 är byggda från nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04. GPU-avbildningarna för cuda10 är byggda från nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04.
<a id="getname"></a>

> [!IMPORTANT]
> När du använder anpassade Docker-avbildningar rekommenderar vi att du fäster paketversioner för att bättre säkerställa reproducerbarhet.

### <a name="get-container-registry-information"></a>Hämta information om containerregister

I det här avsnittet lär du dig att hämta namnet på Azure Container Registry för din Azure Machine Learning arbetsyta.

> [!WARNING]
> Den Azure Container Registry för din arbetsyta __skapas första gången du tränar eller distribuerar en modell__ med hjälp av arbetsytan. Om du har skapat en ny arbetsyta, men inte har tränat eller skapat en modell, Azure Container Registry inte finnas för arbetsytan.

Om du redan har tränat eller distribuerat modeller med Azure Machine Learning skapas ett containerregister för din arbetsyta. Använd följande steg för att hitta namnet på det här containerregistret:

1. Öppna ett nytt gränssnitt eller en kommandotolk och använd följande kommando för att autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Följ anvisningarna för att autentisera till prenumerationen.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Använd följande kommando för att visa en lista över arbetsytans containerregister. Ersätt `<myworkspace>` med namnet Azure Machine Learning arbetsytan. Ersätt `<resourcegroup>` med den Azure-resursgrupp som innehåller din arbetsyta:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Informationen som returneras liknar följande text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Värdet `<registry_name>` är namnet på den Azure Container Registry för din arbetsyta.

### <a name="build-a-custom-base-image"></a>Skapa en anpassad basavbildning

Stegen i det här avsnittet går igenom hur du skapar en anpassad Docker-avbildning i Azure Container Registry. Exempel på dockerfiles finns på [GitHub-lagringsplatsen Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

1. Skapa en ny textfil med `Dockerfile` namnet och använd följande text som innehåll:

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

2. Från ett gränssnitt eller en kommandotolk använder du följande för att autentisera till Azure Container Registry. Ersätt med `<registry_name>` namnet på det containerregister som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Om du vill ladda upp Dockerfile och skapa den använder du följande kommando. Ersätt `<registry_name>` med namnet på det containerregister som du vill lagra avbildningen i:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > I det här exemplet tillämpas `:v1` taggen för på avbildningen. Om ingen tagg anges tillämpas `:latest` taggen .

    Under byggprocessen strömmas information tillbaka till kommandoraden. Om bygget lyckas får du ett meddelande som liknar följande text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Mer information om hur du skapar avbildningar med en Azure Container Registry finns [i Skapa och köra en containeravbildning med hjälp av Azure Container Registry-uppgifter](../container-registry/container-registry-quickstart-task-cli.md)

Mer information om hur du laddar upp befintliga avbildningar till en Azure Container Registry finns i Push your first image to a private Docker container registry (Skicka din första avbildning till [ett privat Docker-containerregister).](../container-registry/container-registry-get-started-docker-cli.md)

## <a name="use-a-custom-base-image"></a>Använda en anpassad basavbildning

Om du vill använda en anpassad avbildning behöver du följande information:

* __Avbildningens namn.__ Är till exempel `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` sökvägen till en enkel Docker-avbildning som tillhandahålls av Microsoft.

    > [!IMPORTANT]
    > För anpassade avbildningar som du har skapat måste du inkludera alla taggar som användes med avbildningen. Om avbildningen till exempel har skapats med en viss tagg, till exempel `:v1` . Om du inte använder en specifik tagg när du skapade avbildningen tillämpades `:latest` taggen .

* Om avbildningen finns på __en privat__ lagringsplats behöver du följande information:

    * __Registeradressen__. Till exempel `myregistry.azureecr.io`.
    * Ett användarnamn och __lösenord för__ __tjänstens__ huvudnamn som har läsbehörighet till registret.

    Om du inte har den här informationen kan du prata med administratören för Azure Container Registry som innehåller bilden.

### <a name="publicly-available-base-images"></a>Offentligt tillgängliga basavbildningar

Microsoft tillhandahåller flera Docker-avbildningar på en offentligt tillgänglig lagringsplats som kan användas med stegen i det här avsnittet:

| Bild | Beskrivning |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Kärnavbildning för Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Innehåller ONNX-körning för CPU-inferens |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Innehåller ONNX Runtime och CUDA för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Innehåller ONNX Runtime och TensorRT för GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Innehåller ONNX Runtime och OpenVINO för Intel <sup></sup> Vision Accelerator-design som baseras på Movidius<sup>TM</sup> MyriadX-VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Innehåller ONNX Runtime och OpenVINO för Intel <sup></sup> Movidius<sup>TM USB-minnen</sup> |

Mer information om ONNX Runtime-basavbildningarna finns i avsnittet [ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) på GitHub-lagringsplatsen.

> [!TIP]
> Eftersom bilderna är offentligt tillgängliga behöver du inte ange en adress, ett användarnamn eller lösenord när du använder dem.

Mer information finns i [lagringsplatsen Azure Machine Learning containrar](https://github.com/Azure/AzureML-Containers) på GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Använda en avbildning med Azure Machine Learning SDK

Om du vill använda en avbildning **som Azure Container Registry** i arbetsytan , eller ett **containerregister** som är offentligt tillgängligt, anger du följande [miljöattribut:](/python/api/azureml-core/azureml.core.environment.environment)

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

Om du vill använda en __avbildning från__ ett privat containerregister som inte finns på din arbetsyta måste du använda för att ange adressen till lagringsplatsen och ett `docker.base_image_registry` användarnamn och lösenord:

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

Du måste lägga till azureml-defaults med version >= 1.0.45 som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webbtjänst. Du måste också ange inferencing_stack_version i miljön till "latest" (senaste). Då installeras specifika APT-paket som krävs av webbtjänsten. 

När du har definierat miljön använder du den med [ett InferenceConfig-objekt](/python/api/azureml-core/azureml.core.model.inferenceconfig) för att definiera den härledningsmiljö som modellen och webbtjänsten ska köras i.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Nu kan du fortsätta med distributionen. Följande kodfragment skulle till exempel distribuera en webbtjänst lokalt med hjälp av inferenskonfigurationen och den anpassade avbildningen:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om distribution finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Mer information om hur du anpassar Python-miljön finns i [Skapa och hantera miljöer för träning och distribution.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Använda en avbildning med Machine Learning CLI

> [!IMPORTANT]
> För närvarande Machine Learning CLI använda avbildningar från Azure Container Registry för din arbetsyta eller offentligt tillgängliga lagringsplatsen. Den kan inte använda avbildningar från fristående privata register.

Innan du distribuerar en modell med Machine Learning CLI skapar du en [miljö som](/python/api/azureml-core/azureml.core.environment.environment) använder den anpassade avbildningen. Skapa sedan en inferenskonfigurationsfil som refererar till miljön. Du kan också definiera miljön direkt i inferenskonfigurationsfilen. Följande JSON-dokument visar hur du refererar till en avbildning i ett offentligt containerregister. I det här exemplet definieras miljön infogade:

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

Den här filen används med `az ml model deploy` kommandot . Parametern `--ic` används för att ange inferenskonfigurationsfilen.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Mer information om hur du distribuerar en modell med HJÄLP av ML CLI finns i avsnittet "modellregistrering, profilering och distribution" i [artikeln CLI-tillägg för Azure Machine Learning.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Var du distribuerar och hur](how-to-deploy-and-where.md).
* Lär dig hur du [tränar och distribuerar maskininlärningsmodeller med hjälp av Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning).