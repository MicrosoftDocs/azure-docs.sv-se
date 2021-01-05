---
title: Träna en modell med hjälp av en anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder dina egna Docker-avbildningar eller granskade från Microsoft för att träna modeller i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: cb8e71d9f94441f79dd7ce2fd5ee6458987563c3
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795928"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Träna en modell med hjälp av en anpassad Docker-avbildning

I den här artikeln får du lära dig hur du använder en anpassad Docker-avbildning när du tränar modeller med Azure Machine Learning. Du använder exempel skripten i den här artikeln för att klassificera PET-bilder genom att skapa ett (convolutional neurala-nätverk. 

Azure Machine Learning tillhandahåller en standard Docker-bas avbildning. Du kan också använda Azure Machine Learning miljöer för att ange en annan bas avbildning, till exempel en av de underhållna [Azure Machine Learning bas avbildningarna](https://github.com/Azure/AzureML-Containers) eller din egen [anpassade avbildning](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Med anpassade bas avbildningar kan du noggrant hantera dina beroenden och upprätthålla bättre kontroll över komponent versioner när du kör utbildnings jobb.

## <a name="prerequisites"></a>Förutsättningar

Kör koden i någon av följande miljöer:

* Azure Machine Learning beräknings instans (inga hämtningar eller installationer behövs):
  * Slutför själv studie kursen [Konfigurera miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
  * Leta upp en slutförd antecknings bok i Azure Machine Learning [exempel lagrings plats](https://github.com/Azure/azureml-examples)genom att gå till **antecknings böckerna**  >  **fastai**  >  **Train-sällskaps-resnet34. ipynb** Directory. 
* Din egen Jupyter Notebook Server:
  * Skapa en [konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
  * Installera [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
  * Skapa ett [Azure Container Registry](../container-registry/index.yml) eller andra Docker-register som är tillgängligt på Internet.

## <a name="set-up-a-training-experiment"></a>Konfigurera ett utbildnings experiment

I det här avsnittet ställer du in ditt utbildnings experiment genom att initiera en arbets yta, definiera din miljö och konfigurera ett beräknings mål.

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objekt.

Skapa ett `Workspace` objekt från config.jspå filen som du skapade som en [förutsättning](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definiera din miljö

Skapa ett `Environment` objekt och aktivera Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Den angivna bas avbildningen i följande kod stöder fast.ai-biblioteket, som möjliggör distribuerade djup inlärnings funktioner. Mer information finns i [fast.AI Docker Hub-lagringsplatsen](https://hub.docker.com/u/fastdotai). 

När du använder din anpassade Docker-avbildning kanske du redan har konfigurerat python-miljön. I så fall ställer du in `user_managed_dependencies` flaggan på `True` att använda den anpassade avbildningens inbyggda python-miljö. Azure Machine Learning skapar som standard en Conda-miljö med beroenden som du har angett. Tjänsten kör skriptet i den miljön i stället för att använda python-bibliotek som du har installerat på bas avbildningen.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Använd ett privat behållar register (valfritt)

Använd `docker.base_image_registry` för att ange adressen till lagrings platsen och ett användar namn och lösen ord för att använda en avbildning från ett privat behållar register som inte finns på din arbets yta:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Använd en anpassad Dockerfile (valfritt)

Det är också möjligt att använda en anpassad Dockerfile. Använd den här metoden om du behöver installera icke-python-paket som beroenden. Kom ihåg att ställa in bas avbildningen på `None` .

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

Mer information om hur du skapar och hanterar Azure Machine Learning miljöer finns i [skapa och använda program miljöer](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Skapa eller koppla ett beräknings mål

Du måste skapa ett [beräknings mål](concept-azure-machine-learning-architecture.md#compute-targets) för att träna din modell. I den här självstudien skapar du `AmlCompute` som din beräknings resurs för utbildning.

`AmlCompute`Det tar några minuter att skapa ett par minuter. Om `AmlCompute` resursen redan finns på arbets ytan hoppar den här koden över skapande processen.

Precis som med andra Azure-tjänster finns det begränsningar för vissa resurser (till exempel `AmlCompute` ) som är associerade med Azure Machine Learning tjänsten. Mer information finns i [standard gränser och hur du begär en högre kvot](how-to-manage-quotas.md).

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

## <a name="configure-your-training-job"></a>Konfigurera ditt utbildnings jobb

I den här självstudien använder du övnings skriptet *Train.py* på [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). I praktiken kan du göra ett anpassat utbildnings skript och köra det, precis som med Azure Machine Learning.

Skapa en `ScriptRunConfig` resurs för att konfigurera jobbet för att köras på önskat [beräknings mål](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Skicka in ditt utbildnings jobb

När du skickar en övnings körning med hjälp av ett `ScriptRunConfig` objekt `submit` returnerar metoden ett objekt av typen `ScriptRun` . Det returnerade `ScriptRun` objektet ger dig programmerings mässig åtkomst till information om tränings körningen. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. I stället kan du komma åt dina data med hjälp av ett data [lager](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du tränat en modell med hjälp av en anpassad Docker-avbildning. Mer information om Azure Machine Learning finns i dessa artiklar:
* [Spåra körnings mått](how-to-track-experiments.md) under träning.
* [Distribuera en modell](how-to-deploy-custom-docker-image.md) med hjälp av en anpassad Docker-avbildning.
