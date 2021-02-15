---
title: Automatisk träna en tids serie prognos modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att träna en Regressions Regressions modell i Time Series med hjälp av automatisk maskin inlärning.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 6e686c7b22eb834a096cdd7a67beb6d8d291ef20
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392331"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisk träna en tids serie prognos modell


I den här artikeln får du lära dig hur du konfigurerar och tränar en uppskattnings Regressions modell i Time-serien med hjälp av automatisk maskin inlärning, AutoML, i [Azure Machine Learning python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Det gör du på följande sätt: 

> [!div class="checklist"]
> * Förbereda data för tids serie modellering.
> * Konfigurera vissa parametrar för tids serier i ett [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objekt.
> * Köra förutsägelser med Time Series-data.

En låg kod upplevelse finns i [självstudien: prognostisera efter frågan med automatiserad maskin inlärning](tutorial-automated-ml-forecast.md) för ett exempel på en uppskattning av tids serier med hjälp av automatisk maskin inlärning i [Azure Machine Learning Studio](https://ml.azure.com/).

Till skillnad från klassiska Time Series-metoder i automatiserade ML, är tidigare tids serie värden "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser. Den här metoden omfattar flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. Eftersom flera faktorer kan påverka en prognos justeras den här metoden korrekt med verkliga prognos scenarier. Till exempel, när försäljnings prognoser används, interaktioner av historiska trender, växelkurs och pris gemensamt, kommer försäljnings resultatet gemensamt. 

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du 

* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).

* I den här artikeln förutsätter vi att du ställer in ett automatiserat maskin inlärnings experiment. Följ [själv studie kursen](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att se design mönster för automatiserade maskin inlärnings experiment.

## <a name="preparing-data"></a> Förbereda data

Den viktigaste skillnaden mellan en typ av Regressions Regressions typ och Regressions aktivitets typ i AutoML är bland annat en funktion i dina data som representerar en giltig tids serie. En vanlig tids serie har en väldefinierad och konsekvent frekvens och har ett värde vid varje exempel punkt i ett kontinuerligt tidsintervall. 

Överväg följande ögonblicks bild av en fil `sample.csv` .
Den här data uppsättningen är av dagliga försäljnings data för ett företag som har två olika butiker, A och B. 

Det finns dessutom funktioner för

 *  `week_of_year`: gör att modellen kan identifiera vecko säsongs beroende.
* `day_datetime`: representerar en ren tids serie med daglig frekvens.
* `sales_quantity`: mål kolumnen för att köra förutsägelser. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Läs data till en Pandas-dataframe och Använd sedan `to_datetime` funktionen för att se till att tids serien är av `datetime` typen.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet är data redan sorterade stigande efter fältet Time `day_datetime` . Men när du konfigurerar ett experiment ser du till att kolumnen önskad tid sorteras i stigande ordning för att skapa en giltig tids serie. 

Följande kod, 
* Förutsätter att data innehåller 1 000 poster och gör en deterministisk delning i data för att skapa utbildnings-och test data uppsättningar. 
* Identifierar etikettens kolumn som `sales_quantity` .
* Separerar etikett fältet från `test_data` för att bilda `test_target` uppsättningen.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> När du tränar en modell för att förutsäga framtida värden kan du se till att alla funktioner som används i träningen kan användas när du kör förutsägelser för din avsedda horisont. <br> <br>När du skapar en prognos för efter frågan kan du till exempel öka inlärnings precisionen med en funktion för det aktuella lager priset. Men om du planerar att prognostisera med lång horisont kanske du inte kan förutsäga framtida lager värden som motsvarar framtida tids serie punkter och modell precisionen kan bli lidande.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Utbildning och verifierings data

Du kan ange separata tåg-och validerings uppsättningar direkt i `AutoMLConfig` objektet.   Läs mer om [AutoMLConfig](#configure-experiment).

För tids serie prognoser används endast **rullande ursprung mellan validering (ROCV)** för validering som standard. Skicka inlärnings-och verifierings data tillsammans och ange antalet kors validerings veck med `n_cross_validations` parametern i din `AutoMLConfig` . ROCV delar upp serien i utbildning och validerings data med hjälp av en start tid. När du drar ursprunget i tiden genererar det kors validerings vecket. Den här strategin bevarar tids seriens data integritet och eliminerar risken för data läckage

![kors validering av rullande ursprung](./media/how-to-auto-train-forecast/ROCV.svg)

Du kan också ta med dina egna verifierings data, Läs mer i [Konfigurera data delning och kors validering i AutoML](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Lär dig mer om hur AutoML använder kors validering för att [förhindra överanpassning av modeller](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Konfigurera experiment

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py)Objektet definierar de inställningar och data som krävs för en automatiserad maskin inlärnings uppgift. Konfigurationen för en prognos modell liknar installationen av en standard Regressions modell, men vissa modeller, konfigurations alternativ och funktionalisering-steg finns specifikt för Time-seriens data. 

### <a name="supported-models"></a>Modeller som stöds
Automatisk maskin inlärning försöker automatiskt med olika modeller och algoritmer som en del av processen för att skapa och justera modeller. Som användare behöver du inte ange algoritmen. För prognostisering av experiment är både interna Time-Series-och djup inlärnings modeller en del av rekommendations systemet. I följande tabell sammanfattas den här del mängden av modeller. 

>[!Tip]
> Traditionella Regressions modeller testas också som en del av rekommendations systemet för att förutse experiment. Se [tabellen modell som stöds](how-to-configure-auto-train.md#supported-models) för den fullständiga listan över modeller. 

Modeller| Beskrivning | Fördelar
----|----|---
Prophet (för hands version)|Prophet fungerar bäst med tids serier som har starka säsongs effekter och flera säsonger av historiska data. Om du vill utnyttja den här modellen installerar du den lokalt med `pip install fbprophet` . | Korrekt & snabb, robust för att kunna avvika, saknade data och dramatiska ändringar i din tids serie.
Auto-ARIMA (för hands version)|Autoregressiva Integrated glidande medelvärde (ARIMA) fungerar bäst när data är Station ära. Det innebär att dess statistiska egenskaper, t. ex. medelvärdet och var Ian sen är konstant över hela uppsättningen. Om du till exempel vänder en mynt är sannolikheten för att du får 50%, oavsett om du vänder idag, imorgon eller nästa år.| Perfekt för univariate-serien, eftersom de tidigare värdena används för att förutsäga framtida värden.
ForecastTCN (för hands version)| ForecastTCN är en neurala-nätverks modell som är utformad för att ta itu med de mest krävande prognos uppgifterna, vilket fångar icke-linjära lokala och globala trender i dina data samt relationer mellan tids serier.|Kan använda komplexa trender i dina data och skalas enkelt till största av data uppsättningar.

### <a name="configuration-settings"></a>Konfigurationsinställningar

Precis som med ett Regressions problem definierar du standard utbildnings parametrar som aktivitets typ, antal iterationer, tränings data och antalet kors valideringar. För prognos uppgifter finns det ytterligare parametrar som måste anges som påverkar experimentet. 

I följande tabell sammanfattas dessa ytterligare parametrar. I [ForecastingParameter-klassens referens dokumentation](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) finns mönster för utformning av syntax.

| Parameter &nbsp; namn | Beskrivning | Krävs |
|-------|-------|-------|
|`time_column_name`|Används för att ange kolumnen datetime i de indata som används för att bygga tids serien och härleda dess frekvens.|✓|
|`forecast_horizon`|Definierar hur många perioder som vidarebefordrar dig till prognos. Horisonten är i enheter av tids serie frekvensen. Enheter baseras på tidsintervallet för dina utbildnings data, till exempel varje månad, varje vecka att prognosen ska förutsäga.|✓|
|`enable_dnn`|[Aktivera Prognosticering av hyperoptimerade]().||
|`time_series_id_column_names`|Kolumn namn som används för att unikt identifiera tids serier i data som har flera rader med samma tidsstämpel. Om Time Series-identifierare inte har definierats antas data uppsättningen vara en tids serie. Mer information om engångs-serien finns i [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| Frekvensen för Time Series-datauppsättningen. Den här parametern representerar den period med vilken händelser förväntas inträffa, till exempel varje dag, varje vecka, varje år osv. Frekvensen måste vara ett [Pandas offset-alias](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects).||
|`target_lags`|Antal rader att ange för fördröjning av målvärdena baserat på data frekvensen. Fördröjningen visas som en lista eller ett enda heltal. Fördröjning ska användas när relationen mellan oberoende variabler och beroende variabel inte matchar eller korrelerar som standard. ||
|`feature_lags`| Funktionerna i fördröjningen väljs automatiskt av automatisk ML när `target_lags` har angetts och `feature_lags` är inställt på `auto` . Att aktivera funktionen lags kan hjälpa till att förbättra noggrannheten. Funktionen lags är inaktive rad som standard. ||
|`target_rolling_window_size`|*n* historiska perioder som ska användas för att generera prognostiserade värden <= storlek för tränings uppsättning. Om det utelämnas är *n* den fullständiga inlärnings uppsättningens storlek. Ange den här parametern när du bara vill ta hänsyn till en viss mängd historik när du tränar modellen. Lär dig mer om [agg regerings fönster för mål](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Möjliggör kort tids serie hantering för att undvika misslyckande vid utbildning på grund av otillräckliga data. Hantering av korta serier är inställt på `auto` som standard. Läs mer om [hantering av korta serier](#short-series-handling).|


Följande kod, 
* Utnyttjar klassen för [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) att definiera prognos parametrar för din experiment utbildning
* Anger `time_column_name` `day_datetime` fältet i data uppsättningen. 
* Definierar `time_series_id_column_names` parametern till `"store"` . Detta säkerställer att **två separata tids serie grupper** skapas för data. en för Store A och B.
* Ställer in `forecast_horizon` till 50 för att förutsäga för hela test uppsättningen. 
* Ställer in en prognos period på 10 perioder med `target_rolling_window_size`
* Anger en enda fördröjning för målvärdena för två perioder i förväg med `target_lags` parametern. 
* Anger `target_lags` den rekommenderade inställningen "Auto", som automatiskt identifierar det här värdet.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

De `forecasting_parameters` skickas sedan till standard- `AutoMLConfig` objektet tillsammans med `forecasting` uppgifts typ, primärt mått, avslutnings villkor och tränings data. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

Mängden data som krävs för att kunna träna en prognos modell med automatisk ML påverkas av de `forecast_horizon` , `n_cross_validations` -och eller- `target_lags` `target_rolling_window_size` värden som anges när du konfigurerar `AutoMLConfig` . 

Följande formel beräknar mängden historiska data som behövs för att skapa funktioner för tids serier.

Minsta antal historiska data krävs: (2x `forecast_horizon` ) + # `n_cross_validations` + Max (max ( `target_lags` ), `target_rolling_window_size` )

Ett fel undantag uppstår för alla serier i data uppsättningen som inte uppfyller den nödvändiga mängden historiska data för de relevanta inställningar som anges. 

### <a name="featurization-steps"></a>Funktionalisering-steg

I varje automatiserad maskin inlärnings experiment tillämpas automatiska skalnings-och normaliserings metoder på dina data som standard. Dessa metoder är typer av **funktionalisering** som hjälper *vissa* algoritmer som är känsliga för funktioner i olika skalor. Lär dig mer om standard stegen i funktionalisering i [funktionalisering i AutoML](how-to-configure-auto-features.md#automatic-featurization)

Följande steg utförs dock bara för `forecasting` aktivitets typer:

* Identifiera exempel frekvensen för tids serier (till exempel varje timme, varje dag, varje vecka) och skapa nya poster för frånvaro tids punkter för att göra serien kontinuerlig.
* Ange värden som saknas i målet (via Forward-Fill) och funktions kolumner (med kolumn värden i median)
* Skapa funktioner baserade på Time Series-identifierare för att aktivera fasta effekter i olika serier
* Skapa tidsbaserade funktioner för att hjälpa till med utbildnings säsongs mönster
* Koda kategoriska-variabler till numeriska kvantiteter

För att få en översikt över vilka funktioner som skapas som ett resultat av de här stegen, se [funktionalisering Transparency](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

#### <a name="customize-featurization"></a>Anpassa funktionalisering

Du kan också välja att anpassa dina funktionalisering-inställningar för att säkerställa att de data och funktioner som används för att träna din ML-modell leder till relevanta förutsägelser. 

Anpassningar som stöds för `forecasting` uppgifter är:

|Anpassning|Definition|
|--|--|
|**Uppdatering av kolumn syfte**|Åsidosätt den automatiska identifierade funktions typen för den angivna kolumnen.|
|**Transformering av parameter uppdatering** |Uppdatera parametrarna för den angivna transformeraren. Stöder för närvarande *imputerade* (fill_value och median).|
|**Ta bort kolumner** |Anger kolumner som ska släppas från att bearbetas.|

Om du vill anpassa featurizations med SDK anger du `"featurization": FeaturizationConfig` i- `AutoMLConfig` objektet. Läs mer om [anpassade featurizations](how-to-configure-auto-features.md#customize-featurization).

>[!NOTE]
> Funktionen **släpp kolumner** är inaktuell från och med SDK-version 1,19. Släpp kolumner från data uppsättningen som en del av data rengöringen, innan du använder den i ditt automatiserade ML-experiment. 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Om du använder Azure Machine Learning Studio för experimentet läser du så [här anpassar du funktionalisering i Studio](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

## <a name="optional-configurations"></a>Valfria konfigurationer

Ytterligare valfria konfigurationer är tillgängliga för prognostisering av uppgifter, t. ex. aktivering av djup inlärning och att ange en rullande åtgärds fönster agg regering. 

### <a name="enable-deep-learning"></a>Aktivera djup inlärning

> [!NOTE]
> DNN-stöd för Prognosticering i automatiserade Machine Learning är i för **hands version** och stöds inte för lokala körningar.

Du kan också använda djup inlärning med djup neurala nätverk, Hyperoptimerade, för att förbättra poängen i din modell. Med automatiserad MLs djup inlärning kan du prognostisera univariate-och multivarierad Time Series-data.

Djup inlärnings modeller har tre inbyggda funktioner:
1. De kan lära sig från valfria mappningar från indata till utdata
1. De stöder flera indata och utdata
1. De kan automatiskt extrahera mönster i indata som sträcker sig över långa sekvenser. 

Om du vill aktivera djup inlärning ställer du in `enable_dnn=True` i- `AutoMLConfig` objektet.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> När du aktiverar DNN för experiment som skapats med SDK inaktive ras [bästa modell förklaringar](how-to-machine-learning-interpretability-automl.md) .

Om du vill aktivera DNN för ett AutoML-experiment som skapats i Azure Machine Learning Studio kan du läsa [uppgifts typ inställningarna i Studio instruktion](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Visa [Notebook Production Forecasting-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) för ett detaljerat kod exempel som utnyttjar hyperoptimerade.

### <a name="target-rolling-window-aggregation"></a>Samling av rullande fönster i mål
Ofta är den bästa informationen som en prognosare kan ha är det senaste värdet för målet.  Med mål för rullande fönster kan du lägga till en rullande agg regering av data värden som funktioner. Att skapa och använda dessa ytterligare funktioner som extra sammanhangsbaserade data hjälper till att uppnå en bättre noggrannhet i träna-modellen.

Anta till exempel att du vill förutse energi efter frågan. Du kanske vill lägga till en rullande fönster funktion på tre dagar för att kunna påverka termiska ändringar av upphettade utrymmen. I det här exemplet skapar du det här fönstret genom `target_rolling_window_size= 3` att ange i `AutoMLConfig` konstruktorn. 

Tabellen visar den resulterande funktions teknik som inträffar när Window aggregation används. Kolumner för **minimum, maximum** och **Sum** genereras i ett glidande fönster av tre baserat på de definierade inställningarna. Varje rad har en ny beräknad funktion, när det gäller tidsstämpeln för den 8 september 2017 4:10:00 de högsta, lägsta och sammanlagda värdena beräknas utifrån värdena för **efter frågan** den 8 september 2017 1:10:00-3:10:00. Det här fönstret innehåller tre skiften och fyller i data för de återstående raderna.

![mål för rullande fönster](./media/how-to-auto-train-forecast/target-roll.svg)

Visa en python code-exempel som använder den angivna [mål funktionen för mängd funktions fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="short-series-handling"></a>Hantering av korta serier

Med automatisk ML betraktas en tids serie med en **kort serie** om det inte finns tillräckligt många data punkter för att driva tåg-och validerings faserna för modell utveckling. Antalet data punkter varierar för varje experiment, och beror på max_horizon, antalet delningar av kors validering och längden på modellens lookback, vilket är den maximala historik som krävs för att skapa funktioner i Time-serien. Den exakta beräkningen finns i [referens dokumentationen för short_series_handling_configuration](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

Med automatisk ML får du en kort serie hantering som standard med- `short_series_handling_configuration` parametern i `ForecastingParameters` objektet. 

Om du vill aktivera hantering av korta serier `freq` måste parametern också definieras. För att definiera en Tim frekvens kommer vi att ställas in `freq='H'` . Visa alternativ för frekvens sträng [här](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects). Om du vill ändra standard beteendet `short_series_handling_configuration = 'auto'` uppdaterar du `short_series_handling_configuration` parametern i `ForecastingParameter` objektet.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
I följande tabell sammanfattas de tillgängliga inställningarna för `short_series_handling_config` .
 
|Inställning|Beskrivning
|---|---
|`auto`| Följande är standard beteendet för hantering av korta serier <li> *Om alla serier är korta* kan du fylla i data. <br> <li> *Om inte alla serier är korta* släpper du den korta serien. 
|`pad`| Om du `short_series_handling_config = pad` sedan väljer automatiserad ml läggs slumpmässiga värden till i varje kort serie. Nedan visas kolumn typerna och vad de fylls med: <li>Objekt kolumner med NaNs <li> Numeriska kolumner med 0 <li> Booleska/Logic-kolumner med falskt <li> Mål kolumnen fylls med slumpmässiga värden med medelvärdet noll och standard avvikelsen 1. 
|`drop`| Om du `short_series_handling_config = drop` sedan väljer automatiserad ml tar den korta serien och används inte för utbildning eller förutsägelse. Förutsägelserna för dessa serier kommer att returnera NaN.
|`None`| Ingen serie är utfylld eller utelämnad

>[!WARNING]
>Utfyllnaden kan påverka noggrannheten i den resulterande modellen eftersom vi introducerar artificiell data bara för att få en tidigare utbildning utan problem. <br> <br> Om många av serierna är korta kan du också se vissa konsekvenser i konsekvens resultatet

## <a name="run-the-experiment"></a>Kör experimentet 

När ditt objekt är `AutoMLConfig` klart kan du skicka in experimentet. När modellen har slutförts hämtar du den bästa körnings iterationen.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Prognosticering med bästa modell

Använd den bästa modellen iterationer för att beräkna prognos värden för data uppsättningen test.

`forecast()`Funktionen gör det möjligt att specifikationer när förutsägelser ska starta, till skillnad från `predict()` , som vanligt vis används för klassificerings-och Regressions uppgifter.

I följande exempel ersätter du först alla värden i `y_pred` med `NaN` . Prognosens ursprung är i slutet av tränings data i det här fallet. Men om du bara ersatte den andra halvan av `y_pred` med `NaN` , lämnar funktionen de numeriska värdena i den första halvan oförändrade, men prognoserar `NaN` värdena i den andra halvan. Funktionen returnerar både de beräknade värdena och de justerade funktionerna.

Du kan också använda- `forecast_destination` parametern i `forecast()` funktionen för att prognostisera värden fram till ett angivet datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna ett RMSE (root mean error) mellan de `actual_labels` faktiska värdena och de prognostiserade värdena i `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu när den övergripande modell precisionen har fastställts är det mest realistiska nästa steg att använda modellen för att förutsäga okända framtida värden. 

Ange en data mängd i samma format som test uppsättningen `test_data` , men med framtida datetime-värden och den resulterande förutsägelse uppsättningen är de prognostiserade värdena för varje tids serie steg. Anta att de senaste tid serie posterna i data uppsättningen var i 12/31/2018. Om du vill prognostisera efter frågan för nästa dag (eller så många perioder som du behöver prognostisera, <= `forecast_horizon` ) skapar du en enda tids serie post för varje butik för 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Upprepa de nödvändiga stegen för att läsa in framtida data till en dataframe och kör sedan `best_run.predict(test_data)` för att förutsäga framtida värden.

> [!NOTE]
> In-Sample-förutsägelser stöds inte för prognostisering med automatisk ML när `target_lags` och/eller `target_rolling_window_size` är aktiverade.


## <a name="example-notebooks"></a>Exempelnotebook-filer
Se [exempel antecknings böcker för Prognosticering](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för detaljerade kod exempel på avancerad prognos konfiguration, inklusive:

* [jul avkänning och funktionalisering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [kors validering av rullande ursprung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurerbar lags](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [mängd funktioner för rullande fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).
* Lär dig mer om [tolkning: modell förklaringar i Automatisk maskin inlärning (för hands version)](how-to-machine-learning-interpretability-automl.md). 
* Lär dig hur du tränar flera modeller med AutoML i [många modeller lösnings acceleratorer](https://aka.ms/many-models).
* Följ [själv studie kursen](tutorial-auto-train-models.md) för ett slut punkt till slut punkts exempel för att skapa experiment med automatiserad maskin inlärning.
