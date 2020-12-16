---
title: Träna djup inlärning PyTorch-modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-utbildnings skript i företags skala med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 12/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ed368615395614bc0d3e9a6f06727da8c64d8486
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559649"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Träna PyTorch-modeller i skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina [PyTorch](https://pytorch.org/) -utbildnings skript i företags skala med hjälp av Azure Machine Learning.

Exempel skripten i den här artikeln används för att klassificera kyckling-och kalkon bilder för att bygga en DNN (djup Learning neurala Network) som baseras på PyTorch i [guiden](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)för överförings inlärning. Överförings inlärning är en teknik som används för att lösa ett problem med ett annat men relaterat problem. Den här genvägen är en utbildnings process genom att kräva mindre data, tid och beräknings resurser än utbildning från grunden.

Oavsett om du tränar en djup inlärnings PyTorch-modell från grunden eller om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildnings jobb för öppen källkod med elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning. 

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

- Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

    - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel djup inlärning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > pytorch > träna--parameter-Tune-Deploy-with-pytorch** -mappen. 
 
 - Din egen Jupyter Notebook Server
    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa beräknings målet och definiera inlärnings miljön.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Hämta data

Data uppsättningen består av ungefär 120 utbildnings avbildningar för kalkoner och kycklingar, med 100 verifierings avbildningar för varje klass. Vi kommer att ladda ned och extrahera data uppsättningen som en del av vårt övnings skript `pytorch_train.py` . Avbildningarna är en delmängd av de [Öppna avbildningarna i V5-uppsättningen](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Förbered utbildnings skript

I den här självstudien har övnings skriptet `pytorch_train.py` redan angetts. I praktiken kan du ta med ett anpassat utbildnings skript, som det är och köra det med Azure Machine Learning.

Skapa en mapp för dina utbildnings skript (n).

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Skapa ett beräknings mål

Skapa ett beräknings mål för ditt PyTorch-jobb som ska köras. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

### <a name="define-your-environment"></a>Definiera din miljö

Om du vill definiera en Azure ML- [miljö](concept-environments.md) som kapslar in dina utbildnings skripts beroenden kan du antingen definiera en anpassad miljö eller använda en Azure ml-granskad miljö.

#### <a name="use-a-curated-environment"></a>Använda en granskad miljö
Azure ML tillhandahåller färdiga, granskade miljöer om du inte vill definiera en egen miljö. Azure ML har flera processor-och GPU-granskade miljöer för PyTorch som motsvarar olika versioner av PyTorch. Mer information finns [här](resource-curated-environments.md).

Om du vill använda en granskad miljö kan du köra följande kommando i stället:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Om du vill se de paket som ingår i den granskade miljön kan du skriva ut Conda-beroenden till disk:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Se till att den granskade miljön innehåller alla beroenden som krävs av ditt utbildnings skript. Om inte, måste du ändra miljön för att inkludera de beroenden som saknas. Observera att om miljön ändras måste du ge den ett nytt namn eftersom prefixet "AzureML" är reserverat för granskade miljöer. Om du har ändrat Conda-beroende YAML-filen kan du skapa en ny miljö från den med ett nytt namn, t. ex.:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Om du i stället har ändrat det granskade miljö objektet direkt, kan du klona miljön med ett nytt namn:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Skapa en anpassad miljö

Du kan också skapa en egen Azure ML-miljö som kapslar in dina utbildnings skript beroenden.

Definiera först dina Conda-beroenden i en YAML-fil. i det här exemplet heter filen `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Skapa en Azure ML-miljö från den här Conda-miljö specifikationen. Miljön kommer att paketeras i en Docker-behållare vid körning.

Som standard om ingen bas avbildning anges använder Azure ML en processor avbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` som bas avbildningen. Eftersom det här exemplet kör utbildning i ett GPU-kluster måste du ange en GPU-bas avbildning som har nödvändiga GPU-drivrutiner och-beroenden. Azure ML innehåller en uppsättning grundläggande avbildningar som publiceras på Microsoft Container Registry (MCR) som du kan använda, se [Azure/azureml-containers](https://github.com/Azure/AzureML-Containers) GitHub lagrings platsen för mer information.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Om du vill kan du bara samla in alla dina beroenden direkt i en anpassad Docker-avbildning eller Dockerfile och skapa din miljö. Mer information finns i [träna med anpassad bild](how-to-train-with-custom-image.md).

Mer information om hur du skapar och använder miljöer finns [i skapa och använda program miljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din utbildnings körning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig

Skapa ett [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) -objekt om du vill ange konfigurations information för ditt utbildnings jobb, inklusive ditt utbildnings skript, vilken miljö som ska användas och vilket beräknings mål som ska köras. Alla argument till ditt utbildnings skript skickas via kommando raden om de anges i `arguments` parametern. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. Gå i stället till dina data med hjälp av en Azure ML- [datauppsättning](how-to-train-with-datasets.md).

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns i [Konfigurera och skicka utbildnings körningar](how-to-set-up-training-targets.md).

> [!WARNING]
> Om du tidigare använde PyTorch-uppskattningen för att konfigurera dina PyTorch-utbildnings jobb, bör du tänka på att uppskattningar är inaktuella i en framtida version av Azure ML SDK. Med Azure ML SDK >= 1.15.0, är ScriptRunConfig det rekommenderade sättet att konfigurera utbildnings jobb, inklusive de som använder DL-ramverk.

## <a name="submit-your-run"></a>Skicka in din körning

[Kör-objektet](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Vad händer under körningen
När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt den miljö som definierats. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet. Om en granskad miljö anges i stället används den cachelagrade avbildningen som ska användas för att återställa den hanterade miljön.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och `script` utförs. Utdata från STDOUT och **./logs** -mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen **./outputs** i körningen kopieras till körnings historiken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också hämta en lokal kopia av modellen med hjälp av objektet kör. I övnings skriptet bevarar `pytorch_train.py` ett PyTorch Save-objekt modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Distribuerad träning

Azure Machine Learning stöder även distribuerade PyTorch-jobb med flera noder så att du kan skala dina utbildnings arbets belastningar. Du kan enkelt köra distribuerade PyTorch-jobb och Azure ML för att hantera dirigeringen åt dig.

Azure ML stöder körning av distribuerade PyTorch-jobb med både Horovod och PyTorch inbyggda DistributedDataParallel-modul.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är en öppen källkod som alla reducerar ramverk för distribuerad utbildning som utvecklas av Uber. Den erbjuder en enkel väg till att skriva distribuerad PyTorch-kod för utbildning.

Din utbildnings kod måste instrumenteras med Horovod för distribuerad utbildning. Mer information om hur du använder Horovod med PyTorch finns i [Horovod-dokumentationen](https://horovod.readthedocs.io/en/stable/pytorch.html).

Se dessutom till att din utbildnings miljö innehåller **horovod** -paketet. Om du använder en PyTorch-granskad miljö ingår horovod redan som en av beroendena. Om du använder en egen miljö kontrollerar du att horovod-beroendet ingår, till exempel:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

För att kunna köra ett distribuerat jobb med MPI/Horovod på Azure ML måste du ange en [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) till- `distributed_job_config` parametern för ScriptRunConfig-konstruktorn. I koden nedan kan du konfigurera ett distribuerat jobb med två noder som kör en process per nod. Om du också vill köra flera processer per nod (dvs. om klustrets SKU har flera GPU: er) anger du även `process_count_per_node` parametern i MpiConfiguration (Standardvärdet är `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

En fullständig själv studie kurs om att köra distribuerade PyTorch med Horovod på Azure ML finns i [distribuerade PyTorch med Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Om du använder PyTorch inbyggda [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) -modulen som har skapats med hjälp av paketet **Torch. Distributed** i din utbildnings kod kan du också starta det distribuerade jobbet via Azure ml.

För att kunna köra ett distribuerat PyTorch-jobb med DistributedDataParallel, anger du en [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) till- `distributed_job_config` parametern för ScriptRunConfig-konstruktorn. Om du vill använda NCCL-Server delen för Torch. Distributed anger `communication_backend='Nccl'` du i PyTorchConfiguration. I koden nedan kan du konfigurera ett distribuerat jobb med två noder. NCCL-Server delen är den rekommenderade Server delen för distribuerad GPU-utbildning i PyTorch.

För distribuerade PyTorch-jobb som kon figurer ATS via PyTorchConfiguration kommer Azure ML att ange följande miljövariabler för nodernas beräknings mål:

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: URL för den delade fil system initieringen av process gruppen
* `AZ_BATCHAI_TASK_INDEX`: arbets processens globala rangordning

Du kan ange de här miljövariablerna till motsvarande argument i övnings skriptet via `arguments` parametern ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

Om du i stället vill använda gloo-Server delen för distribuerad utbildning anger du `communication_backend='Gloo'` i stället. Gloo-Server delen rekommenderas för distribuerad CPU-utbildning.

En fullständig själv studie kurs om att köra distribuerade PyTorch i Azure ML finns i [distribuerade PyTorch med DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo).

## <a name="export-to-onnx"></a>Exportera till ONNX

Du kan optimera härledningen med [ONNX-körningen](concept-onnx.md)genom att konvertera din tränade PyTorch-modell till ONNX-formatet. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data. Se [självstudien](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) för ett exempel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en djup inlärning, neurala Network med PyTorch på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår modell distributions artikel.

* [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
