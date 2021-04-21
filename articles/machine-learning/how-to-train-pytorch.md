---
title: Träna PyTorch-modeller för djupinlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-träningsskript i företagsskala med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5a955424f3fb91a38e9061966ed742922913f1c4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819168"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Träna PyTorch-modeller i stor skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör [dina PyTorch-träningsskript](https://pytorch.org/) i företagsskala med hjälp av Azure Machine Learning.

Exempelskripten i den här artikeln används för att klassificera bilder för att skapa ett neuralt nätverk för djupinlärning (DNN) baserat på PyTorchs självstudie om [överförd inlärning.](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) Överföringsinlärning är en teknik som tillämpar kunskap från att lösa ett problem till ett annat men relaterat problem. Detta genvägar träningsprocessen genom att kräva mindre data, tid och beräkningsresurser än träning från grunden. Mer information [om överföringsinlärning finns](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) i artikeln om djupinlärning kontra maskininlärning.

Oavsett om du tränar en PyTorch-modell för djupinlärning från grunden eller om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut träningsjobb med öppen källkod med hjälp av elastiska beräkningsresurser i molnet. Du kan skapa, distribuera, versionsövervaka och övervaka modeller i produktionsklass med Azure Machine Learning. 

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av dessa miljöer:

- Azure Machine Learning beräkningsinstans – ingen nedladdning eller installation krävs

    - Slutför [självstudien: Konfigurera miljö och arbetsyta för](tutorial-1st-experiment-sdk-setup.md) att skapa en dedikerad notebook-server som är förinstallerad med SDK och exempeldatabasen.
    - I exempelmappen för djupinlärning på notebook-servern hittar du en slutförd och expanderad notebook-fil genom att gå till den här katalogen: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch.** 
 
 - Din egen Jupyter Notebook server
    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Skapa en konfigurationsfil för arbetsytan.](how-to-configure-environment.md#workspace)
    - [Ladda ned exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Du hittar också en slutförd version [Jupyter Notebook av](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) den här guiden på sidan med GitHub-exempel. Anteckningsboken innehåller utökade avsnitt som omfattar intelligent justering av hyperparametrar, modelldistribution och notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet konfigureras träningsexperimentet genom att nödvändiga Python-paket läses in, en arbetsyta initieras, beräkningsmålet skapas och träningsmiljön definieras.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

Arbetsytan [Azure Machine Learning](concept-workspace.md) är den översta resursen för tjänsten. Det ger dig en central plats där du kan arbeta med alla artefakter som du skapar. I Python SDK kan du komma åt arbetsytans artefakter genom att skapa ett [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) -objekt.

Skapa ett arbetsyteobjekt `config.json` från filen som skapades i [kravavsnittet](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Hämta data

Datamängden består av cirka 120 träningsbilder vardera för 100 valideringsbilder för varje klass. Vi laddar ned och extraherar datauppsättningen som en del av träningsskriptet `pytorch_train.py` . Bilderna är en delmängd av [Open Images v5-datauppsättningen](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Förbereda träningsskript

I den här självstudien har `pytorch_train.py` träningsskriptet, , redan angetts. I praktiken kan du använda valfria anpassade träningsskript och köra det med Azure Machine Learning.

Skapa en mapp för dina träningsskript.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål som PyTorch-jobbet ska köras på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräkningskluster.

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

Mer information om beräkningsmål finns i artikeln [vad är ett beräkningsmål.](concept-compute-target.md)

### <a name="define-your-environment"></a>Definiera din miljö

Om du vill definiera Azure [ML-miljön](concept-environments.md) som kapslar in beroenden för träningsskriptet kan du antingen definiera en anpassad miljö eller använda en Azure ML-hanterad miljö.

#### <a name="use-a-curated-environment"></a>Använda en curated-miljö
Azure ML tillhandahåller fördefinierade, hanterade miljöer om du inte vill definiera din egen miljö. Azure ML har flera cpu- och GPU-hanterade miljöer för PyTorch som motsvarar olika versioner av PyTorch. Mer information finns [här.](resource-curated-environments.md)

Om du vill använda en curated-miljö kan du köra följande kommando i stället:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Om du vill se paketen som ingår i den här miljön kan du skriva ut conda-beroenden till disken:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Kontrollera att den curated -miljön innehåller alla beroenden som krävs av ditt träningsskript. Annars måste du ändra miljön så att den inkluderar de saknade beroendena. Observera att om miljön ändras måste du ge den ett nytt namn eftersom prefixet "AzureML" är reserverat för hanterade miljöer. Om du har ändrat YAML-filen conda-beroenden kan du skapa en ny miljö från den med ett nytt namn, t.ex.:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Om du i stället hade ändrat det hanterade miljöobjektet direkt kan du klona miljön med ett nytt namn:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Skapa en anpassad miljö

Du kan också skapa en egen Azure ML-miljö som kapslar in träningsskriptens beroenden.

Definiera först dina conda-beroenden i en YAML-fil. I det här exemplet heter filen `conda_dependencies.yml` .

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

Skapa en Azure ML-miljö från den här conda-miljöspecifikationen. Miljön kommer att paketeras i en Docker-container vid körning.

Som standard använder Azure ML en CPU-avbildning som basavbildning om ingen basavbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` anges. Eftersom det här exemplet kör träning på ett GPU-kluster måste du ange en GPU-basavbildning som har nödvändiga GPU-drivrutiner och beroenden. Azure ML har en uppsättning basavbildningar publicerade på Microsoft Container Registry (MCR) som du kan använda. Mer information finns i [GitHub-lagringsplatsen för Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Alternativt kan du bara samla in alla dina beroenden direkt i en anpassad Docker-avbildning eller Dockerfile och skapa din miljö från den. Mer information finns i [Träna med anpassad avbildning.](how-to-train-with-custom-image.md)

Mer information om hur du skapar och använder miljöer finns [i Skapa och använda programvarumiljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din träningskörning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig

Skapa ett [ScriptRunConfig-objekt](/python/api/azureml-core/azureml.core.scriptrunconfig) för att ange konfigurationsinformationen för träningsjobbet, inklusive träningsskriptet, miljön som ska användas och beräkningsmålet som ska köras. Argument till träningsskriptet skickas via kommandoraden om det anges i `arguments` parametern . 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning kör träningsskript genom att kopiera hela källkatalogen. Om du har känsliga data som du inte vill ladda upp använder du en [.ignore-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i källkatalogen . I stället får du åtkomst till dina data med hjälp av en Azure [ML-datauppsättning](how-to-train-with-datasets.md).

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns [i Konfigurera och skicka träningskörningar.](how-to-set-up-training-targets.md)

> [!WARNING]
> Om du tidigare använde PyTorch-beräknaren för att konfigurera dina PyTorch-träningsjobb bör du observera att beräknare har gjorts inaktuella från och med SDK-versionen 1.19.0. Med Azure ML SDK >= 1.15.0 är ScriptRunConfig det rekommenderade sättet att konfigurera träningsjobb, inklusive de som använder djupinlärningsramverk. Vanliga migreringsfrågor finns i [migreringsguiden Estimator to ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Skicka körningen

[Körningsobjektet](/python/api/azureml-core/azureml.core.run%28class%29) tillhandahåller gränssnittet för körningshistoriken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Vad händer vid körning
När körningen körs går den igenom följande steg:

- **Förbereda:** En Docker-avbildning skapas enligt den definierade miljön. Avbildningen laddas upp till arbetsytans containerregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet. Om en rensad miljö anges i stället används den cachelagrade avbildningen som backar upp den här curated-miljön.

- **Skalning:** Klustret försöker skala upp om Batch AI kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Körs:** Alla skript i skriptmappen laddas upp till beräkningsmålet, datalager monteras eller kopieras `script` och körs. Utdata från stdout och **./logs-mappen** strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning:** **Mappen ./outputs** för körningen kopieras till körningshistoriken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och versions versionera dina modeller på din arbetsyta för att [förenkla modellhanteringen och distributionen.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Distributionen innehåller ett avsnitt om att registrera modeller, men [](how-to-deploy-and-where.md#choose-a-compute-target) du kan gå direkt till att skapa ett beräkningsmål för distribution eftersom du redan har en registrerad modell.

Du kan också ladda ned en lokal kopia av modellen med hjälp av Objektet Kör. I träningsskriptet `pytorch_train.py` sparar ett PyTorch-spara-objekt modellen i en lokal mapp (lokalt till beräkningsmålet). Du kan använda Run-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Distribuerad träning

Azure Machine Learning också stöd för distribuerade PyTorch-jobb med flera noder så att du kan skala dina träningsarbetsbelastningar. Du kan enkelt köra distribuerade PyTorch-jobb så hanterar Azure ML orkestrering åt dig.

Azure ML stöder körning av distribuerade PyTorch-jobb med både Horovod och PyTorchs inbyggda DistributedDataParallel-modul.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är ett ramverk för minskning av distribuerad träning med öppen källkod som utvecklats av Uber. Den erbjuder en enkel väg till att skriva distribuerad PyTorch-kod för träning.

Din träningskod måste instrumenteras med Horovod för distribuerad träning. Mer information om hur du använder Horovod med PyTorch finns i [Horovod-dokumentationen.](https://horovod.readthedocs.io/en/stable/pytorch.html)

Kontrollera också att din träningsmiljö innehåller **horovod-paketet.** Om du använder en PyTorch-miljö ingår horovod redan som ett av beroendena. Om du använder en egen miljö kontrollerar du att horovod-beroendet ingår, till exempel:

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

För att kunna köra ett distribuerat jobb med hjälp av MPI/Horovod på Azure ML måste du ange en [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) till parametern för `distributed_job_config` ScriptRunConfig-konstruktorn. Koden nedan konfigurerar ett distribuerat jobb med två noder som kör en process per nod. Om du även vill köra flera processer per nod (d.v.s. om kluster-SKU:n har flera GPU:er) anger du dessutom parametern i `process_count_per_node` MpiConfiguration (standardvärdet är `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

En fullständig självstudie om hur du kör distribuerade PyTorch med Horovod på Azure ML finns i [Distribuerad PyTorch med Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Om du använder PyTorchs inbyggda [DistributedDataParallel-modul](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) som har skapats med hjälp av paketet **"jaga.distribuerad"** i din träningskod kan du även starta det distribuerade jobbet via Azure ML.

Om du vill starta ett distribuerat PyTorch-jobb på Azure ML har du två alternativ:
1. Start per process: Ange det totala antalet arbetsprocesser som du vill köra så hanterar Azure ML varje process.
2. Start per nod `torch.distributed.launch` med : ange det kommando som du vill köra på varje `torch.distributed.launch` nod. Launch-verktyget hanterar start av arbetsprocesserna på varje nod.

Det finns inga grundläggande skillnader mellan de här startalternativen. Det är i stort sett upp till användarens önskemål eller konventionerna för ramverk/bibliotek som bygger på vanilla PyTorch (till exempel Blixt eller Tavlaing Face).

#### <a name="per-process-launch"></a>Start per process
Om du vill använda det här alternativet för att köra ett distribuerat PyTorch-jobb gör du följande:
1. Ange träningsskript och argument
2. Skapa en [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) och `process_count` ange samt `node_count` . `process_count`motsvarar det totala antalet processer som du vill köra för jobbet. Detta bör vanligtvis vara lika med antalet GPU:er per nod multiplicerat med antalet noder. Om `process_count` inte anges startar Azure ML som standard en process per nod.

Azure ML anger följande miljövariabler:
* `MASTER_ADDR` – IP-adressen för den dator som ska vara värd för processen med plats 0.
* `MASTER_PORT` – En kostnadsfri port på datorn som ska vara värd för processen med plats 0.
* `NODE_RANK` – Rangordningen för noden för träning med flera noder. Möjliga värden är 0 till (totalt antal noder – 1).
* `WORLD_SIZE` – Det totala antalet processer. Detta ska vara lika med det totala antalet enheter (GPU) som används för distribuerad träning.
* `RANK` – Den (globala) rangordningen för den aktuella processen. Möjliga värden är 0 till (världsstorlek – 1).
* `LOCAL_RANK` – Den lokala (relativa) rangordningen för processen i noden. Möjliga värden är 0 till (antal processer på noden – 1).

Eftersom de nödvändiga miljövariablerna anges åt dig av [](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) Azure ML kan du använda standardmetoden för initiering av miljövariabler för att initiera processgruppen i din träningskod.

Följande kodfragment konfigurerar ett PyTorch-jobb med 2 noder, 2 processer per nod:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> För att kunna använda det här alternativet för träning med flera processer per nod måste du använda Azure ML Python SDK >= 1.22.0, som introducerades `process_count` i 1.22.0.

> [!TIP]
> Om träningsskriptet skickar information som lokal rangordning eller rangordning som skriptargument kan du referera till miljövariabler i argumenten: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Start per nod med `torch.distributed.launch`
PyTorch tillhandahåller ett startverktyg i [node.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) som användarna kan använda för att starta flera processer per nod. Modulen `torch.distributed.launch` skapar flera träningsprocesser på var och en av noderna.

Följande steg visar hur du konfigurerar ett PyTorch-jobb med en start per nod i Azure ML som uppnår motsvarigheten till att köra följande kommando:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Ange kommandot `torch.distributed.launch` till `command` -parametern för `ScriptRunConfig` konstruktorn. Azure ML kör det här kommandot på varje nod i träningsklustret. `--nproc_per_node` ska vara mindre än eller lika med antalet GPU:er som är tillgängliga på varje nod. `MASTER_ADDR`, `MASTER_PORT` och anges av Azure `NODE_RANK` ML, så du kan bara referera till miljövariablerna i kommandot . Azure ML ställer `MASTER_PORT` in på 6105, men du kan skicka ett annat `--master_port` värde till kommandots argument om du `torch.distributed.launch` vill. (Startverktyget återställer miljövariablerna.)
2. Skapa en `PyTorchConfiguration` och ange `node_count` . Du behöver inte ange som standard att Azure ML startar en `process_count` process per nod, vilket kör startkommandot som du har angett.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

En fullständig självstudie om hur du kör distribuerad PyTorch på Azure ML finns i [Distribuerad PyTorch med DistributedDataParallel.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)

### <a name="troubleshooting"></a>Felsökning

* **Horovod** har stängts av: I de flesta fall, om du stöter på "AbortedError: Horovod har stängts av", fanns det ett underliggande undantag i någon av processerna som gjorde att Horovod stängdes av. Varje rang i MPI-jobbet hämtar en egen dedikerad loggfilen i Azure ML. De här loggarna heter `70_driver_logs`. Vid distribuerad träning får loggnamnen suffixet `_rank` så att du enklare kan se skillnad på loggarna. Om du vill hitta det exakta fel som gjorde att Horovod stängdes går du igenom alla loggfiler och letar efter `Traceback` i slutet av driver_log filerna. En av dessa filer ger dig det faktiska underliggande undantaget. 

## <a name="export-to-onnx"></a>Exportera till ONNX

Om du vill optimera inferensen [med ONNX Runtime](concept-onnx.md)konverterar du din tränade PyTorch-modell till ONNX-formatet. Slutsatsledning, eller modellbedömning, är den fas där den distribuerade modellen används för förutsägelse, oftast på produktionsdata. Ett exempel [finns](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) i självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat ett neuralt nätverk för djupinlärning med Hjälp av PyTorch Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår artikel om modelldistribution.

* [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körningsmått under träning](how-to-log-view-metrics.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referensarkitektur för distribuerad djupinlärningsträning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
