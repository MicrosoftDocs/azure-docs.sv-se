---
title: Skapa automatiska ML-experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar data källor, beräknings-och konfigurations inställningar för dina automatiserade maskin inlärnings experiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 9021d933e3808867ec784ad3c6d0f8810d608ea3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600072"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i Python


I den här guiden får du lära dig hur du definierar olika konfigurations inställningar för dina automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Automatisk maskin inlärning väljer en algoritm och sina egenskaper för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskin inlärnings experiment.

Om du vill visa exempel på automatiserade Machine Learning-experiment, se [Självstudier: träna en klassificerings modell med automatiserad maskin inlärning](tutorial-auto-train-models.md) eller [träna modeller med automatiserad maskin inlärning i molnet](how-to-auto-train-remote.md).

Konfigurations alternativ som är tillgängliga i Automatisk maskin inlärning:

* Välj experiment typ: klassificering, regression eller tids serie prognoser
* Data källa, format och hämta data
* Välj ditt beräknings mål: lokal eller fjärran sluten
* Automatiserade experiment inställningar för Machine Learning
* Köra ett automatiserat maskininlärningsexperiment
* Utforska modell mått
* Registrera och distribuera modell

Om du föredrar en ingen kod upplevelse kan du också [skapa dina automatiserade maskin inlärnings experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du 
* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).

* Azure Machine Learning python SDK har installerats.
    Om du vill installera SDK kan du antingen 
    * Skapa en beräknings instans som automatiskt installerar SDK och som är förkonfigurerat för ML-arbetsflöden. Mer information finns i [skapa och hantera en Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md) . 

    * [Installera `automl` paketet själv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), som innehåller [standard installationen](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) av SDK.

## <a name="select-your-experiment-type"></a>Välj experimenttyp

Innan du påbörjar experimentet bör du bestämma vilken typ av Machine Learning-problem som du löser. Automatiserad Machine Learning stöder aktivitets typer av `classification` , `regression` och `forecasting` . Läs mer om [aktivitets typer](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Följande kod använder- `task` parametern i `AutoMLConfig` konstruktorn för att ange experiment typen som `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datakällor och format

Automatiserad maskininlärning har stöd för både lokala data och data i molnlagring som Azure Blob Storage. Data kan läsas i en Pandas- **DataFrame** eller en **Azure Machine Learning TabularDataset**. [Läs mer om datamängder](how-to-create-register-datasets.md).

Krav för tränings data i Machine Learning:
- Data måste vara i tabell form.
- Värdet som ska förutsäga, mål kolumnen, måste finnas i datan.

**För fjärrexperiment** måste inlärnings data vara tillgängliga från fjärrdatorn. AutoML tar bara emot [TabularDatasets i Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) när du arbetar från en fjärransluten dator. 

Azure Machine Learning-datamängder exponerar funktioner för att:

* Överför enkelt data från statiska filer eller URL-källor till din arbets yta.
* Göra data tillgängliga för träningsskript när de körs på beräkningsresurser i molnet. Se [hur du tränar med data uppsättningar](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) för att få ett exempel på hur du använder `Dataset` klassen för att montera data till fjärrdatorns beräknings mål.

Följande kod skapar en TabularDataset från en webb-URL. Se [skapa en TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) för kod exempel för hur du skapar data uppsättningar från andra källor som lokala filer och data lager.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**För lokala Compute-experiment** rekommenderar vi Pandas-dataframes för snabbare bearbetnings tider.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Utbildning, verifiering och test av data

Du kan ange separata **tränings data och verifierings data uppsättningar** direkt i `AutoMLConfig` konstruktorn. Läs mer om [hur du konfigurerar data delningar och kors validering](how-to-configure-cross-validation-data-splits.md) för dina AutoML experiment. 

Om du inte uttryckligen anger en `validation_data` -eller `n_cross_validation` -parameter, använder automatisk ml standard tekniker för att avgöra hur valideringen utförs. Den här bestämningen beror på antalet rader i data uppsättningen som har tilldelats till din `training_data` parameter. 

|Tränings &nbsp; data &nbsp; storlek| Verifierings teknik |
|---|-----|
|**Större &nbsp; än &nbsp; 20 000 &nbsp; rader**| Data delning för träna/verifiering används. Standardvärdet är att ta 10% av den inledande tränings data uppsättningen som validerings uppsättningen. I sin tur används validerings uppsättningen för mått beräkning.
|**Mindre &nbsp; än &nbsp; 20 000 &nbsp; rader**| Metoden för kors validering används. Standard antalet vikningar beror på antalet rader. <br> **Om data uppsättningen är mindre än 1 000 rader** används 10 vikning. <br> **Om raderna är mellan 1 000 och 20 000** används tre vik.

För tillfället måste du ange dina egna **test data** för utvärdering av modeller. Ett kod exempel på hur du får dina egna test data för modell utvärdering finns i avsnittet **test** i [den här Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Härnäst bestämmer var modellen ska tränas. Ett träningsexperiment i automatiserad maskininlärning kan köras med följande beräkningsalternativ. Läs mer om [fördelar och nackdelar med lokala och fjärranslutna beräkningar](concept-automated-ml.md#local-remote). 

* Din **lokala** dator, till exempel ett lokalt skriv bord eller en bärbar dator – vanligt vis när du har en liten data uppsättning och du fortfarande befinner dig i utforsknings fasen. Det finns ett exempel med lokala beräkningar i [den här notebook-filen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* En **fjärrdator** i molnet – [Azure Machine Learning hanterad beräkning](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör det möjligt att träna maskin inlärnings modeller på kluster av virtuella Azure-datorer. 

    Det finns ett exempel med fjärrberäkningar med Azure Machine Learning Managed Compute i [den här notebook-filen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* Ett **Azure Databricks kluster** i din Azure-prenumeration. Du hittar mer information i [Konfigurera ett Azure Databricks-kluster för automatisk ml](how-to-configure-databricks-automl-environment.md). Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på antecknings böcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera dina experiment inställningar

Det finns flera alternativ som du kan använda för att konfigurera ditt automatiserade experiment för maskin inlärning. Dessa parametrar anges genom att instansiera ett `AutoMLConfig` objekt. Se [AutoMLConfig-klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) för en fullständig lista över parametrar.

Några exempel är:

1. Klassificerings experiment med AUC viktat som primärt mått med experiment tids gräns minuter angivet till 30 minuter och 2 kors validerings vikning.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. I följande exempel är ett Regressions experiment inställt på att sluta efter 60 minuter med fem verifierings kors veck.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Prognosticering-uppgifter kräver extra konfiguration. mer information finns i artikeln [träna en Time-Series prognos modell](how-to-auto-train-forecast.md) . 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Modeller som stöds

Automatisk Machine Learning försöker med olika modeller och algoritmer under Automation-och fin justerings processen. Som användare behöver du inte ange algoritmen. 

De tre olika `task` parametervärdena bestämmer listan över algoritmer, eller modeller, som ska användas. Använd `allowed_models` parametrarna eller `blocked_models` för att ytterligare ändra iterationer med tillgängliga modeller som ska tas med eller undantas. 

I följande tabell sammanfattas de modeller som stöds efter uppgifts typ. 

> [!NOTE]
> Om du planerar att exportera dina Auto ML-skapade modeller till en [ONNX-modell](concept-onnx.md), kan bara de algoritmer som anges med en * konverteras till ONNX-formatet. Lär dig mer om [att konvertera modeller till ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observera också att ONNX endast stöder klassificerings-och Regressions uppgifter för tillfället. 

Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
[Logistisk regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Stöd vektor klassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Genomsnittlig Perceptron-klassificerare](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Brantaste modellerings regressor för online-gradient](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[ARIMA automatiskt](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast linjär modellerings regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linjär SVM-klassificerare](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Primärt mått
`primary metric`Parametern bestämmer det mått som ska användas vid modell träning för optimering. Tillgängliga mått som du kan välja bestäms av den aktivitets typ som du väljer och följande tabell visar giltiga primära mått för varje aktivitets typ.

Lär dig mer om de olika definitionerna av dessa mått i [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

|Klassificering | Regression | Prognostisering för tidsserier
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Data funktionalisering

I varje automatiserad maskin inlärnings experiment skalas dina data automatiskt och normaliseras för att hjälpa *vissa* algoritmer som är känsliga för funktioner som är i olika skalor. Den här skalningen och normaliseringen kallas funktionalisering. Mer information och kod exempel finns i [funktionalisering i AutoML](how-to-configure-auto-features.md#) . 

När du konfigurerar experiment i `AutoMLConfig` objektet kan du aktivera/inaktivera inställningen `featurization` . I följande tabell visas de accepterade inställningarna för funktionalisering i [AutoMLConfig-objektet](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Funktionalisering-konfiguration | Beskrivning |
| ------------- | ------------- |
|`"featurization": 'auto'`| Anger att [data guardrails och funktionalisering-steg](how-to-configure-auto-features.md#featurization) utförs automatiskt när en del av förbearbetningen. **Standardinställning**.|
|`"featurization": 'off'`| Anger att funktionalisering-steget inte ska göras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att det anpassade funktionalisering-steget ska användas. [Lär dig hur du anpassar funktionalisering](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Konfiguration av Ensemble

Ensemble-modeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en AutoML-körning. För närvarande stöds **VotingEnsemble** och **StackEnsemble** . 

Röstningen implementerar mjuk röstning, som använder viktade medelvärden. Stack implementeringen använder en implementering på två nivåer, där det första lagret har samma modeller som en ensemble-Ensemble, och den andra skikt modellen används för att hitta den optimala kombinationen av modeller från det första lagret. 

Om du använder ONNX-modeller **eller** om modell förklarande är aktiverat, är stackning inaktive rad och endast röstning används.

Ensemble-utbildning kan inaktive ras `enable_voting_ensemble` med `enable_stack_ensemble` parametrarna och booleska.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Om du vill ändra standard-Ensemble-beteendet finns det flera standard argument som kan anges som `kwargs` i ett `AutoMLConfig` objekt.

> [!IMPORTANT]
>  Följande parametrar är inte explicita parametrar för AutoMLConfig-klassen. 

* `ensemble_download_models_timeout_sec`: Vid genereringen av **VotingEnsemble** och **StackEnsemble** -modellen laddas flera monterade modeller från de tidigare underordnade körningarna ned. Om du stöter på det här felet `AutoMLEnsembleException: Could not find any models for running ensembling` kan du behöva ange mer tid för att modeller ska laddas ned. Standardvärdet är 300 sekunder för nedladdning av dessa modeller parallellt och det finns ingen maximal tids gräns. Konfigurera den här parametern med ett högre värde än 300 sekunder, om mer tid behövs. 

  > [!NOTE]
  >  Om tids gränsen har nåtts och det finns hämtade modeller, fortsätter ensembling med så många modeller som den har laddat ned. Det krävs inte att alla modeller måste laddas ned för att slutföras inom tids gränsen.

Följande parametrar gäller endast för **StackEnsemble** -modeller: 

* `stack_meta_learner_type`: meta-eleven är en modell som är utbildad i resultatet av de enskilda heterogena modellerna. Standard-metadata är `LogisticRegression` för klassificerings aktiviteter (eller `LogisticRegressionCV` om kors validering är aktiverat) och `ElasticNet` för Regressions-/prognos aktiviteter (eller `ElasticNetCV` om kors validering är aktive rad). Den här parametern kan vara en av följande strängar:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` eller `LinearRegression` .

* `stack_meta_learner_train_percentage`: anger den del av inlärnings uppsättningen (när du väljer tåg-och validerings typ för utbildning) som ska reserveras för att träna meta-eleven. Standardvärdet är `0.2`. 

* `stack_meta_learner_kwargs`: valfria parametrar som ska skickas till initieraren för meta-eleven. Dessa parametrar och parameter typer speglar parametrar och parameter typer från motsvarande modell-konstruktor och vidarebefordras till modell-konstruktorn.

Följande kod visar ett exempel på hur du anger anpassade Ensemble-beteenden i ett `AutoMLConfig` objekt.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Avslutnings villkor

Det finns några alternativ som du kan definiera i AutoMLConfig för att avsluta experimentet.

|Kriterie| beskrivning
|----|----
Inga &nbsp; kriterier | Om du inte definierar några avslutnings parametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
Efter &nbsp; en &nbsp; viss &nbsp; &nbsp; tid| Använd `experiment_timeout_minutes` i dina inställningar för att definiera hur lång tid, i minuter, som experimentet ska fortsätta att köras. <br><br> För att undvika tids brist på experiment, det finns minst 15 minuter eller 60 minuter om raden efter kolumn storlek överskrider 10 000 000.
&nbsp;Poängen &nbsp; har &nbsp; &nbsp; nåtts| Använd `experiment_exit_score` Slutför experimentet när en angiven primär mått Poäng har nåtts.

## <a name="run-experiment"></a>Kör experiment

För automatisk ML skapar du ett `Experiment` -objekt, som är ett namngivet objekt i en som `Workspace` används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experimentet för att köra och generera en modell. Skicka `AutoMLConfig` till- `submit` metoden för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Inställning `show_output` till `True` resultat i utdata som visas i-konsolen.

### <a name="multiple-child-runs-on-clusters"></a>Flera underordnade körs i kluster

Automatiserade ML experiment med underordnade körningar kan utföras på ett kluster som redan kör ett annat experiment. Tiden är dock beroende av hur många noder klustret har, och om noderna är tillgängliga för att köra ett annat experiment.

Varje nod i klustret fungerar som en enskild virtuell dator (VM) som kan utföra en enda utbildning. för automatisk ML innebär en underordnad körning. Om alla noder är upptagna är det nya experimentet placerat i kö. Men om det finns kostnads fria noder kommer det nya experimentet att köra automatiserad ML-underordnad parallellt i tillgängliga noder/virtuella datorer.

För att hantera underordnade körningar och när de kan utföras rekommenderar vi att du skapar ett dedikerat kluster per experiment och matchar antalet `max_concurrent_iterations` noder i klustret. På så sätt använder du alla noder i klustret samtidigt med antalet samtidiga underordnade körningar/iterationer som du vill ha.

Konfigurera  `max_concurrent_iterations` i `AutoMLConfig` objektet. Om den inte har kon figurer ATS tillåts som standard bara en samtidiga underordnade körning/iteration per experiment.  

## <a name="explore-models-and-metrics"></a>Utforska modeller och mått

Du kan visa dina utbildnings resultat i en widget eller infogad om du befinner dig i en bärbar dator. Se [spåra och utvärdera modeller](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) för mer information.

Se [utvärdera automatiserade experiment resultat för maskin inlärning](how-to-understand-automated-ml.md) för definitioner och exempel på prestanda diagram och mått som tillhandahålls för varje körning. 

För att få en funktionalisering-Sammanfattning och förstå vilka funktioner som har lagts till i en viss modell, se [funktionalisering Transparency](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Algoritmerna för automatisk ML-användning har potentiell slumpmässig het som kan orsaka smärre variationer i en rekommenderad modells slutliga mått poäng, som noggrannhet. Med automatisk ML utförs även åtgärder för data som träna-test-delning, träna eller kors validering när det behövs. Så om du kör ett experiment med samma konfigurations inställningar och primär mått flera gånger, kommer du troligen att se variationen i varje experiment slutliga Mät resultat på grund av dessa faktorer. 

## <a name="register-and-deploy-models"></a>Registrera och distribuera modeller

Information om hur du hämtar eller registrerar en modell för distribution till en webb tjänst finns i [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modell tolkning kan du förstå varför dina modeller har gjort förutsägelser och de underliggande funktions prioritets värdena. SDK: n innehåller olika paket för att aktivera modell tolknings funktioner, både vid utbildning och drift störningar, för lokala och distribuerade modeller.

Se anvisningar [för kod exempel för hur](how-to-machine-learning-interpretability-automl.md) du aktiverar tolknings funktioner specifikt inom automatiserade maskin inlärnings experiment.

Allmän information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](how-to-machine-learning-interpretability.md) om tolkning.

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av förklarings klienten. Den här modellen returnerar ingen förklarings instrument panel om den returneras som den bästa modellen och inte stöder förklarings körningar på begäran.

## <a name="troubleshooting"></a>Felsökning

* **Den senaste uppgraderingen av `AutoML` beroenden till nyare versioner kommer att brytas**: från och med version 1.13.0 av SDK kommer modeller inte att läsas in i äldre SDK: er på grund av inkompatibilitet mellan äldre versioner som vi har fäst i våra tidigare paket och de nyare versioner vi fäster nu. Du får ett fel meddelande som:
  * Modulen hittades inte: t. ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Import fel: t. ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Fel i attribut: t. ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Undvik det här problemet genom att göra något av följande två steg beroende på din SDK- `AutoML` utbildnings version:
    * Om din `AutoML` SDK-tränings version är större än 1.13.0, behöver du `pandas == 0.25.1` och `sckit-learn==0.22.1` . Om det finns en versions konflikt uppgraderar du scikit-lära och/eller Pandas för att korrigera versionen enligt nedan:
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Om din `AutoML` SDK-tränings version är mindre än eller lika med 1.12.0, behöver du `pandas == 0.23.4` och `sckit-learn==0.20.3` . Om det finns en versions konflikt kan du nedgradera scikit – lära och/eller Pandas för att korrigera versionen enligt nedan:
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Misslyckad distribution**: för versioner <= 1.18.0 av SDK kan bas avbildningen som skapats för distribution Miss lyckas med följande fel: "ImportError: kan inte importera namn `cached_property` från `werkzeug` ". 

  Följande steg kan lösa problemet:
  1. Ladda ned modell paketet
  2. Packa upp paketet
  3. Distribuera med hjälp av unzippade till gångar

* **Prognosticering R2-poängen är alltid noll**: det här problemet uppstår om de angivna tränings data har tids serier som innehåller samma värde för de sista `n_cv_splits`  +  `forecasting_horizon` data punkterna. Om det här mönstret förväntas i din tids serie kan du växla det primära måttet till normaliserat rot genomsnitts fel i roten.
 
* **TensorFlow**: från och med version 1.5.0 av SDK installerar automatiserad Machine Learning inte TensorFlow-modeller som standard. Installera TensorFlow och Använd det med dina automatiserade ML-experiment genom att installera TensorFlow = = 1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Experiment diagram**: binära klassificerings diagram (precision-återkallande, Roc, kurva osv.) som visas i automatiserade ml experiment-iterationer återges inte korrekt i användar gränssnittet sedan 4/12. Diagram observationer visar för närvarande inversa resultat, där modeller med bättre prestanda visas med lägre resultat. En lösning är under undersökning.

* **Databricks avbryter en automatiserad maskin inlärnings körning**: när du använder automatiserade funktioner för maskin inlärning på Azure Databricks, för att avbryta en körning och starta en ny experiment körning, startar du om Azure Databricks klustret.

* **Databricks >10 iterationer för automatisk maskin inlärning**: i automatiserade inställningar för maskin inlärning, om du har fler än 10 iterationer, anger du `show_output` `False` när du skickar in körningen.

* **Databricks-widget för Azure Machine Learning SDK och automatisk maskin inlärning**: widgeten Azure Machine Learning SDK stöds inte i en Databricks Notebook eftersom antecknings böckerna inte kan parsa HTML-widgetar. Du kan visa widgeten i portalen genom att använda den här python-koden i din Azure Databricks Notebook-cell:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup Miss lyckas**: 
    * I Windows kör du automl_setup från en Anaconda-prompt. Använd den här länken för att [Installera Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Se till att Conda 64-bit är installerad, i stället för 32-bitars genom att köra `conda info` kommandot. `platform`Ska vara `win-64` för Windows eller `osx-64` Mac.
    * Se till att Conda 4.4.10 eller senare är installerat. Du kan kontrol lera versionen med kommandot `conda -V` . Om du har installerat en tidigare version kan du uppdatera den med hjälp av kommandot: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Om `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` felet uppstår installerar du build Essentials med hjälp av kommandot `sudo apt-get install build-essential` .
      * Skicka ett nytt namn som den första parametern till automl_setup för att skapa en ny Conda-miljö. Visa befintliga Conda-miljöer med `conda env list` och ta bort dem med `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh Miss lyckas**: om automl_setup_linus. sh Miss lyckas på Ubuntu Linux med felet: `unable to execute 'gcc': No such file or directory`-
  1. Se till att de utgående portarna 53 och 80 är aktiverade. På en virtuell Azure-dator kan du göra detta från Azure Portal genom att välja den virtuella datorn och klicka på nätverk.
  2. Kör kommandot: `sudo apt-get update`
  3. Kör kommandot: `sudo apt-get install build-essential --fix-missing`
  4. Kör `automl_setup_linux.sh` igen

* **konfiguration. ipynb Miss lyckas**:
  * För lokala Conda kontrollerar du först att automl_setup har körts.
  * Kontrol lera att subscription_id är korrekt. Hitta subscription_id i Azure Portal genom att välja alla tjänster och sedan prenumerationer. Tecknen "<" och ">" ska inte tas med i subscription_id svärdet. Har till exempel `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` det giltiga formatet.
  * Se till att deltagar-eller ägar åtkomst till prenumerationen.
  * Kontrol lera att regionen är en av de regioner som stöds:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` `southcentralus` .
  * Se till att du har åtkomst till regionen med hjälp av Azure Portal.
  
* **`import AutoMLConfig` Miss lyckas**: paket ändringar i den automatiserade Machine Learning-versionen 1.0.76, som kräver att den tidigare versionen avinstallerades innan den nya versionen uppdaterades. Om `ImportError: cannot import name AutoMLConfig` felet inträffar efter att du har uppgraderat från en SDK-version före v-1.0.76 till v 1.0.76 eller senare, löser du felet genom att köra: `pip uninstall azureml-train automl` och sedan `pip install azureml-train-auotml` . Skriptet automl_setup. cmd gör detta automatiskt. 

* **Workspace.from_config Miss lyckas**: om anropen ws = Workspace.from_config () Miss lyckas –
  1. Se till att konfigurationen. ipynb Notebook har körts.
  2. Om antecknings boken körs från en mapp som inte finns under den mapp där `configuration.ipynb` kördes, kopierar du mappen aml_config och filen config.jsden innehåller till den nya mappen. Workspace.from_config läser config.jsför mappen Notebook eller den överordnade mappen.
  3. Om en ny prenumeration, resurs grupp, arbets yta eller region används, se till att du kör `configuration.ipynb` antecknings boken igen. Att ändra config.jsdirekt fungerar bara om arbets ytan redan finns i den angivna resurs gruppen under den angivna prenumerationen.
  4. Om du vill ändra region ändrar du arbets ytan, resurs gruppen eller prenumerationen. `Workspace.create` kommer inte att skapa eller uppdatera en arbets yta om den redan finns, även om den angivna regionen är annorlunda.
  
* **Exempel på antecknings bok Miss lyckas**: om en exempel antecknings bok Miss lyckas med ett fel där egenskapen, metoden eller biblioteket inte finns:
  * Kontrol lera att rätt kernel har marker ATS i Jupyter Notebook. Kerneln visas i det övre högra hörnet på antecknings sidan. Standardvärdet är azure_automl. Kärnan sparas som en del av antecknings boken. Så om du växlar till en ny Conda-miljö måste du välja den nya kerneln i antecknings boken.
      * För Azure Notebooks bör det vara python 3,6. 
      * För lokala Conda-miljöer bör det vara namnet på den Conda-miljö som du angav i automl_setup.
  * Se till att antecknings boken är för den SDK-version som du använder. Du kan kontrol lera SDK-versionen genom att köra `azureml.core.VERSION` i en Jupyter Notebook cell. Du kan hämta tidigare versioner av exempel antecknings böckerna från GitHub genom att klicka på `Branch` knappen, välja `Tags` fliken och sedan välja versionen.

* **`import numpy` Miss lyckas i Windows**: vissa Windows-miljöer ser ett fel vid inläsning av numpy med den senaste python-versionen 3.6.8. Om du ser det här problemet kan du prova med python version 3.6.7.

* **`import numpy` Miss lyckas**: kontrol lera TensorFlow-versionen i den automatiserade ml Conda-miljön. Versioner som stöds är < 1,13. Avinstallera TensorFlow från miljön om versionen är >= 1,13. Du kan kontrol lera versionen av TensorFlow och avinstallera på följande sätt:
  1. Starta ett kommando gränssnitt, aktivera Conda-miljön där automatiserade ml-paket är installerade.
  2. Ange `pip freeze` och leta efter `tensorflow` , om den hittas, ska den version som visas vara < 1,13
  3. Om den listade versionen inte är en version som stöds går du till `pip uninstall tensorflow` kommando gränssnittet och anger y för bekräftelse.
  
 * **Körningen Miss `jwt.exceptions.DecodeError` lyckas med**: exakt fel meddelande: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

    För versioner <= 1.17.0 av SDK kan installationen resultera i en version av PyJWT som inte stöds. Kontrol lera PyJWT-versionen i den automatiserade ml Conda-miljön. Versioner som stöds är < 2.0.0. Du kan kontrol lera versionen av PyJWT på följande sätt:
    1. Starta ett kommando gränssnitt, aktivera Conda-miljön där automatiserade ml-paket är installerade.
    2. Ange `pip freeze` och leta efter `PyJWT` , om den hittas, ska den version som visas < 2.0.0

    Om den listade versionen inte är en version som stöds:
    1. Överväg att uppgradera till den senaste versionen av AutoML SDK: `pip install -U azureml-sdk[automl]` .
    2. Om detta inte är livskraftigt, avinstallera PyJWT från miljön och installera rätt version på följande sätt:
        - `pip uninstall PyJWT` i kommando tolken och ange `y` bekräftelse.
        - Installera med `pip install 'PyJWT<2.0.0'` .

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

+ Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).

+ Lär dig hur du tränar flera modeller med AutoML i [många modeller lösnings acceleratorer](https://aka.ms/many-models).
