---
title: Träna scikit-learn-maskininlärningsmodeller
titleSuffix: Azure Machine Learning
description: Lär dig Azure Machine Learning du kan skala ut ett scikit-learn-träningsjobb med hjälp av elastiska molnresurser för beräkning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef64d94ed3e860895bcc81a1429008205a1a8acb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817115"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Träna scikit-learn-modeller i stor skala med Azure Machine Learning

I den här artikeln får du lära dig hur du kör dina scikit-learn-träningsskript med Azure Machine Learning.

Exempelskripten i den här artikeln används för att klassificera irisbilder för att skapa en maskininlärningsmodell baserad på scikit-learns [irisdatauppsättning](https://archive.ics.uci.edu/ml/datasets/iris).

Oavsett om du tränar en scikit-learn-modell för maskininlärning från grunden eller om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut träningsjobb med öppen källkod med hjälp av elastiska beräkningsresurser i molnet. Du kan skapa, distribuera, versionsövervaka och övervaka modeller i produktionsklass med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av dessa miljöer:
 - Azure Machine Learning beräkningsinstans – ingen nedladdning eller installation krävs

    - Slutför [självstudien: Konfigurera miljö och arbetsyta för](tutorial-1st-experiment-sdk-setup.md)  att skapa en dedikerad notebook-server som är förinstallerad med SDK och exempeldatabasen.
    - I mappen exempelträning på notebook-servern hittar du en slutförd och expanderad notebook-fil genom att gå till den här katalogen: **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn.**

 - Din egen Jupyter Notebook server

    - [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Skapa en konfigurationsfil för arbetsytan.](how-to-configure-environment.md#workspace)

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet konfigureras träningsexperimentet genom att de nödvändiga Python-paketen läses in, en arbetsyta initieras, träningsmiljön definieras och träningsskriptet förbereds.

### <a name="initialize-a-workspace"></a>Initiera en arbetsyta

Den [Azure Machine Learning arbetsytan](concept-workspace.md) är den översta resursen för tjänsten. Det ger dig en central plats där du kan arbeta med alla artefakter som du skapar. I Python SDK kan du komma åt arbetsytans artefakter genom att skapa ett [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) -objekt.

Skapa ett arbetsyteobjekt `config.json` från filen som skapades i avsnittet [krav.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Förbereda skript

I den här självstudien har **träningsskriptet train_iris.py** redan angetts för dig [här.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) I praktiken bör du kunna använda ett anpassat träningsskript som det är och köra det med Azure ML utan att behöva ändra koden.

Obs!
- Det tillhandahållna träningsskriptet visar hur du loggar vissa mått till din Azure ML-körning med `Run` hjälp av -objektet i skriptet.
- Det angivna träningsskriptet använder exempeldata från  `iris = datasets.load_iris()` funktionen .  Om du vill använda och komma åt dina egna data kan [du se hur du tränar med datauppsättningar för](how-to-train-with-datasets.md) att göra data tillgängliga under träningen.

### <a name="define-your-environment"></a>Definiera din miljö

Om du vill definiera Azure [ML-miljön](concept-environments.md) som kapslar in beroenden för träningsskriptet kan du antingen definiera en anpassad miljö eller använda och en azure ML-hanterad miljö.

#### <a name="use-a-curated-environment"></a>Använda en curated-miljö
Om du inte vill definiera en egen miljö kan du välja att använda Azure ML för inbyggda, hanterade miljöer. Mer information finns [här.](resource-curated-environments.md)
Om du vill använda en curated-miljö kan du köra följande kommando i stället:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Skapa en anpassad miljö

Du kan också skapa en egen anpassad miljö. Definiera dina conda-beroenden i en YAML-fil. I det här exemplet heter filen `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Skapa en Azure ML-miljö från den här Conda-miljöspecifikationen. Miljön paketeras i en Docker-container vid körning.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Mer information om hur du skapar och använder miljöer finns [i Skapa och använda programvarumiljöer i Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurera och skicka din träningskörning

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig
Skapa ett ScriptRunConfig-objekt för att ange konfigurationsinformationen för träningsjobbet, inklusive träningsskriptet, miljön som ska användas och beräkningsmålet som ska köras.
Argument till träningsskriptet skickas via kommandoraden om de anges i `arguments` parametern .

Följande kod konfigurerar ett ScriptRunConfig-objekt för att skicka jobbet för körning på den lokala datorn.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Om du i stället vill köra jobbet i ett fjärrkluster kan du ange önskat beräkningsmål `compute_target` till parametern i ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Skicka körningen
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning kör träningsskript genom att kopiera hela källkatalogen. Om du har känsliga data som du inte vill ladda upp använder du en [.ignore-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i källkatalogen . I stället kan du komma åt dina data med hjälp av en Azure [ML-datauppsättning](how-to-train-with-datasets.md).

### <a name="what-happens-during-run-execution"></a>Vad händer under körningen
När körningen körs går den igenom följande steg:

- **Förbereda:** En Docker-avbildning skapas enligt den definierade miljön. Avbildningen laddas upp till arbetsytans containerregister och cachelagras för senare körningar. Loggar strömmas också till körningshistoriken och kan visas för att övervaka förloppet. Om en rensad miljö anges i stället används den cachelagrade avbildningen som backar upp den här curated-miljön.

- **Skalning:** Klustret försöker skala upp om Batch AI kräver fler noder för att köra körningen än vad som är tillgängligt för närvarande.

- **Körs:** Alla skript i skriptmappen laddas upp till beräkningsmålet, datalager monteras eller kopieras `script` och körs. Utdata från stdout och **./logs-mappen** strömmas till körningshistoriken och kan användas för att övervaka körningen.

- **Efterbearbetning:** **Mappen ./outputs** för körningen kopieras till körningshistoriken.

## <a name="save-and-register-the-model"></a>Spara och registrera modellen

När du har tränat modellen kan du spara och registrera den på din arbetsyta. Med modellregistrering kan du lagra och versionsförse dina modeller på arbetsytan för att [förenkla modellhantering och distribution.](concept-model-management-and-deployment.md)

Lägg till följande kod i träningsskriptet, train_iris.py, för att spara modellen. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrera modellen på din arbetsyta med följande kod. Genom att ange `model_framework` `model_framework_version` parametrarna , `resource_configuration` och blir modelldistribution utan kod tillgänglig. Med modelldistribution utan kod kan du distribuera din modell direkt som en webbtjänst från den registrerade modellen, och objektet definierar [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) beräkningsresursen för webbtjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Distribution

Den modell som du precis registrerade kan distribueras på exakt samma sätt som andra registrerade modeller i Azure ML. Distributionen innehåller ett avsnitt om att registrera modeller, men [](how-to-deploy-and-where.md#choose-a-compute-target) du kan gå direkt till att skapa ett beräkningsmål för distribution eftersom du redan har en registrerad modell.

### <a name="preview-no-code-model-deployment"></a>(Förhandsversion) Modelldistribution utan kod

I stället för den traditionella distributionsvägen kan du också använda funktionen för distribution utan kod (förhandsversion) för scikit-learn. Distribution utan kodmodell stöds för alla inbyggda scikit-learn-modelltyper. Genom att registrera din modell enligt ovan med parametrarna , och kan du helt enkelt använda `model_framework` `model_framework_version` den `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statiska funktionen för att distribuera din modell.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Obs! Dessa beroenden ingår i den förbyggda scikit-learn-inferenscontainern.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Den fullständiga [i detalj beskriver](how-to-deploy-and-where.md) distributionen i Azure Machine Learning mer djupgående.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en scikit-learn-modell och lärt dig om distributionsalternativ. I de här andra artiklarna kan du läsa mer om Azure Machine Learning.

* [Spåra körningsmått under träning](how-to-log-view-metrics.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
