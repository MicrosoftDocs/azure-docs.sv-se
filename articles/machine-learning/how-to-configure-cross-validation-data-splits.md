---
title: Data delas och kors validering i automatiserad maskin inlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar data uppsättnings delningar och kors validering för automatiserade maskin inlärnings experiment
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: add84c2cb53a362fc78fc50a6df13b4976e3868d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661043"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurera datadelningar och korsvalidering vid automatiserad maskininlärning

I den här artikeln lär du dig de olika alternativen för att konfigurera tränings data och verifierings data delningar tillsammans med inställningar för kors validering för din automatiserade maskin inlärning, automatiserade ML, experiment.

När du använder automatisk ML för att skapa flera ML-modeller i Azure Machine Learning måste varje underordnad körning verifiera den relaterade modellen genom att beräkna kvalitets måtten för modellen, till exempel precision eller AUC viktad. Dessa mått beräknas genom att jämföra förutsägelserna som gjorts med varje modell med verkliga etiketter från tidigare observationer i verifierings data. [Lär dig mer om hur mått beräknas baserat på validerings typ](#metric-calculation-for-cross-validation-in-machine-learning). 

Automatiserade ML-experiment utför automatisk modell validering. I följande avsnitt beskrivs hur du kan anpassa verifierings inställningarna ytterligare med [Azure Machine Learning python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

En låg kod eller ingen kod får [du i skapa dina automatiserade Machine Learning-experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> Studio stöder för närvarande utbildning och validering av data, samt alternativ för kors validering, men har inte stöd för att ange enskilda datafiler för verifierings uppsättningen. 

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du

* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).

* Bekanta dig med att ställa in ett automatiserat maskin inlärnings experiment med Azure Machine Learning SDK. Följ [själv studie kursen](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att se design mönster för de grundläggande automatiserade tipsen för maskin inlärning.

* En förståelse för att träna/Verifiera data delas och kors validering som metoder för maskin inlärning. En förklaring på hög nivå

    * [Om utbildning, validering och test av data i Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Förstå kors validering i Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Standard data delningar och kors validering i Machine Learning

Använd [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) -objektet för att definiera dina experiment-och utbildnings inställningar. Observera att endast de parametrar som krävs har definierats i följande kodfragment, det vill säga parametrarna för `n_cross_validation` eller `validation_ data` **inte** ingår.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Om du inte uttryckligen anger antingen en `validation_data` -eller `n_cross_validation` -parameter, använder automatisk ml standard tekniker beroende på antalet rader som anges i den enskilda data uppsättningen `training_data` :

|Tränings &nbsp; data &nbsp; storlek| Verifierings teknik |
|---|-----|
|**Större &nbsp; än &nbsp; 20 000 &nbsp; rader**| Data delning för träna/verifiering används. Standardvärdet är att ta 10% av den inledande tränings data uppsättningen som validerings uppsättningen. I sin tur används validerings uppsättningen för mått beräkning.
|**Mindre &nbsp; än &nbsp; 20 000 &nbsp; rader**| Metoden för kors validering används. Standard antalet vikningar beror på antalet rader. <br> **Om data uppsättningen är mindre än 1 000 rader** används 10 vikning. <br> **Om raderna är mellan 1 000 och 20 000** används tre vik.

## <a name="provide-validation-data"></a>Tillhandahåll verifierings data

I det här fallet kan du antingen börja med en enda datafil och dela den i tränings data och verifierings data uppsättningar, eller så kan du ange en separat datafil för validerings uppsättningen. Oavsett hur tilldelar- `validation_data` parametern i `AutoMLConfig` objektet vilka data som ska användas som verifierings uppsättning. Den här parametern accepterar bara data mängder i form av en [Azure Machine Learning dataset](how-to-create-register-datasets.md) -eller Pandas-dataframe.   

> [!NOTE]
> `validation_size`Parametern stöds inte i prognos scenarier.

Följande kod exempel definierar uttryckligen vilken del av de tillhandahållna data som `dataset` ska användas för utbildning och verifiering.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Ange storlek för validerings uppsättning

I det här fallet tillhandahålls endast en enda data uppsättning för experimentet. Det vill säga att `validation_data` parametern **inte** anges och att den angivna data uppsättningen tilldelas till  `training_data` parametern.  

I ditt `AutoMLConfig` objekt kan du ange `validation_size` att parametern ska innehålla en del av tränings data för verifiering. Det innebär att verifierings uppsättningen kommer att delas upp med automatisk ML från den ursprungliga `training_data` . Värdet måste vara mellan 0,0 och 1,0 (till exempel 0,2 betyder att 20% av data hålls kvar för verifierings data).

> [!NOTE]
> `validation_size`Parametern stöds inte i prognos scenarier. 

Se följande kod exempel:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K-vikning kors validering

Om du vill utföra en mellanliggande kors validering inkluderar du `n_cross_validations` parametern och anger den till ett värde. Den här parametern anger hur många kors valideringar som ska utföras baserat på samma antal vikningar.

> [!NOTE]
> `n_cross_validations`Parametern stöds inte i klassificerings scenarier som använder djup neurala nätverk.
 
I följande kod definieras fem vikningar för kors validering. Därför är fem olika utbildningar, varje utbildning som använder 4/5 av data och varje validering som använder 1/5 av data med en annan uttrycks vikning varje tillfälle.

Resultatet blir att måtten beräknas med genomsnittet av de fem verifierings måtten.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Monte Carlo kors validering

Om du vill utföra en kors validering av Monte Carlo inkluderar du både `validation_size` `n_cross_validations` parametrarna och i `AutoMLConfig` objektet. 

För Monte Carlo kors validering tar automatiserade ML ur den del av tränings data som anges av `validation_size` parametern för verifiering och tilldelar sedan resten av data för utbildning. Den här processen upprepas sedan baserat på det värde som anges i `n_cross_validations` parametern, vilket genererar nya inlärnings-och validerings delningar, varje gång.

> [!NOTE]
> Carlo kors validering stöds inte i prognos scenarier.

Följande kod definierar, 7 vikningar för kors validering och 20% av tränings data bör användas för verifiering. Därför använder 7 olika utbildningar 80% av data och varje validering använder 20% av data med en annan uttrycks vikning varje tillfälle.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Ange anpassade kors validerings data vikningar

Du kan också ange dina egna data vikning för kors validering (ka). Detta betraktas som ett mer avancerat scenario eftersom du anger vilka kolumner som ska delas och används för verifiering.  Ta med egna ka-delade kolumner i tränings data och ange vilka kolumner genom att fylla i kolumn namnen i `cv_split_column_names` parametern. Varje kolumn representerar en delning mellan validering och är fylld med heltals värden 1 eller 0 – där 1 anger att raden ska användas för utbildning och 0 anger att raden ska användas för verifiering.

Följande kodfragment innehåller bank marknadsförings data med två CV Split-kolumner, CV1 och CV2.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Om du vill använda `cv_split_column_names` med `training_data` och `label_column_name` uppgraderar du Azure Machine Learning python SDK-version 1.6.0 eller senare. För tidigare SDK-versioner, se använda `cv_splits_indices` , men Observera att den endast används med `X` och `y` data uppsättnings indata. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Mått beräkning för kors validering i Machine Learning

När antingen k-vikning eller Monte Carlo kors validering används, beräknas måtten på varje validerings vikning och sedan aggregeras. Agg regerings åtgärden är ett medelvärde för skalära mått och en summa för diagram. Mått som beräknas under kors valideringen baseras på alla vikningar och därmed alla exempel från inlärnings uppsättningen. [Lär dig mer om mått i automatiserad maskin inlärning](how-to-understand-automated-ml.md).

När du använder en anpassad validerings uppsättning eller en automatiskt vald verifierings uppsättning, beräknas modell utvärderings måtten endast från den validerings uppsättningen, inte tränings data.

## <a name="next-steps"></a>Nästa steg

* [Förhindra obalanserade data och överanpassningar](concept-manage-ml-pitfalls.md).
* [Självstudie: Använd automatiserad Machine Learning för att förutse taxi-biljetten – dela data avsnitt](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Hur man [automatiskt tränar en prognos modell för tids serier](how-to-auto-train-forecast.md).
