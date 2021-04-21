---
title: Skapa automatiska ML-experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar datakällor, beräkningar och konfigurationsinställningar för dina automatiserade maskininlärningsexperiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 146697dec7e3d22e745fba2a1e9fae5d486195ef
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819329"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i Python


I den här guiden lär du dig att definiera olika konfigurationsinställningar för dina automatiserade maskininlärningsexperiment [med Azure Machine Learning SDK.](/python/api/overview/azure/ml/intro) Automatiserad maskininlärning väljer en algoritm och hyperparametrar åt dig och genererar en modell som är redo för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.

Exempel på automatiserade maskininlärningsexperiment finns i [Självstudie:](tutorial-auto-train-models.md) Träna en klassificeringsmodell med automatiserad maskininlärning eller Träna modeller med [automatiserad maskininlärning i molnet.](how-to-auto-train-remote.md)

Konfigurationsalternativ som är tillgängliga i automatiserad maskininlärning:

* Välj experimenttyp: Klassificering, regression eller prognostiserade tidsserier
* Datakälla, format och hämta data
* Välj ditt beräkningsmål: lokalt eller fjärranslutet
* Inställningar för automatiserade maskininlärningsexperiment
* Köra ett automatiserat maskininlärningsexperiment
* Utforska modellmått
* Registrera och distribuera modellen

Om du föredrar en upplevelse utan kod kan du även skapa dina automatiserade [maskininlärningsexperiment i Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du 
* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbetsytan finns [i Skapa en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

* Den Azure Machine Learning Python SDK installerad.
    Om du vill installera SDK:n kan du antingen, 
    * Skapa en beräkningsinstans som automatiskt installerar SDK och är förkonfigurerad för ML-arbetsflöden. Mer [information finns i Skapa Azure Machine Learning hantera en beräkningsinstans.](how-to-create-manage-compute-instance.md) 

    * [Installera paketet `automl` själv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), som innehåller [standardinstallationen](/python/api/overview/azure/ml/install#default-install) av SDK: n.
    
    > [!WARNING]
    > Python 3.8 är inte kompatibelt med `automl` . 

## <a name="select-your-experiment-type"></a>Välj experimenttyp

Innan du påbörjar experimentet bör du fastställa vilken typ av maskininlärningsproblem du löser. Automatiserad maskininlärning stöder uppgiftstyperna `classification` `regression` , och `forecasting` . Läs mer om [uppgiftstyper.](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)

Följande kod använder `task` parametern i `AutoMLConfig` konstruktorn för att ange experimenttypen som `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datakällor och format

Automatiserad maskininlärning har stöd för både lokala data och data i molnlagring som Azure Blob Storage. Data kan läsas till en **Pandas DataFrame eller** en **Azure Machine Learning TabularDataset**. [Läs mer om datamängder](how-to-create-register-datasets.md).

Krav för träningsdata i maskininlärning:
- Data måste vara i tabellform.
- Värdet som ska förutsägas, målkolumnen, måste finnas i data.

**För fjärrexperiment** måste träningsdata vara tillgängliga från fjärrbearbetningen. AutoML tar bara emot [TabularDatasets i Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) när du arbetar från en fjärransluten dator. 

Azure Machine Learning-datamängder exponerar funktioner för att:

* Överför enkelt data från statiska filer eller URL-källor till din arbetsyta.
* Göra data tillgängliga för träningsskript när de körs på beräkningsresurser i molnet. Se [Träna med datauppsättningar för ett](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) exempel på hur du använder klassen för att montera data till `Dataset` fjärrbearbetningsmålet.

Följande kod skapar en TabularDataset från en webbadress. Kodexempel på hur du skapar datauppsättningar från andra källor som lokala filer och datalager finns i Skapa ett [TabularDatasets.](how-to-create-register-datasets.md#create-a-tabulardataset)

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**För lokala beräkningsexperiment** rekommenderar vi Pandas-dataramar för snabbare bearbetningstider.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Tränings-, validerings- och testdata

Du kan ange separata **träningsdata och valideringsdatauppsättningar** direkt i `AutoMLConfig` konstruktorn. Läs mer om [hur du konfigurerar datasplitter och korsvalidering](how-to-configure-cross-validation-data-splits.md) för dina AutoML-experiment. 

Om du inte uttryckligen anger en `validation_data` - eller `n_cross_validation` -parameter tillämpar automatiserad ML standardtekniker för att avgöra hur valideringen utförs. Den här bestämningen beror på antalet rader i datauppsättningen som tilldelats din `training_data` parameter. 

|Storlek &nbsp; på &nbsp; träningsdata| Valideringsteknik |
|---|-----|
|**Större &nbsp; än &nbsp; 20 000 &nbsp; rader**| Uppdelning av träna/valideringsdata tillämpas. Standardvärdet är att ta 10 % av den inledande träningsdatauppsättningen som valideringsuppsättning. Valideringsuppsättningen används i sin tur för måttberäkning.
|**Mindre &nbsp; än &nbsp; 20 000 &nbsp; rader**| Korsvalideringsmetod tillämpas. Standardantalet gånger beror på antalet rader. <br> **Om datauppsättningen är mindre än 1 000 rader** används 10 gånger. <br> **Om raderna är mellan 1 000 och 20 000** används tre gånger.

Just nu måste du ange dina egna **testdata för modellutvärdering.** Ett kodexempel på hur du hämtar dina egna testdata för modellutvärdering finns i **avsnittet Test i** den här [Jupyter Notebook.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Fastställ sedan var modellen ska tränas. Ett träningsexperiment i automatiserad maskininlärning kan köras med följande beräkningsalternativ. Läs mer om [fördelar och nackdelar med lokala och fjärranslutna beräkningar](concept-automated-ml.md#local-remote). 

* Din **lokala** dator, till exempel en lokal stationär dator eller bärbar dator – Vanligtvis när du har en liten datauppsättning och du fortfarande är i utforskningsfasen. Det finns ett exempel med lokala beräkningar i [den här notebook-filen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* En **fjärrdator** i molnet [– Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör det möjligt att träna maskininlärningsmodeller i kluster med virtuella Azure-datorer. 

    Det finns ett exempel med fjärrberäkningar med Azure Machine Learning Managed Compute i [den här notebook-filen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* Ett **Azure Databricks i** din Azure-prenumeration. Mer information finns i Konfigurera [ett kluster Azure Databricks för automatiserad ML.](how-to-configure-databricks-automl-environment.md) Se den [här GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på notebook-Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera experimentinställningarna

Det finns flera alternativ som du kan använda för att konfigurera ditt automatiserade maskininlärningsexperiment. Dessa parametrar anges genom att ett objekt `AutoMLConfig` instansieras. En fullständig [lista över parametrar finns i AutoMLConfig-klassen.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Några exempel är:

1. Klassificeringsexperiment med AUC viktat som primärt mått med tidsgränsminuter för experiment inställt på 30 minuter och 2 korsvalideringsveckor.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Följande exempel är ett regressionsexperiment som är inställt på att avslutas efter 60 minuter med fem valideringskryssveckor.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Prognostiserade uppgifter kräver extra konfiguration. Mer information finns i artikeln [Autotrain a time-series forecast model (Träna](how-to-auto-train-forecast.md) en prognosmodell för tidsserier automatiskt). 

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

Automatiserad maskininlärning provar olika modeller och algoritmer under automatiserings- och justeringsprocessen. Som användare behöver du inte ange algoritmen. 

De tre olika `task` parametervärdena avgör vilken lista med algoritmer, eller modeller, som ska tillämpas. Använd `allowed_models` parametrarna `blocked_models` eller för att ytterligare ändra iterationer med de tillgängliga modeller som ska inkluderas eller exkluderas. 

I följande tabell sammanfattas de modeller som stöds efter uppgiftstyp. 

> [!NOTE]
> Om du planerar att exportera dina automatiskt ML-skapade modeller till en [ONNX-modell](concept-onnx.md)kan endast de algoritmer som anges med * konverteras till ONNX-formatet. Läs mer om [att konvertera modeller till ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observera också att ONNX endast stöder klassificerings- och regressionsuppgifter för närvarande. 

Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
[Logistisk regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lätt GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Lätt GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Lätt GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boost (toning)](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient Boost (toning)](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boost (toning)](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[ERIK Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Stödvektorklassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD (Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD (Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Genomsnittlig perceptron-klassificerare](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Profeten](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD (Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linjär SVM-klassificerare](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Primärt mått
Parametern `primary metric` avgör vilket mått som ska användas under modellträningen för optimering. Vilka tillgängliga mått du kan välja beror på vilken aktivitetstyp du väljer, och i följande tabell visas giltiga primära mått för varje uppgiftstyp.

Att välja ett primärt mått för automatiserad maskininlärning för att optimera beror på många faktorer. Vi rekommenderar att du främst väljer ett mått som bäst representerar dina affärsbehov. Överväg sedan om måttet är lämpligt för din datauppsättningsprofil (datastorlek, intervall, klassdistribution osv.).

Lär dig mer om de specifika definitionerna av de här måtten i [Förstå automatiserade maskininlärningsresultat.](how-to-understand-automated-ml.md)

|Klassificering | Regression | Prognostisering för tidsserier
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Primära mått för klassificeringsscenarier 

Efter tröskelvärden som , , och kanske inte heller optimeras för små datauppsättningar, har mycket stor klassskevning (obalans i klassen) eller när det förväntade måttvärdet är mycket nära `accuracy` `average_precision_score_weighted` `norm_macro_recall` `precision_score_weighted` 0,0 eller 1,0. I dessa fall `AUC_weighted` kan vara ett bättre val för det primära måttet. När automatiserad maskininlärning är klar kan du välja den 5:e modellen baserat på det mått som passar bäst för dina affärsbehov.

| Metric | Exempel på användningsfall |
| ------ | ------- |
| `accuracy` | Bildklassificering, attitydanalys, omsättningsförutsägelse |
| `AUC_weighted` | Bedrägeriidentifiering, bildklassificering, avvikelseidentifiering/skräppostidentifiering |
| `average_precision_score_weighted` | Attitydanalys |
| `norm_macro_recall` | Omsättningsförutsägelse |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Primära mått för regressionsscenarier

Mått som och kan bättre representera modellens kvalitet när skalan `r2_score` för det värde som ska `spearman_correlation` förutsägas omfattar många storleksordrar. Till exempel en uppskattning av lönen, där många har en lön på 20 000 till 100 000 USD, men skalan är mycket hög med vissa löner i intervallet 100 miljoner DOLLAR. 

`normalized_mean_absolute_error` och skulle i det här fallet behandla ett förutsägelsefel på 20 000 USD på samma sätt för en arbetare med en lön på 30 000 USD som en arbetare som gör `normalized_root_mean_squared_error` 20 miljoner USD. I verkligheten är det mycket nära att förutsäga endast 20 000 USD från en lön på 20 miljoner USD (en liten relativ skillnad på 0,1 %), medan 20 000 USD från 30 000 är inte nära (en stor relativ skillnad på 67 %). `normalized_mean_absolute_error` och `normalized_root_mean_squared_error` är användbara när värdena som ska förutsägas är i en liknande skala.

| Metric | Exempel på användningsfall |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prisförutsägelse (hus/produkt/tips), förutsägelse av omdömespoäng |
| `r2_score` | Flygfördröjning, löneuppskattning, lösningstid för bugg |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Primära mått för scenarier med tidsserieprognoser

Se regressionsanteckningar ovan.

| Metric | Exempel på användningsfall |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prisförutsägelse (prognostisering), lageroptimering, prognostisering av efterfrågan |
| `r2_score` | Prisförutsägelse (prognostisering), lageroptimering, prognostisering av efterfrågan |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Datadealisering

I varje automatiserat maskininlärningsexperiment skalas  och normaliseras dina data automatiskt för att hjälpa vissa algoritmer som är känsliga för funktioner som är i olika skalor. Den här skalningen och normaliseringen kallas för featurisering. Se [Funktionen i AutoML för mer](how-to-configure-auto-features.md#) information och kodexempel. 

När du konfigurerar experiment i `AutoMLConfig` ditt -objekt kan du aktivera/inaktivera inställningen `featurization` . I följande tabell visas godkända inställningar för funktioner i [AutoMLConfig-objektet](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfiguration av featurisering | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Anger att som en del av förbearbetningen utförs [dataskyddsräcken och featuriseringssteg](how-to-configure-auto-features.md#featurization) automatiskt. **Standardinställning**.|
|`"featurization": 'off'`| Anger att ett steg för featurisering inte ska utföras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att anpassade featuriseringssteg ska användas. [Lär dig hur du anpassar featurization](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatiserade funktionaliseringssteg för maskininlärning (funktions normalisering, hantering av saknade data, konvertering av text till numeriska osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma featuriseringssteg som tillämpades under träningen på dina indata automatiskt.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Ensemblekonfiguration

Ensemblemodeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en AutoML-körning. För **närvarande stöds VotingEnsemble** **och StackEnsemble.** 

Röstning implementerar mjuk röstning, som använder viktade medelvärden. Stackimplementeringsimplementering använder en implementering med två lager, där det första skiktet har samma modeller som röstnings-ensemblen, och den andra skiktmodellen används för att hitta den optimala kombinationen av modellerna från det första skiktet. 

Om du använder ONNX-modeller **eller** har aktiverat modellförklaring inaktiveras stapling och endast röstning används.

Ensembleträning kan inaktiveras med hjälp av de `enable_voting_ensemble` `enable_stack_ensemble` booleska parametrarna och .

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

Om du vill ändra standardbeteendet för ensemblen finns det flera standardargument som kan anges `kwargs` som i ett `AutoMLConfig` -objekt.

> [!IMPORTANT]
>  Följande parametrar är inte explicita parametrar för klassen AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Under **modellgenereringen VotingEnsemble** **och StackEnsemble** hämtas flera anpassade modeller från de tidigare underordnade körningarna. Om du stöter på det `AutoMLEnsembleException: Could not find any models for running ensembling` här felet: kan du behöva ange mer tid för de modeller som ska laddas ned. Standardvärdet är 300 sekunder för nedladdning av dessa modeller parallellt och det finns ingen maximal tidsgräns. Konfigurera den här parametern med ett högre värde än 300 sek om mer tid behövs. 

  > [!NOTE]
  >  Om tidsgränsen uppnås och det finns nedladdade modeller fortsätter tempusen med så många modeller som den har laddat ned. Det krävs inte att alla modeller laddas ned för att slutföras inom den tidsgränsen.

Följande parametrar gäller endast för **StackEnsemble-modeller:** 

* `stack_meta_learner_type`: meta learner är en modell som tränats på utdata från de enskilda heterogena modellerna. Standardmetainatorer är till för klassificeringsuppgifter (eller om korsvalidering är aktiverat) och `LogisticRegression` `LogisticRegressionCV` för `ElasticNet` regressions-/prognostiserade uppgifter (eller om `ElasticNetCV` korsvalidering är aktiverat). Den här parametern kan vara någon av följande strängar: `LogisticRegression` , , , , , , eller `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` `LinearRegression` .

* `stack_meta_learner_train_percentage`: anger andelen av träningsuppsättningen (när du väljer tränings- och valideringstyp för träning) som ska reserveras för träning av meta learner. Standardvärdet är `0.2`. 

* `stack_meta_learner_kwargs`: valfria parametrar som ska överföras till meta learner-initieraren. Dessa parametrar och parametertyper speglar parametrarna och parametertyperna från motsvarande modellkonstruktor och vidarebefordras till modellkonstruktorn.

Följande kod visar ett exempel på hur du anger anpassat ensemblebeteende i ett `AutoMLConfig` objekt.

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

### <a name="exit-criteria"></a>Avslutningsvillkor

Det finns några alternativ som du kan definiera i AutoMLConfig för att avsluta experimentet.

|Kriterie| beskrivning
|----|----
Inga &nbsp; kriterier | Om du inte definierar några slutparametrar fortsätter experimentet tills inga ytterligare framsteg har gjorts för ditt primära mått.
Efter &nbsp; &nbsp; en längre &nbsp; &nbsp; tid| Använd `experiment_timeout_minutes` i inställningarna för att definiera hur länge experimentet ska fortsätta köras i minuter. <br><br> För att undvika time out-fel i experimentet finns det minst 15 minuter eller 60 minuter om raden efter kolumnstorlek överskrider 10 miljoner.
En &nbsp; poäng &nbsp; har &nbsp; &nbsp; uppnåtts| Använd `experiment_exit_score` slutför experimentet när en angiven primär måttpoäng har uppnåtts.

## <a name="run-experiment"></a>Köra experiment

För automatiserad ML skapar du ett `Experiment` -objekt, som är ett namngivet objekt i en `Workspace` som används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experimentet för att köra och generera en modell. Skicka till `AutoMLConfig` metoden för att generera `submit` modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Om `show_output` du ställer in på resulterar det i `True` utdata som visas på konsolen.

### <a name="multiple-child-runs-on-clusters"></a>Flera underordnade körningar i kluster

Automatiserade underordnade körningar av ML-experiment kan utföras på ett kluster som redan kör ett annat experiment. Tidsinställningen beror dock på hur många noder klustret har och om dessa noder är tillgängliga för att köra ett annat experiment.

Varje nod i klustret fungerar som en enskild virtuell dator (VM) som kan utföra en enda träningskörning. för automatiserad ML innebär detta en underordnad körning. Om alla noder är upptagna köas det nya experimentet. Men om det finns lediga noder kör det nya experimentet automatiserade ML-underordnade körningar parallellt i tillgängliga noder/virtuella datorer.

För att hantera underordnade körningar och när de kan utföras rekommenderar vi att du skapar ett dedikerat kluster per experiment och matchar antalet experiment med antalet noder i `max_concurrent_iterations` klustret. På så sätt kan du använda alla noder i klustret samtidigt med antalet samtidiga underordnade körningar/iterationer som du vill ha.

Konfigurera  `max_concurrent_iterations` i `AutoMLConfig` ditt -objekt. Om den inte är konfigurerad tillåts som standard bara en samtidig underordnad körning/iteration per experiment.  

## <a name="explore-models-and-metrics"></a>Utforska modeller och mått

Automatiserad ML erbjuder alternativ för att övervaka och utvärdera dina träningsresultat. 

* Du kan visa dina träningsresultat i en widget eller infogade om du är i en notebook-dator. Mer [information finns i Övervaka automatiserade maskininlärningskörningar.](#monitor)

* Definitioner och exempel på prestandadiagram och mått som tillhandahålls för varje körning finns i Utvärdera resultat från [automatiserade maskininlärningsexperiment.](how-to-understand-automated-ml.md) 

* Om du vill få en sammanfattning av funktioner och förstå vilka funktioner som har lagts till i en viss modell kan du gå till [Funktioners transparens.](how-to-configure-auto-features.md#featurization-transparency) 

Du kan visa hyperparametrarna, skalnings- och normaliseringsteknikerna och algoritmen som tillämpas på en specifik automatiserad ML-körning med följande anpassade kodlösning. 

Följande definierar den anpassade metoden, `print_model()` , som skriver ut hyperparametrarna för varje steg i den automatiserade ML-träningspipelinen.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

För en lokal körning eller fjärrkörning som precis skickades och tränades inifrån samma notebook-experiment kan du skicka in den bästa modellen med hjälp av `get_output()` metoden . 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

Följande utdata visar att:
 
* Tekniken StandardScalerWrapper användes för att skala och normalisera data före träningen.

* XGBoostClassifier-algoritmen identifierades som den bästa körningen och visar även hyperparametervärdena. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

För en befintlig körning från ett annat experiment på arbetsytan hämtar du det specifika körnings-ID som du vill utforska och skickar det till `print_model()` metoden . 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Algoritmerna som automatiserad ML använder har inbyggd slumpmässighet som kan orsaka viss variation i en rekommenderad modells slutliga måttpoäng, till exempel noggrannhet. Automatiserad ML utför också åtgärder på data, till exempel uppdelning av träna/testa, uppdelning av validering eller korsvalidering vid behov. Så om du kör ett experiment med samma konfigurationsinställningar och primära mått flera gånger ser du förmodligen variationen i varje experiments slutliga måttpoäng på grund av dessa faktorer. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Övervaka automatiserade maskininlärningskörningar

För automatiserade maskininlärningskörningar ersätter du med lämpligt experimentnamn för att komma `<<experiment_name>>` åt diagrammen från en tidigare körning:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-widget för Automated Machine Learning](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Registrera och distribuera modeller

Du kan registrera en modell så att du kan komma tillbaka till den för senare användning. 

Om du vill registrera en modell från en automatiserad ML-körning använder du [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) metoden . 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Mer information om hur du skapar en distributionskonfiguration och distribuerar en registrerad modell till en webbtjänst finns i hur och var [du distribuerar en modell.](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)

> [!TIP]
> För registrerade modeller är distribution med ett klick tillgängligt via [Azure Machine Learning-studio](https://ml.azure.com). Se [hur du distribuerar registrerade modeller från studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modelltolkning kan du förstå varför dina modeller har gjort förutsägelser och de underliggande värdena för egenskapsbetydelse. SDK:n innehåller olika paket för att aktivera funktioner för modelltolkning, både vid träning och slutsatsdragning, för lokala och distribuerade modeller.

Se [kodexementen](how-to-machine-learning-interpretability-automl.md) om hur du aktiverar tolkningsfunktioner specifikt i automatiserade maskininlärningsexperiment.

Allmän information om hur modellförklaringar och funktionsbetydelse kan aktiveras i andra [](how-to-machine-learning-interpretability.md) områden av SDK:n utanför automatiserad maskininlärning finns i begreppsartikeln om tolkning.

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av förklaringsklienten. Den här modellen returnerar ingen förklaringsinstrumentpanel om den returneras som den bästa modellen och inte stöder körningar av förklaringar på begäran.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [hur och var du distribuerar en modell.](how-to-deploy-and-where.md)

+ Lär dig mer [om hur du tränar en regressionsmodell med automatiserad](tutorial-auto-train-models.md) maskininlärning eller hur du tränar med [automatiserad maskininlärning på en fjärransluten resurs.](how-to-auto-train-remote.md)

+ Lär dig hur du tränar flera modeller med AutoML i [lösningsacceleratorn många modeller.](https://aka.ms/many-models)

+ [Felsöka automatiserade ML-experiment.](how-to-troubleshoot-auto-ml.md) 
