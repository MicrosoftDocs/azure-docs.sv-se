---
title: Träna Keras-modeller för djupinlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och registrerar en Keras-klassificeringsmodell för djupa neurala nätverk som körs på TensorFlow med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817154"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Träna Keras-modeller i stor skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina Keras-träningsskript med Azure Machine Learning.

Exempelkoden i den här artikeln visar hur du tränar och registrerar en Keras-klassificeringsmodell som skapats med TensorFlow-Azure Machine Learning. Det använder populära [MNIST-datauppsättning](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med hjälp av ett djupt neuralt nätverk (DNN) som skapats med hjälp av [Keras Python-biblioteket](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview).

Keras är ett högnivå-API för neuralt nätverk som kan köras ovanpå andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning kan du snabbt skala ut träningsjobb med beräkningsresurser i elastiska moln. Du kan också spåra dina träningskörningar, versionsmodeller, distribuera modeller och mycket mer.

Oavsett om du utvecklar en Keras-modell från grunden eller om du inför en befintlig modell i molnet kan Azure Machine Learning hjälpa dig att skapa produktionsklara modeller.

> [!NOTE]
> Om du använder Keras API **tf.keras** som är inbyggt i TensorFlow och inte det fristående Keras-paketet kan du i stället referera till [Träna TensorFlow-modeller.](how-to-train-tensorflow.md)

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av dessa miljöer:

- Azure Machine Learning beräkningsinstans – ingen nedladdning eller installation krävs

     - Slutför [självstudien: Konfigurera miljö och arbetsyta för](tutorial-1st-experiment-sdk-setup.md) att skapa en dedikerad notebook-server som är förinstallerad med SDK och exempeldatabasen.
    - I mappen samples på notebook-servern hittar du en slutförd och expanderad notebook-fil genom att gå till den här katalogen: **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras.**

 - Din egen Jupyter Notebook server

    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Skapa en konfigurationsfil för arbetsytan.](how-to-configure-environment.md#workspace)
    - [Ladda ned exempelskriptfilerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` Och `utils.py`

    Du hittar även en slutförd Jupyter Notebook [av den](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) här guiden på GitHub-exempelsidan. Anteckningsboken innehåller utökade avsnitt som omfattar intelligent justering av hyperparametrar, modelldistribution och notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet konfigureras träningsexperimentet genom att de nödvändiga Python-paketen läses in, en arbetsyta initieras, FileDataset skapas för indataträningsdata, beräkningsmålet skapas och träningsmiljön definieras.

### <a name="import-packages"></a>Importera paket

Importera först nödvändiga Python-bibliotek.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
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

Ett `FileDataset` objekt refererar till en eller flera filer i arbetsytans datalager eller offentliga URL:er. Filerna kan ha vilket format som helst och klassen ger dig möjlighet att ladda ned eller montera filerna till din beräkning. Genom att skapa `FileDataset` en skapar du en referens till datakällans plats. Om du har tillämpat några transformningar på datauppsättningen lagras de också i datauppsättningen. Data finns kvar på den befintliga platsen, så det tillkommer ingen extra lagringskostnad. Mer information [finns i i](./how-to-create-register-datasets.md) guiden om `Dataset` paketet.

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

Du kan använda metoden för att registrera datauppsättningen till din arbetsyta så att de kan delas med andra, återanvändas i olika experiment och refereras till med namnet i `register()` träningsskriptet.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Skapa ett beräkningsmål som träningsjobbet ska köras på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning ett beräkningskluster.

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

Definiera Azure [ML-miljön](concept-environments.md) som kapslar in träningsskriptens beroenden.

Definiera först dina conda-beroenden i en YAML-fil. I det här exemplet heter filen `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Skapa en Azure ML-miljö från den här conda-miljöspecifikationen. Miljön kommer att paketeras i en Docker-container vid körning.

Som standard använder Azure ML en CPU-avbildning som basavbildning om ingen basavbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` anges. Eftersom det här exemplet kör träning på ett GPU-kluster måste du ange en GPU-basavbildning som har nödvändiga GPU-drivrutiner och beroenden. Azure ML har en uppsättning basavbildningar publicerade på Microsoft Container Registry (MCR) som du kan använda. Mer information finns på GitHub-lagringsplatsen [för Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Mer information om hur du skapar och använder miljöer finns [i Skapa och använda programvarumiljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din träningskörning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig
Hämta först data från arbetsytans datalager med hjälp av `Dataset` klassen .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Skapa ett ScriptRunConfig-objekt för att ange konfigurationsinformationen för träningsjobbet, inklusive träningsskriptet, miljön som ska användas och beräkningsmålet som ska köras.

Argument till träningsskriptet skickas via kommandoraden om de anges i `arguments` parametern . DatasetConsumptionConfig för vår FileDataset skickas som ett argument till träningsskriptet för `--data-folder` argumentet . Azure ML löser datasetConsumptionConfig till monteringspunkten för det backande dataarkivet, som sedan kan nås från träningsskriptet.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns [i Konfigurera och skicka träningskörningar.](how-to-set-up-training-targets.md)

> [!WARNING]
> Om du tidigare använde TensorFlow-beräknaren för att konfigurera dina Keras-träningsjobb bör du observera att beräknare har gjorts inaktuella från och med SDK-versionen 1.19.0. Med Azure ML SDK >= 1.15.0 är ScriptRunConfig det rekommenderade sättet att konfigurera träningsjobb, inklusive de som använder djupinlärningsramverk. Vanliga migreringsfrågor finns i migreringsguiden [Estimator to ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Skicka körningen

[Körningsobjektet](/python/api/azureml-core/azureml.core.run%28class%29) tillhandahåller gränssnittet för körningshistoriken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Vad händer vid körning
När körningen körs går den igenom följande steg:

- **Förbereda:** En Docker-avbildning skapas enligt den definierade miljön. Avbildningen laddas upp till arbetsytans containerregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet. Om en rensad miljö anges i stället används den cachelagrade avbildningen som backar upp den här miljön.

- **Skalning:** Klustret försöker skala upp om Batch AI kräver fler noder för att köra körningen än vad som är tillgängligt för tillfället.

- **Körs:** Alla skript i skriptmappen laddas upp till beräkningsmålet, datalager monteras eller kopieras `script` och körs. Utdata från stdout- och **./logs-mappen** strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning:** Mappen **./outputs** för körningen kopieras till körningshistoriken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat modellen kan du registrera den på din arbetsyta. Med modellregistrering kan du lagra och versionsförse dina modeller på arbetsytan för att [förenkla modellhantering och distribution.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Distributionen innehåller ett avsnitt om att registrera modeller, men [](how-to-deploy-and-where.md#choose-a-compute-target) du kan gå direkt till att skapa ett beräkningsmål för distribution eftersom du redan har en registrerad modell.

Du kan också ladda ned en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modellverifieringsarbete lokalt. I träningsskriptet `keras_mnist.py` sparar ett TensorFlow-spararobjekt modellen i en lokal mapp (lokalt för beräkningsmålet). Du kan använda run-objektet för att ladda ned en kopia från körningshistoriken.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en Keras-modell på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår artikel om modelldistribution.

* [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körningsmått under träning](how-to-log-view-metrics.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referensarkitektur för distribuerad djupinlärningsträning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
