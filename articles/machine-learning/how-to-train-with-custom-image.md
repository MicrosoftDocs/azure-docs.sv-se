---
title: Träna en modell med hjälp av en anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder dina egna Docker-avbildningar, eller de som har curerats från Microsoft, för att träna modeller i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817079"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Träna en modell med hjälp av en anpassad Docker-avbildning

I den här artikeln får du lära dig hur du använder en anpassad Docker-avbildning när du tränar modeller med Azure Machine Learning. Du använder exempelskripten i den här artikeln för att klassificera bilder på husdjur genom att skapa ett neuralt faltningsnätverk. 

Azure Machine Learning tillhandahåller en Docker-standardbasavbildning. Du kan också använda Azure Machine Learning för att ange en annan basavbildning, till exempel en av de Azure Machine Learning basavbildningar eller din egen [anpassade avbildning.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) [](https://github.com/Azure/AzureML-Containers) Med anpassade basavbildningar kan du noggrant hantera dina beroenden och behålla bättre kontroll över komponentversioner när du kör träningsjobb.

## <a name="prerequisites"></a>Förutsättningar

Kör koden i någon av dessa miljöer:

* Azure Machine Learning beräkningsinstans (ingen nedladdning eller installation krävs):
  * Slutför [självstudien Konfigurera miljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad notebook-server som förinstallerats med SDK och exempeldatabasen.
  * I lagringsplatsen Azure Machine Learning [exempel](https://github.com/Azure/azureml-examples)hittar du en slutförd notebook-fil genom att gå till katalogen   >  **notebooks fastai**  >  **train-pets-resnet34.ipynb.** 
* Din egen Jupyter Notebook server:
  * Skapa en [konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
  * Installera [Azure Machine Learning SDK.](/python/api/overview/azure/ml/install) 
  * Skapa ett [Azure-containerregister](../container-registry/index.yml) eller ett annat Docker-register som är tillgängligt på Internet.

## <a name="set-up-a-training-experiment"></a>Konfigurera ett träningsexperiment

I det här avsnittet konfigurerar du träningsexperimentet genom att initiera en arbetsyta, definiera din miljö och konfigurera ett beräkningsmål.

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

Arbetsytan [Azure Machine Learning](concept-workspace.md) är den översta resursen för tjänsten. Det ger dig en central plats där du kan arbeta med alla artefakter som du skapar. I Python SDK kan du komma åt arbetsytans artefakter genom att skapa ett [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) -objekt.

Skapa ett `Workspace` -objekt från config.jspå filen som du skapade som en [förutsättning](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definiera din miljö

Skapa ett `Environment` -objekt och aktivera Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Den angivna basavbildningen i följande kod stöder fast.ai, vilket möjliggör distribuerade djupinlärningsfunktioner. Mer information finns i fast.ai Docker Hub [lagringsplats.](https://hub.docker.com/u/fastdotai) 

När du använder din anpassade Docker-avbildning kanske du redan har python-miljön korrekt konfigurerad. I så fall anger du `user_managed_dependencies` flaggan till för att använda den anpassade `True` avbildningens inbyggda Python-miljö. Som standard skapar Azure Machine Learning Conda-miljö med beroenden som du har angett. Tjänsten kör skriptet i den miljön i stället för att använda python-bibliotek som du har installerat på basavbildningen.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Använda ett privat containerregister (valfritt)

Om du vill använda en avbildning från ett privat containerregister som inte finns på arbetsytan använder du för att ange lagringsplatsens adress och `docker.base_image_registry` ett användarnamn och lösenord:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Använda en anpassad Dockerfile (valfritt)

Du kan också använda en anpassad Dockerfile. Använd den här metoden om du behöver installera icke-Python-paket som beroenden. Kom ihåg att ange basavbildningen till `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning stöder endast Docker-avbildningar som tillhandahåller följande programvara:
> * Ubuntu 16.04 eller högre.
> * Conda 4.5.# eller högre.
> * Python 3.6+.

Mer information om hur du skapar och Azure Machine Learning miljöer finns i [Skapa och använda programvarumiljöer.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Skapa eller koppla ett beräkningsmål

Du måste skapa ett [beräkningsmål för](concept-azure-machine-learning-architecture.md#compute-targets) träning av din modell. I den här självstudien skapar `AmlCompute` du som beräkningsresurs för träning.

Det tar `AmlCompute` några minuter att skapa. Om resursen `AmlCompute` redan finns på arbetsytan hoppar den här koden över skapandeprocessen.

Precis som med andra Azure-tjänster finns det begränsningar för vissa resurser (till exempel `AmlCompute` ) som är associerade med Azure Machine Learning Service. Mer information finns i [Standardgränser och hur du begär en högre kvot.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Konfigurera träningsjobbet

I den här självstudien använder du *träningsskriptet train.py* på [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). I praktiken kan du använda ett anpassat träningsskript och köra det med hjälp av Azure Machine Learning.

Skapa en `ScriptRunConfig` resurs för att konfigurera jobbet för körning på önskat [beräkningsmål.](how-to-set-up-training-targets.md)

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Skicka in ditt träningsjobb

När du skickar en träningskörning med hjälp av `ScriptRunConfig` ett -objekt `submit` returnerar metoden ett objekt av typen `ScriptRun` . Det `ScriptRun` returnerade objektet ger dig programmatisk åtkomst till information om träningskörningen. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning kör träningsskript genom att kopiera hela källkatalogen. Om du har känsliga data som du inte vill ladda upp använder du en [.ignore-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i källkatalogen. I stället kan du komma åt dina data med hjälp [av ett datalager](/python/api/azureml-core/azureml.data).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du tränat en modell med hjälp av en anpassad Docker-avbildning. I de här andra artiklarna kan du läsa mer om Azure Machine Learning:
* [Spåra körningsmått](how-to-log-view-metrics.md) under träning.
* [Distribuera en modell med](how-to-deploy-custom-docker-image.md) hjälp av en anpassad Docker-avbildning.
