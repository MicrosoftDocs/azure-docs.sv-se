---
title: Träna och distribuera en TensorFlow-modell
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning ger dig möjlighet att skala ut ett TensorFlow utbildnings jobb med elastiska moln beräknings resurser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c82b1ffbb005542822016a55346d9067e23050b2
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630878"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Träna TensorFlow-modeller i skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina [TensorFlow](https://www.tensorflow.org/overview) -utbildnings skript i skala med hjälp av Azure Machine Learning.

Detta exempel tågen och registrerar en TensorFlow modell för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN).

Oavsett om du utvecklar en TensorFlow-modell från grunden eller om du har en [befintlig modell](how-to-deploy-existing-model.md) i molnet kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod för att skapa, distribuera, skapa och övervaka modeller för produktions miljön.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

 - Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

     - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel djup inlärning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > tensorflow > träna--parameter-Tune-Deploy-with-tensorflow** -mappen. 
 
 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` särskilt `utils.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa beräknings målet och definiera inlärnings miljön.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

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

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Skapa en fil data uppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i data lagret för din arbets yta eller offentliga URL: er. Filerna kan vara i valfritt format, och klassen ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Genom att skapa en `FileDataset` skapar du en referens till data käll platsen. Om du har tillämpat eventuella omvandlingar till data uppsättningen lagras de även i data uppsättningen. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår. Mer information finns i [instruktions](./how-to-create-register-datasets.md) guiden `Dataset` för.

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

Använd `register()` metoden för att registrera data uppsättningen på din arbets yta så att de kan delas med andra, återanvändas över olika experiment och refereras till av namn i ditt utbildnings skript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Skapa ett beräknings mål

Skapa ett beräknings mål för ditt TensorFlow-jobb som ska köras. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

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
Azure ML tillhandahåller färdiga, granskade miljöer om du inte vill definiera en egen miljö. Azure ML har flera processor-och GPU-granskade miljöer för TensorFlow som motsvarar olika versioner av TensorFlow. Mer information finns [här](resource-curated-environments.md).

Om du vill använda en granskad miljö kan du köra följande kommando i stället:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Om du vill se de paket som ingår i den granskade miljön kan du skriva ut Conda-beroenden till disk:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Se till att den granskade miljön innehåller alla beroenden som krävs av ditt utbildnings skript. Om inte, måste du ändra miljön för att inkludera de beroenden som saknas. Observera att om miljön ändras måste du ge den ett nytt namn eftersom prefixet "AzureML" är reserverat för granskade miljöer. Om du har ändrat Conda-beroende YAML-filen kan du skapa en ny miljö från den med ett nytt namn, t. ex.:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Om du i stället har ändrat det granskade miljö objektet direkt, kan du klona miljön med ett nytt namn:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
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
  - tensorflow-gpu==2.2.0
```

Skapa en Azure ML-miljö från den här Conda-miljö specifikationen. Miljön kommer att paketeras i en Docker-behållare vid körning.

Som standard om ingen bas avbildning anges använder Azure ML en processor avbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` som bas avbildningen. Eftersom det här exemplet kör utbildning i ett GPU-kluster måste du ange en GPU-bas avbildning som har nödvändiga GPU-drivrutiner och-beroenden. Azure ML innehåller en uppsättning grundläggande avbildningar som publiceras på Microsoft Container Registry (MCR) som du kan använda, se [Azure/azureml-containers](https://github.com/Azure/AzureML-Containers) GitHub lagrings platsen för mer information.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Om du vill kan du bara samla in alla dina beroenden direkt i en anpassad Docker-avbildning eller Dockerfile och skapa din miljö. Mer information finns i [träna med anpassad bild](how-to-train-with-custom-image.md).

Mer information om hur du skapar och använder miljöer finns [i skapa och använda program miljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din utbildnings körning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig

Skapa ett [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) -objekt om du vill ange konfigurations information för ditt utbildnings jobb, inklusive ditt utbildnings skript, vilken miljö som ska användas och vilket beräknings mål som ska köras. Alla argument till ditt utbildnings skript skickas via kommando raden om de anges i `arguments` parametern.

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
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. Gå i stället till dina data med hjälp av en Azure ML- [datauppsättning](how-to-train-with-datasets.md).

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns i [Konfigurera och skicka utbildnings körningar](how-to-set-up-training-targets.md).

> [!WARNING]
> Om du tidigare använde TensorFlow-uppskattningen för att konfigurera TensorFlow-utbildnings jobben, Tänk på att uppskattningar är föråldrade från och med 1.19.0 SDK-versionen. Med Azure ML SDK >= 1.15.0, är ScriptRunConfig det rekommenderade sättet att konfigurera utbildnings jobb, inklusive de som använder djup inlärnings ramverk. För vanliga frågor om migrering, se [uppskattningen till ScriptRunConfig migration guide](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Vad händer under körningen
När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt den miljö som definierats. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet. Om en granskad miljö anges i stället används den cachelagrade avbildningen som ska användas för att återställa den hanterade miljön.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och `script` utförs. Utdata från STDOUT och **./logs** -mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen **./outputs** i körningen kopieras till körnings historiken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md). Valfritt: genom att ange parametrarna `model_framework` , `model_framework_version` , och `resource_configuration` , blir modell distribution utan kod tillgängligt. På så sätt kan du distribuera din modell direkt som en webb tjänst från den registrerade modellen, och `ResourceConfiguration` objektet definierar beräknings resursen för webb tjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Du kan också hämta en lokal kopia av modellen med hjälp av objektet kör. I övnings skriptet `tf_mnist.py` behåller ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Distribuerad träning

Azure Machine Learning stöder även distribuerade TensorFlow-jobb med flera noder så att du kan skala dina utbildnings arbets belastningar. Du kan enkelt köra distribuerade TensorFlow-jobb och Azure ML för att hantera dirigeringen åt dig.

Azure ML stöder körning av distribuerade TensorFlow-jobb med både Horovod och TensorFlows inbyggda API för distribuerad utbildning.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är en öppen källkod som alla reducerar ramverk för distribuerad utbildning som utvecklas av Uber. Den erbjuder en enkel väg till att skriva distribuerad TensorFlow-kod för utbildning.

Din utbildnings kod måste instrumenteras med Horovod för distribuerad utbildning. Mer information om hur du använder Horovod med TensorFlow finns i Horovod-dokumentationen:

Mer information om hur du använder Horovod med TensorFlow finns i Horovod-dokumentationen:

* [Horovod med TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod med TensorFlow s keras-API](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Se dessutom till att din utbildnings miljö innehåller **horovod** -paketet. Om du använder en TensorFlow-granskad miljö ingår horovod redan som en av beroendena. Om du använder en egen miljö kontrollerar du att horovod-beroendet ingår, till exempel:

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

För att kunna köra ett distribuerat jobb med MPI/Horovod på Azure ML måste du ange en [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) till- `distributed_job_config` parametern för ScriptRunConfig-konstruktorn. I koden nedan kan du konfigurera ett distribuerat jobb med två noder som kör en process per nod. Om du också vill köra flera processer per nod (dvs. om klustrets SKU har flera GPU: er) anger du även `process_count_per_node` parametern i MpiConfiguration (Standardvärdet är `1` ).

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

En fullständig själv studie kurs om att köra distribuerade TensorFlow med Horovod på Azure ML finns i [distribuerade TensorFlow med Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. distribuera

Om du använder [interna distribuerade TensorFlow](https://www.tensorflow.org/guide/distributed_training) i din utbildnings kod, t. ex. TensorFlow 2. x s `tf.distribute.Strategy` API, kan du också starta det distribuerade jobbet via Azure ml. 

Det gör du genom att ange en [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) till- `distributed_job_config` parametern för ScriptRunConfig-konstruktorn. Om du använder `tf.distribute.experimental.MultiWorkerMirroredStrategy` anger du `worker_count` i TensorflowConfiguration som motsvarar antalet noder för utbildnings jobbet.

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

I TensorFlow `TF_CONFIG` krävs miljö variabeln för utbildning på flera datorer. Azure ML konfigurerar och ställer in `TF_CONFIG` variabeln på lämpligt sätt för varje arbets tagare innan du kör ditt utbildnings skript. Du kan komma åt `TF_CONFIG` från ditt utbildnings skript om du behöver via `os.environ['TF_CONFIG']` .

Exempel struktur som är `TF_CONFIG` inställd på en Chief Worker-nod:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Om ditt utbildnings skript använder parameter Server strategin för distribuerad utbildning, d.v.s. för äldre TensorFlow 1. x, måste du också ange antalet parameter servrar som ska användas i jobbet, t. ex. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Distribuera en TensorFlow-modell

Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

### <a name="preview-no-code-model-deployment"></a>Förhandsgranskningsvyn Distribution utan kod modell

I stället för den traditionella distributions vägen kan du också använda funktionen utan kod distribution (för hands version) för TensorFlow. Genom att registrera din modell enligt vad som visas ovan med `model_framework` `model_framework_version` parametrarna,, och `resource_configuration` kan du bara använda den `deploy()` statiska funktionen för att distribuera modellen.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Den fullständiga [instruktionen att](how-to-deploy-and-where.md) distribuera i Azure Machine Learning större djup.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en TensorFlow-modell och lärt dig om distributions alternativen. Mer information om Azure Machine Learning finns i de här artiklarna.

* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
