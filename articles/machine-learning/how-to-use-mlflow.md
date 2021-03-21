---
title: MLflow spårning för ML experiment
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att logga mått och artefakter från ML-modeller och distribuera dina ML-modeller som en webb tjänst.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 02684ba91c207357e15684870a6fa0ceab3e17ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520973"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Träna och spåra ML-modeller med MLflow och Azure Machine Learning (för hands version)

I den här artikeln lär du dig hur du aktiverar MLflow för spårnings-och loggnings-API: t, gemensamt känt som [MLflow spårning](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), för att ansluta Azure Machine Learning som Server del för dina MLflow experiment. 

Funktioner som stöds är: 

+ Spåra och logga experiment mått och artefakter i din [Azure Machine Learning-arbetsyta](./concept-azure-machine-learning-architecture.md#workspace). Om du redan använder MLflow spårning för dina experiment, tillhandahåller arbets ytan en centraliserad, säker och skalbar plats för att lagra utbildnings mått och-modeller.

+ Skicka utbildnings jobb med [MLflow-projekt](https://www.mlflow.org/docs/latest/projects.html) med Azure Machine Learning backend-support (för hands version). Du kan skicka jobb lokalt med Azure Machine Learning spårning eller migrera dina körningar till molnet som via en [Azure Machine Learning-beräkning](./how-to-create-attach-compute-cluster.md).

+ Spåra och hantera modeller i MLflow och Azure Machine Learning modell register.

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. MLFlow-spårning är en komponent i MLflow som loggar och spårar din utbildning kör mått och modell artefakter, oavsett experimentets miljö – lokalt på datorn, på ett fjärrberäknings mål, en virtuell dator eller ett [Azure Databricks-kluster](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Som ett bibliotek med öppen källkod ändras MLflow ofta. De funktioner som gjorts tillgängliga via Azure Machine Learning-och MLflow-integreringen bör därför betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Följande diagram illustrerar att med MLflow spårning spårar du ett Experiments körnings mått och lagrings modell artefakter i din Azure Machine Learning-arbetsyta.

![mlflow med Azure Machine Learning-diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är en administratör som vill övervaka resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningar eller slutförda modell distributioner, se [övervakning Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Jämför MLflow och Azure Machine Learning klienter

 I följande tabell sammanfattas de olika klienter som kan använda Azure Machine Learning och deras respektive funktioner.

 MLflow tracking erbjuder funktioner för mått loggning och artefakt lagring som endast är tillgängliga via [Azure Machine Learning python SDK](/python/api/overview/azure/ml/intro).

| Funktion | MLflow spårning & distribution | Azure Machine Learning python SDK |  Azure Machine Learning CLI | Azure Machine Learning-studio|
|---|---|---|---|---|
| Hantera arbets yta |   | ✓ | ✓ | ✓ |
| Använda data lager  |   | ✓ | ✓ | |
| Loggmått      | ✓ | ✓ |   | |
| Ladda upp artefakter | ✓ | ✓ |   | |
| Visa mått     | ✓ | ✓ | ✓ | ✓ |
| Hantera beräkning   |   | ✓ | ✓ | ✓ |
| Distribuera modeller    | ✓ | ✓ | ✓ | ✓ |
|Övervaka modell prestanda||✓|  |   |
| Identifiera dataavvikelser |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Förutsättningar

* Installera paketet `azureml-mlflow`. 
    * Det här paketet kommer automatiskt in i `azureml-core` [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install), som ger anslutningen för MLflow åtkomst till din arbets yta.
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
    * Se vilka [åtkomst behörigheter du behöver för att utföra dina MLflow-åtgärder med din arbets yta](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Spåra lokala körningar

Genom MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina lokala körningar i Azure Machine Learning arbets ytan.

Importera `mlflow` och- [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) klasserna för att få åtkomst till MLflows spårnings-URI och konfigurera din arbets yta.

I följande kod `get_mlflow_tracking_uri()` tilldelar metoden en unik spårnings-URI-adress till arbets ytan, `ws` och `set_tracking_uri()` pekar på MLflow spårnings-URI: n till den adressen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Spårnings-URI: n är giltig till en timme eller mindre. Om du startar om skriptet efter inaktivitet använder du get_mlflow_tracking_uri API för att hämta en ny URI.

Ange MLflow experiment namn med `set_experiment()` och starta din utbildning med `start_run()` . Använd sedan `log_metric()` för att aktivera loggnings-API: et för MLflow och börja logga dina utbildnings körnings mått.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Spåra fjärrkörningar

Med fjärrkörningar kan du träna dina modeller på mer kraftfulla beräkningar, till exempel GPU-aktiverade virtuella datorer eller Machine Learning-beräkning kluster. Se [använda beräknings mål för modell utbildning](how-to-set-up-training-targets.md) för att lära dig mer om olika beräknings alternativ.

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från fjärrdatorn som körs i Azure Machine Learning arbets ytan. Alla körningar med MLflow spårnings kod i den kommer att ha statistik som loggas automatiskt till arbets ytan. 

I följande exempel Conda-miljö ingår `mlflow` och `azureml-mlflow` som pip-paket. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Konfigurera din beräknings-och övnings körnings miljö med klassen i skriptet [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) . Skapa sedan  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) med fjärrberäkning som beräknings mål.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Med den här beräknings-och övnings körnings konfigurationen använder du `Experiment.submit()` metoden för att skicka in en körning. Den här metoden anger automatiskt MLflow spårnings-URI och dirigerar loggningen från MLflow till din arbets yta.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Träna med MLflow-projekt

Med [MLflow-projekt](https://mlflow.org/docs/latest/projects.html) kan du ordna och beskriva din kod för att låta andra data experter (eller automatiserade verktyg) köra den. Med MLflow-projekt med Azure Machine Learning kan du spåra och hantera dina utbildnings körningar i din arbets yta. 

Det här exemplet visar hur du skickar MLflow-projekt lokalt med Azure Machine Learning spårning.

Installera `azureml-mlflow` paketet om du vill använda MLflow spårning med Azure Machine Learning på dina experiment lokalt. Experimenten kan köras via en Jupyter Notebook eller kod redigeraren.

```shell
pip install azureml-mlflow
```

Importera `mlflow` och- [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) klasserna för att få åtkomst till MLflows spårnings-URI och konfigurera din arbets yta.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Ange MLflow experiment namn med `set_experiment()` och starta din utbildning med `start_run()` . Använd sedan `log_metric()` för att aktivera loggnings-API: et för MLflow och börja logga dina utbildnings körnings mått.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Skapa konfigurations objekt för Server delen för att lagra nödvändig information för integreringen, till exempel beräknings målet och vilken typ av hanterad miljö som ska användas.

```python
backend_config = {"USE_CONDA": False}
```
Lägg till `azureml-mlflow` paketet som ett pip-beroende i miljö konfigurations filen för att kunna spåra mått och nyckel artefakter i din arbets yta. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Skicka den lokala körningen och se till att du anger parametern `backend = "azureml" ` . Med den här inställningen kan du skicka körningar lokalt och få till gång till stöd för automatisk utdata, loggfiler, ögonblicks bilder och utskrifts fel i arbets ytan. 

Visa dina körningar och mått i [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visa mått och artefakter i din arbets yta

Måtten och artefakterna från MLflow-loggning behålls i din arbets yta. Om du vill visa dem när som helst, navigerar du till din arbets yta och hittar experimentet efter namn i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).  Eller kör koden nedan. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Hantera modeller 

Registrera och spåra dina modeller med [Azure Machine Learning Model-registret](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) som stöder MLflow Model-registret. Azure Machine Learning modeller justeras med MLflow modell schema och gör det enkelt att exportera och importera dessa modeller över olika arbets flöden. MLflow relaterade metadata som, till exempel körnings-ID taggas också med den registrerade modellen för spårning. Användarna kan skicka in utbildnings körningar, registrera och distribuera modeller som skapats från MLflow-körningar. 

Om du vill distribuera och registrera din produktions färdiga modell i ett enda steg, se [distribuera och registrera MLflow-modeller](how-to-deploy-mlflow-models.md).

Använd följande steg för att registrera och visa en modell från en körning:

1. Anropa metoden när körningen är klar `register_model()` .

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Visa den registrerade modellen i din arbets yta med [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    I följande exempel har den registrerade modellen `my-model` MLflow spårning av metadata som taggats. 

    ![registrera-mlflow-Model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Välj fliken **artefakter** om du vill se alla konsolfiler som överensstämmer med MLflow-modell schema (Conda. yaml, MLmodel, Model. PKL).

    ![modell schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Välj MLmodel för att se MLmodel-filen som genereras av körningen.

    ![MLmodel – schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda de loggade måtten och artefakterna i din arbets yta är möjligheten att ta bort dem individuellt otillgänglig. Ta i stället bort resurs gruppen som innehåller lagrings kontot och arbets ytan, så du debiteras inte några avgifter:

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.

   ![Ta bort i Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Välj den resursgrupp i listan som du har skapat.

1. Välj **Ta bort resursgrupp**.

1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

## <a name="example-notebooks"></a>Exempelnotebook-filer

[MLflow med Azure ml-anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstrerar och utökar begrepp som presenteras i den här artikeln.

> [!NOTE]
> En community-driven databas med exempel som använder mlflow finns på https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Nästa steg

* [Distribuera modeller med MLflow](how-to-deploy-mlflow-models.md).
* Övervaka dina produktions modeller för [data avvikelser](./how-to-enable-data-collection.md).
* [Spåra Azure Databricks körs med MLflow](how-to-use-mlflow-azure-databricks.md).
* [Hantera dina modeller](concept-model-management-and-deployment.md).