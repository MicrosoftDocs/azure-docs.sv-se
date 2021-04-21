---
title: Träna och distribuera en TensorFlow-modell
titleSuffix: Azure Machine Learning
description: Lär dig Azure Machine Learning du kan skala ut ett TensorFlow-träningsjobb med hjälp av elastiska molnresurser för beräkning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0eac999fa53679ef67c2a322bd8cc7841197d493
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819150"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Träna TensorFlow-modeller i stor skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör [TensorFlow-träningsskript](https://www.tensorflow.org/overview) i stor skala med Azure Machine Learning.

Det här exemplet tränar och registrerar en TensorFlow-modell för att klassificera handskrivna siffror med hjälp av ett djupt neuralt nätverk (DNN).

Oavsett om du utvecklar en TensorFlow-modell från grunden eller [](how-to-deploy-existing-model.md) om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut träningsjobb med öppen källkod för att skapa, distribuera, versiona och övervaka modeller i produktionsklass.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av dessa miljöer:

 - Azure Machine Learning beräkningsinstans – ingen nedladdning eller installation krävs

     - Slutför [självstudien: Konfigurera miljö och arbetsyta för](tutorial-1st-experiment-sdk-setup.md) att skapa en dedikerad notebook-server som är förinstallerad med SDK och exempeldatabasen.
    - I exempelmappen för djupinlärning på notebook-servern hittar du en slutförd och expanderad notebook-fil genom att gå till den här katalogen: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow.** 
 
 - Din egen Jupyter Notebook server

    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Skapa en konfigurationsfil för arbetsytan.](how-to-configure-environment.md#workspace)
    - [Ladda ned exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` Och `utils.py`
     
    Du hittar också en slutförd version [Jupyter Notebook av](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) den här guiden på sidan med GitHub-exempel. Anteckningsboken innehåller expanderade avsnitt som täcker intelligent justering av hyperparametrar, modelldistribution och notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet konfigureras träningsexperimentet genom att de nödvändiga Python-paketen läses in, en arbetsyta initieras, beräkningsmålet skapas och träningsmiljön definieras.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
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

### <a name="create-a-file-dataset"></a>Skapa en fildatauppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i arbetsytans datalager eller offentliga URL:er. Filerna kan ha vilket format som helst och klassen ger dig möjlighet att ladda ned eller montera filerna till din beräkning. Genom att skapa `FileDataset` en skapar du en referens till datakällans plats. Om du har tillämpat några transformningar på datauppsättningen lagras de också i datauppsättningen. Data finns kvar på den befintliga platsen, så det tillkommer ingen extra lagringskostnad. Mer information [finns i](./how-to-create-register-datasets.md) i guiden `Dataset` om paketet.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Använd metoden för att registrera datauppsättningen till din arbetsyta så att de kan delas med andra, återanvändas i olika experiment och refereras till med namnet i `register()` träningsskriptet.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål som TensorFlow-jobbet ska köras på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräkningskluster.

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
Azure ML tillhandahåller fördefinierade, hanterade miljöer om du inte vill definiera din egen miljö. Azure ML har flera cpu- och GPU-hanterade miljöer för TensorFlow som motsvarar olika versioner av TensorFlow. Mer information finns [här.](resource-curated-environments.md)

Om du vill använda en curated-miljö kan du köra följande kommando i stället:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Om du vill se paketen som ingår i den här miljön kan du skriva ut conda-beroenden till disken:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Kontrollera att den curated -miljön innehåller alla beroenden som krävs av ditt träningsskript. Annars måste du ändra miljön så att den inkluderar de saknade beroendena. Observera att om miljön ändras måste du ge den ett nytt namn eftersom prefixet "AzureML" är reserverat för hanterade miljöer. Om du har ändrat YAML-filen conda-beroenden kan du skapa en ny miljö från den med ett nytt namn, t.ex.:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Om du i stället hade ändrat det hanterade miljöobjektet direkt kan du klona miljön med ett nytt namn:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
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
  - tensorflow-gpu==2.2.0
```

Skapa en Azure ML-miljö från den här conda-miljöspecifikationen. Miljön kommer att paketeras i en Docker-container vid körning.

Som standard använder Azure ML en CPU-avbildning som basavbildning om ingen basavbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` anges. Eftersom det här exemplet kör träning på ett GPU-kluster måste du ange en GPU-basavbildning som har nödvändiga GPU-drivrutiner och beroenden. Azure ML har en uppsättning basavbildningar publicerade på Microsoft Container Registry (MCR) som du kan använda. Mer information finns på GitHub-lagringsplatsen [för Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Om du vill kan du bara samla in alla dina beroenden direkt i en anpassad Docker-avbildning eller Dockerfile och skapa din miljö utifrån den. Mer information finns i [Träna med anpassad avbildning.](how-to-train-with-custom-image.md)

Mer information om hur du skapar och använder miljöer finns [i Skapa och använda programvarumiljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din träningskörning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig

Skapa ett [ScriptRunConfig-objekt](/python/api/azureml-core/azureml.core.scriptrunconfig) för att ange konfigurationsinformationen för träningsjobbet, inklusive träningsskriptet, miljön som ska användas och beräkningsmålet som ska köras. Argument till träningsskriptet skickas via kommandoraden om det anges i `arguments` parametern .

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning kör träningsskript genom att kopiera hela källkatalogen. Om du har känsliga data som du inte vill ladda upp använder du en [.ignore-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i källkatalogen . I stället får du åtkomst till dina data med hjälp av en Azure [ML-datauppsättning](how-to-train-with-datasets.md).

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns [i Konfigurera och skicka träningskörningar.](how-to-set-up-training-targets.md)

> [!WARNING]
> Om du tidigare använde TensorFlow-beräknaren för att konfigurera Dina TensorFlow-träningsjobb bör du observera att beräknare har gjorts inaktuella från och med 1.19.0 SDK-versionen. Med Azure ML SDK >= 1.15.0 är ScriptRunConfig det rekommenderade sättet att konfigurera träningsjobb, inklusive de som använder djupinlärningsramverk. Vanliga migreringsfrågor finns i [migreringsguiden Estimator to ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Skicka en körning

[Körningsobjektet](/python/api/azureml-core/azureml.core.run%28class%29) tillhandahåller gränssnittet för körningshistoriken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Vad händer vid körning
När körningen körs går den igenom följande steg:

- **Förbereda:** En Docker-avbildning skapas enligt den definierade miljön. Avbildningen laddas upp till arbetsytans containerregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet. Om en rensad miljö anges i stället används den cachelagrade avbildningen som backar upp den curated miljön.

- **Skalning:** Klustret försöker skala upp om Batch AI kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Körs:** Alla skript i skriptmappen laddas upp till beräkningsmålet, datalager monteras eller kopieras `script` och körs. Utdata från stdout och **./logs-mappen** strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning:** **Mappen ./outputs** för körningen kopieras till körningshistoriken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och versions versionera dina modeller på din arbetsyta för att [förenkla modellhanteringen och distributionen.](concept-model-management-and-deployment.md) Valfritt: genom att ange `model_framework` `model_framework_version` parametrarna , `resource_configuration` och blir modelldistribution utan kod tillgänglig. På så sätt kan du direkt distribuera din modell som en webbtjänst från den registrerade modellen, och objektet definierar `ResourceConfiguration` beräkningsresursen för webbtjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Du kan också ladda ned en lokal kopia av modellen med hjälp av Objektet Kör. I träningsskriptet `tf_mnist.py` sparar ett TensorFlow-spararobjekt modellen i en lokal mapp (lokal för beräkningsmålet). Du kan använda Run-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Distribuerad träning

Azure Machine Learning har även stöd för distribuerade TensorFlow-jobb med flera noder så att du kan skala dina träningsarbetsbelastningar. Du kan enkelt köra distribuerade TensorFlow-jobb, så hanterar Azure ML orkestrering åt dig.

Azure ML stöder körning av distribuerade TensorFlow-jobb med både Horovod- och TensorFlows inbyggda API för distribuerad träning.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är ett ramverk för minskning av distribuerad träning med öppen källkod som utvecklats av Uber. Den erbjuder en enkel väg till att skriva distribuerad TensorFlow-kod för träning.

Din träningskod måste instrumenteras med Horovod för distribuerad träning. Mer information om hur du använder Horovod med TensorFlow finns i Horovod-dokumentationen:

Mer information om hur du använder Horovod med TensorFlow finns i Horovod-dokumentationen:

* [Horovod med TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod med TensorFlows Keras-API](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Kontrollera också att din träningsmiljö innehåller **horovod-paketet.** Om du använder en TensorFlow-curerad miljö ingår horovod redan som ett av beroendena. Om du använder en egen miljö kontrollerar du att horovod-beroendet ingår, till exempel:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

För att kunna köra ett distribuerat jobb med hjälp av MPI/Horovod på Azure ML måste du ange en [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) till parametern för `distributed_job_config` ScriptRunConfig-konstruktorn. Koden nedan konfigurerar ett distribuerat jobb med två noder som kör en process per nod. Om du även vill köra flera processer per nod (d.v.s. om kluster-SKU:n har flera GPU:er) anger du dessutom parametern i `process_count_per_node` MpiConfiguration (standardvärdet är `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

En fullständig självstudiekurs om hur du kör distribuerade TensorFlow med Horovod på Azure ML finns i [Distribuerade TensorFlow med Horovod.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)

### <a name="tfdistribute"></a>tf.distribute

Om du använder intern [distribuerad TensorFlow](https://www.tensorflow.org/guide/distributed_training) i din träningskod, t.ex. TensorFlow 2.x:s API, kan du också starta det `tf.distribute.Strategy` distribuerade jobbet via Azure ML. 

Det gör du genom att ange [en TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) till `distributed_job_config` parametern i ScriptRunConfig-konstruktorn. Om du använder `tf.distribute.experimental.MultiWorkerMirroredStrategy` anger du i `worker_count` TensorflowConfiguration som motsvarar antalet noder för träningsjobbet.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

I TensorFlow krävs `TF_CONFIG` miljövariabeln för träning på flera datorer. Azure ML konfigurerar och anger `TF_CONFIG` variabeln på lämpligt sätt för varje arbetsroll innan träningsskriptet körs. Du kan komma `TF_CONFIG` åt från träningsskriptet om du behöver via `os.environ['TF_CONFIG']` .

Exempelstruktur för `TF_CONFIG` uppsättning på en arbetsnod:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Om ditt träningsskript använder parameterserverns strategi för distribuerad träning, d.v.s. för äldre TensorFlow 1.x, måste du också ange antalet parameterservrar som ska användas i jobbet, t.ex. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Distribuera en TensorFlow-modell

Distributionen innehåller ett avsnitt om att registrera modeller, men [](how-to-deploy-and-where.md#choose-a-compute-target) du kan gå direkt till att skapa ett beräkningsmål för distribution eftersom du redan har en registrerad modell.

### <a name="preview-no-code-model-deployment"></a>(Förhandsversion) Modelldistribution utan kod

I stället för den traditionella distributionsvägen kan du också använda funktionen för distribution utan kod (förhandsversion) för TensorFlow. Genom att registrera din modell enligt vad som visas ovan med parametrarna , och kan du helt enkelt `model_framework` `model_framework_version` använda den `resource_configuration` `deploy()` statiska funktionen för att distribuera din modell.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Den fullständiga [i detalj beskriver](how-to-deploy-and-where.md) distribution i Azure Machine Learning mer djupgående.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en TensorFlow-modell och lärt dig om distributionsalternativ. I de här andra artiklarna kan du läsa mer om Azure Machine Learning.

* [Spåra körningsmått under träning](how-to-log-view-metrics.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referensarkitektur för distribuerad djupinlärningsträning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
