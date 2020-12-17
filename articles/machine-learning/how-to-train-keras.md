---
title: Träna djup inlärning keras-modeller
titleSuffix: Azure Machine Learning
description: Lär dig att träna och registrera en keras djup neurala nätverks klassificerings modell som körs på TensorFlow med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c10b2041dec0f4084578de1a72cb59cf1d7a949b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631082"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Träna keras-modeller i skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina keras-utbildnings skript med Azure Machine Learning.

Exempel koden i den här artikeln visar hur du tränar och registrerar en keras klassificerings modell som skapats med TensorFlow-backend med Azure Machine Learning. Den använder den populära [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/) för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN) som skapats med det [keras python-biblioteket](https://keras.io) som körs ovanpå [TensorFlow](https://www.tensorflow.org/overview).

Keras är en neurala nätverks-API på hög nivå som kan köra andra populära DNN-ramverk för att förenkla utvecklingen. Med Azure Machine Learning kan du snabbt skala ut utbildnings jobb med elastiska moln beräknings resurser. Du kan också spåra dina utbildnings körningar, versions modeller, distribuera modeller och mycket mer.

Oavsett om du utvecklar en keras-modell från grunden eller om du har en befintlig modell i molnet kan Azure Machine Learning hjälpa dig att skapa produktions klara modeller.

> [!NOTE]
> Om du använder keras API **TF. keras** som är inbyggd i TensorFlow och inte det fristående keras-paketet kan du läsa i stället [träna TensorFlow-modeller](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer:

- Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

     - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen Samples på Notebook-servern letar du reda på en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > keras > träna-parameter-Tune-Deploy-with-keras** Folder.

 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` särskilt `utils.py`

    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa FileDataset för indata för utbildning, skapa beräknings mål och definiera tränings miljön.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
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

Du kan använda `register()` metoden för att registrera data uppsättningen på din arbets yta, så att de kan delas med andra, återanvändas över olika experiment och refereras till av namn i ditt utbildnings skript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Skapa ett beräknings mål

Skapa ett beräknings mål för ditt utbildnings jobb att köra på. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

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

Definiera Azure ML- [miljön](concept-environments.md) som kapslar in dina utbildnings skript beroenden.

Definiera först dina Conda-beroenden i en YAML-fil. i det här exemplet heter filen `conda_dependencies.yml` .

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

Skapa en Azure ML-miljö från den här Conda-miljö specifikationen. Miljön kommer att paketeras i en Docker-behållare vid körning.

Som standard om ingen bas avbildning anges använder Azure ML en processor avbildning `azureml.core.environment.DEFAULT_CPU_IMAGE` som bas avbildningen. Eftersom det här exemplet kör utbildning i ett GPU-kluster måste du ange en GPU-bas avbildning som har nödvändiga GPU-drivrutiner och-beroenden. Azure ML innehåller en uppsättning grundläggande avbildningar som publiceras på Microsoft Container Registry (MCR) som du kan använda, se [Azure/azureml-containers](https://github.com/Azure/AzureML-Containers) GitHub lagrings platsen för mer information.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Mer information om hur du skapar och använder miljöer finns [i skapa och använda program miljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din utbildnings körning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig
Hämta först data från data lagrets data lager med hjälp av `Dataset` klassen.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Skapa ett ScriptRunConfig-objekt om du vill ange konfigurations information för ditt utbildnings jobb, inklusive ditt utbildnings skript, vilken miljö som ska användas och vilket beräknings mål som ska köras.

Alla argument till ditt utbildnings skript skickas via kommando raden om de anges i `arguments` parametern. DatasetConsumptionConfig för vår FileDataset skickas som ett argument till övnings skriptet för `--data-folder` argumentet. Azure ML kommer att lösa den här DatasetConsumptionConfig till monterings punkten för data lagret, som sedan kan nås från övnings skriptet.

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

Mer information om hur du konfigurerar jobb med ScriptRunConfig finns i [Konfigurera och skicka utbildnings körningar](how-to-set-up-training-targets.md).

> [!WARNING]
> Om du tidigare använde TensorFlow-uppskattningen för att konfigurera keras-utbildnings jobben, Tänk på att uppskattningar är föråldrade från och med 1.19.0 SDK-versionen. Med Azure ML SDK >= 1.15.0, är ScriptRunConfig det rekommenderade sättet att konfigurera utbildnings jobb, inklusive de som använder djup inlärnings ramverk. För vanliga frågor om migrering, se [uppskattningen till ScriptRunConfig migration guide](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Skicka in din körning

[Kör-objektet](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Vad händer under körningen
När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt den miljö som definierats. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet. Om en granskad miljö anges i stället används den cachelagrade avbildningen som ska användas för att återställa den hanterade miljön.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och `script` utförs. Utdata från STDOUT och **./logs** -mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen **./outputs** i körningen kopieras till körnings historiken.

## <a name="register-the-model"></a>Registrera modellen

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också hämta en lokal kopia av modellen. Detta kan vara användbart för att göra ytterligare modell validerings arbete lokalt. I övnings skriptet `keras_mnist.py` sparar ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia från körnings historiken.

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

I den här artikeln har du tränat och registrerat en keras-modell på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår modell distributions artikel.

* [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
