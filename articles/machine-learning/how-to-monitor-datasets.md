---
title: Identifiera dataavdrift på datauppsättningar (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du ställer in identifiering av dataavdrift i Azure Learning. Skapa övervakare av datauppsättningar (förhandsversion), övervaka för dataavdrift och konfigurera aviseringar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 95fb2dfeea98b988eaeaea43efc4ea44fd6e33fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770317"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Identifiera dataavdrift (förhandsversion) på datauppsättningar

Lär dig hur du övervakar dataavdrift och ställer in aviseringar när drift är hög.  

Med Azure Machine Learning datauppsättningsövervakare (förhandsversion) kan du:
* **Analysera avdrift i dina data** för att förstå hur de ändras över tid.
* **Övervaka modelldata för** skillnader mellan att träna och betjäna datauppsättningar.  Börja med [att samla in modelldata från distribuerade modeller](how-to-enable-data-collection.md).
* **Övervaka nya data för** skillnader mellan en baslinje och måldatauppsättning.
* **Profilera funktioner i data** för att spåra hur statistiska egenskaper ändras över tid.
* **Konfigurera aviseringar om dataavdrift för** tidiga varningar till potentiella problem. 
* **[Skapa en ny datauppsättningsversion](how-to-version-track-datasets.md)** när du fastställer att data har gått för mycket.

En [Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md) används för att skapa övervakaren. Datamängden måste innehålla en tidsstämpelkolumn.

Du kan visa mått för dataavdrift med Python SDK eller i Azure Machine Learning-studio.  Andra mått och insikter är tillgängliga via resursen [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) som är associerad med Azure Machine Learning arbetsyta.

> [!IMPORTANT]
> Identifiering av dataavdrift för datamängder finns för närvarande i offentlig förhandsversion.
> Förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med datauppsättningsövervakare behöver du:
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen Azure Machine Learning](https://aka.ms/AMLFree) idag.
* En [Azure Machine Learning arbetsyta](how-to-manage-workspace.md).
* Paketet [Azure Machine Learning SDK för Python installerat](/python/api/overview/azure/ml/install), som innehåller paketet azureml-datasets.
* Strukturerade (tabell) data med en tidsstämpel som anges i filsökvägen, filnamnet eller kolumnen i data.

## <a name="what-is-data-drift"></a>Vad är dataavdrift?

Dataavdrift är en av de främsta orsakerna till att modellens precision försämras med tiden.  För maskininlärningsmodeller är dataavdrift den förändring i modellens indata som leder till försämrad modellprestanda.  Övervakning av dataavdrift hjälper till att identifiera dessa modellprestandaproblem.

Orsaker till dataavdrift är:

- Överordnade processändringar, till exempel en sensor som ersätts som ändrar måttenheterna från tum till centimeter. 
- Problem med datakvaliteten, till exempel en bruten sensor som alltid läser 0.
- Naturliga förändringar i data, till exempel medeltemperaturen ändras med säsongerna.
- Ändra i relation mellan funktioner eller kvariera skift. 

Azure Machine Learning förenklar avdriftsidentifiering genom att beräkna ett enda mått som abstraherar komplexiteten hos datauppsättningar som jämförs.  Dessa datauppsättningar kan ha hundratals funktioner och tiotusentals rader. När avdriften har identifierats går du nedåt i vilka funktioner som orsakar avdriften.  Sedan granskar du mått på funktionsnivå för att felsöka och isolera rotorsaken till avdriften.

Metoden uppifrån och ned gör det enkelt att övervaka data i stället för traditionella regelbaserade tekniker. Regelbaserade tekniker som tillåtna dataintervall eller tillåtna unika värden kan vara tidskrävande och felbenägna.

I Azure Machine Learning du datauppsättningsövervakare för att identifiera och varna för dataavdrift.
  
### <a name="dataset-monitors"></a>Datauppsättningsövervakare 

Med en datamängdsövervakare kan du:

* Identifiera och varna för dataavdrift på nya data i en datauppsättning.
* Analysera historiska data för drift.
* Profilera nya data över tid.

Algoritmen för dataavdrift ger ett övergripande mått på dataförändringar och en indikation på vilka funktioner som är ansvariga för ytterligare undersökning. Datamängdsövervakare skapar ett antal andra mått genom att profilera nya data i `timeseries` datauppsättningen. 

Anpassade aviseringar kan konfigureras för alla mått som genereras av övervakaren via [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Datamängdsövervakare kan användas för att snabbt fånga upp dataproblem och minska tiden för att felsöka problemet genom att identifiera sannolika orsaker.  

Konceptuellt finns det tre huvudsakliga scenarier för att konfigurera övervakare av datauppsättningar i Azure Machine Learning.

Scenario | Beskrivning
---|---
Övervaka en modells betjänande data för drift från träningsdata | Resultat från det här scenariot kan tolkas som övervakning av en proxy för modellens noggrannhet, eftersom modellens noggrannhet försämras när de betjänande data körs från träningsdata.
Övervaka en datauppsättning för tidsserier för drift från en tidigare tidsperiod. | Det här scenariot är mer allmänt och kan användas för att övervaka datauppsättningar som ingår uppströms eller nedströms i modellskapandet.  Måldatauppsättningen måste ha en tidsstämpelkolumn. Baslinjedatamängden kan vara en tabelldatamängd som har funktioner som är gemensamma för måldatauppsättningen.
Utföra analyser på tidigare data. | Det här scenariot kan användas för att förstå historiska data och fatta beslut i inställningarna för datauppsättningsövervakare.

Datauppsättningsövervakare är beroende av följande Azure-tjänster.

|Azure-tjänst  |Description  |
|---------|---------|
| *Datamängd* | Drift använder Machine Learning datauppsättningar för att hämta träningsdata och jämföra data för modellträning.  Genereringsprofilen för data används för att generera några av de rapporterade måtten, till exempel min, max, distinkta värden och distinkta värden. |
| *Azureml-pipeline och beräkning* | Beräkningsjobbet för drift finns i azureml-pipelinen.  Jobbet utlöses på begäran eller enligt schema för körning på en beräkning som konfigurerats vid skapandetiden för driftövervakaren.
| *Application Insights*| Drift skickar mått till Application Insights tillhör maskininlärningsarbetsytan.
| *Azure Blob Storage*| Drift skickar mått i json-format till Azure Blob Storage.

### <a name="baseline-and-target-datasets"></a>Baslinje- och måldatauppsättningar 

Du övervakar [Azure Machine Learning-datauppsättningar för](how-to-create-register-datasets.md) dataavdrift. När du skapar en datauppsättningsövervakare refererar du till:
* Baslinjedatauppsättning – vanligtvis träningsdatauppsättningen för en modell.
* Måldatauppsättningen – vanligtvis modellindata – jämförs med din baslinjedatauppsättning över tid. Den här jämförelsen innebär att måldatauppsättningen måste ha en angiven tidsstämpelkolumn.

Övervakaren jämför datauppsättningarna för baslinjen och målet.

## <a name="create-target-dataset"></a>Skapa måldatauppsättning

Måldatauppsättningen behöver den egenskap som angetts för den genom att ange kolumnen för tidsstämpeln, antingen från en kolumn i data eller en virtuell kolumn som härletts från sökvägsmönstret `timeseries` för filerna. Skapa datauppsättningen med en tidsstämpel via [Python SDK](#sdk-dataset) eller [Azure Machine Learning-studio](#studio-dataset). En kolumn som representerar en "tidsstämpel" måste anges för att lägga `timeseries` till egenskaper i datauppsättningen. Om dina data partitioneras i mappstrukturen med tidsinformation, till exempel "{yyyy/MM/dd}", skapar du en virtuell kolumn via sökvägsmönsterinställningen och anger den som "partitionstidsstämpel" för att förbättra vikten av tidsseriefunktioner.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

Klassmetoden [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  definierar tidsstämpelkolumnen för datauppsättningen.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> Ett fullständigt exempel på hur du använder `timeseries` egenskaper för datauppsättningar finns i [exempel-notebook-](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) eller [SDK-dokumentationen för datauppsättningar.](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Om du skapar din datauppsättning med Azure Machine Learning-studio ska du se till att sökvägen till dina data innehåller tidsstämpelinformation, inkludera alla undermappar med data och ange partitionsformatet.

I följande exempel tas alla data under undermappen *NoaaIsdFlorida/2019* och partitionsformatet anger tidsstämpelns år, månad och dag.

[![Partitionsformat](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

I **schemainställningarna** anger du tidsstämpelkolumnen från en virtuell eller verklig kolumn i den angivna datauppsättningen:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Ange tidsstämpeln":::

Om dina data partitioneras efter datum, vilket är fallet här, kan du också ange partition_timestamp.  Detta möjliggör effektivare bearbetning av datum.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Tidsstämpel för partition":::

---

## <a name="create-dataset-monitor"></a>Skapa datauppsättningsövervakare

Skapa en datauppsättningsövervakare för att identifiera och varna för dataavdrift på en ny datauppsättning.  Använd antingen [Python SDK](#sdk-monitor) eller [Azure Machine Learning-studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Fullständig information [finns i referensdokumentationen för Python SDK](/python/api/azureml-datadrift/azureml.datadrift) om dataavdrift. 

I följande exempel visas hur du skapar en datauppsättningsövervakare med hjälp av Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> Ett fullständigt exempel på hur du ställer in en `timeseries` datamängd och dataavdriftsdetektor finns i vår [exempel-notebook.](https://aka.ms/datadrift-notebook)


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Gå till [startsidan för Studio.](https://ml.azure.com)
1. Välj **fliken Datauppsättningar** till vänster. 
1. Välj **Datauppsättningsövervakare.**
   ![Övervaka lista](./media/how-to-monitor-datasets/monitor-list.png)

1. Klicka på knappen **+Skapa övervakare** och fortsätt genom guiden genom att klicka på **Nästa.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Skapa en övervakningsguide":::

* **Välj måldatauppsättning.**  Måldatauppsättningen är en tabelldatauppsättning med en angiven tidsstämpelkolumn som analyseras för dataavdrift. Måldatauppsättningen måste ha funktioner som är gemensamma för baslinjedatauppsättningen och ska vara en `timeseries` datauppsättning som nya data läggs till i. Historiska data i måldatauppsättningen kan analyseras, eller så kan nya data övervakas.

* **Välj baslinjedatauppsättning.**  Välj den tabelldatamängd som ska användas som baslinje för jämförelse av måldatauppsättningen över tid.  Baslinjedatauppsättningen måste ha funktioner som är gemensamma för måldatauppsättningen.  Välj ett tidsintervall för att använda en sektor av måldatauppsättningen eller ange en separat datauppsättning som ska användas som baslinje.

* **Övervaka inställningar**.  De här inställningarna är för pipelinen för den schemalagda datauppsättningsövervakaren, som skapas. 

    | Inställning | Beskrivning | Tips | Föränderlig | 
    | ------- | ----------- | ---- | ------- |
    | Name | Namnet på datauppsättningsövervakaren. | | No |
    | Funktioner | Lista över funktioner som kommer att analyseras för dataavdrift över tid. | Ställ in på en modells utdatafunktion(er) för att mäta konceptavdrift. Ta inte med funktioner som av naturliga fördr bort över tid (månad, år, index osv.). Du kan återfylla och befintlig övervakare av dataavdrift när du har justerat listan över funktioner. | Yes | 
    | Beräkningsmål | Azure Machine Learning beräkningsmål för att köra övervakningsjobben för datauppsättningen. | | Yes | 
    | Aktivera | Aktivera eller inaktivera schemat för pipelinen för datauppsättningsövervakaren | Inaktivera schemat för att analysera historiska data med återfyllningsinställningen. Det kan aktiveras när datauppsättningsövervakaren har skapats. | Yes | 
    | Frekvens | Den frekvens som ska användas för att schemalägga pipelinejobbet och analysera historiska data om du kör en återfyllning. Alternativen omfattar varje dag, varje vecka eller varje månad. | Varje körning jämför data i måldatauppsättningen enligt frekvensen: <li>Dagligen: Jämför den senaste fullständiga dagen i måldatauppsättningen med baslinjen <li>Varje vecka: Jämför den senaste fullständiga veckan (måndag–söndag) i måldatauppsättningen med baslinjen <li>Månadsvis: Jämför den senaste fullständiga månaden i måldatauppsättningen med baslinjen | No | 
    | Svarstid | Tid, i timmar, det tar för data att tas emot i datauppsättningen. Om det till exempel tar tre dagar för data att tas emot i SQL DB som datauppsättningen kapslar in anger du svarstiden till 72. | Det går inte att ändra när datauppsättningsövervakaren har skapats | No | 
    | E-postadresser | E-postadresser för avisering baserat på överträdelser av tröskelvärdet för dataavdrift i procent. | E-postmeddelanden skickas via Azure Monitor. | Yes | 
    | Tröskelvärde | Tröskelvärde för dataavdrift i procent för e-postaviseringar. | Ytterligare aviseringar och händelser kan anges för många andra mått i arbetsytans associerade Application Insights resurs. | Yes |

När guiden har slutförts visas den resulterande datauppsättningsövervakaren i listan. Välj den för att gå till övervakarens informationssida.

---

## <a name="understand-data-drift-results"></a>Förstå dataavdriftsresultat

Det här avsnittet visar resultatet av övervakningen av en datauppsättning som finns på sidan **Datauppsättningar för**  /  **datauppsättningsövervakare** i Azure Studio.  Du kan uppdatera inställningarna samt analysera befintliga data för en viss tidsperiod på den här sidan.  

Börja med insikter på den översta nivån om omfattningen av dataavdrift och en markering av funktioner som ska undersökas närmare.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Driftöversikt":::


| Metric | Beskrivning | 
| ------ | ----------- | 
| Dataavdrifts omfattning | En procentandel av fördr jämfört med baslinje- och måldatamängden över tid. Mellan 0 och 100 indikerar 0 identiska datauppsättningar och 100 anger att Azure Machine Learning dataavdriftsmodellen helt kan hålla isär de två datauppsättningarna. Bruset i den exakta procentandelen förväntas på grund av maskininlärningstekniker som används för att generera den här omfattningen. | 
| De viktigaste avdriftsfunktionerna | Visar de funktioner från datauppsättningen som har avta mest och därför bidrar mest till Måttet Drift Magnitude. På grund av kvarierat skifte behöver den underliggande distributionen av en funktion inte nödvändigtvis ändras för att ha relativt hög egenskaps prioritet. |
| Tröskelvärde | Dataavdriftens omfattning utöver det inställda tröskelvärdet utlöser aviseringar. Detta kan konfigureras i övervakningsinställningarna. | 

### <a name="drift-magnitude-trend"></a>Trend för drift magnitud

Se hur datauppsättningen skiljer sig från måldatauppsättningen under den angivna tidsperioden.  Ju närmare 100 %, desto mer skiljer sig de två datauppsättningarna åt.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend för drift magnitud":::

### <a name="drift-magnitude-by-features"></a>Drift omfattning efter funktioner

Det här avsnittet innehåller insikter på funktionsnivå om ändringen i den valda funktionens distribution, samt annan statistik, över tid.

Måldatauppsättningen profileras också över tid. Det statistiska avståndet mellan baslinjefördelningen för varje funktion jämförs med måldatamängdens över tid.  Konceptuellt liknar detta dataavdriftens omfattning.  Det här statistiska avståndet är dock för en enskild funktion i stället för alla funktioner. Min, max och mean är också tillgängliga.

I Azure Machine Learning-studio klickar du på en stapel i diagrammet för att se information om funktionsnivå för det datumet. Som standard visas baslinjedatauppsättningens distribution och den senaste körningens distribution av samma funktion.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Drift omfattning efter funktioner":::

De här måtten kan också hämtas i Python SDK via `get_metrics()` metoden på ett `DataDriftDetector` -objekt.

### <a name="feature-details"></a>Funktionsinformation

Rulla slutligen ned för att visa information om varje enskild funktion.  Använd listrutan ovanför diagrammet för att välja funktionen och välj dessutom det mått som du vill visa.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numeriskt funktionsdiagram och jämförelse":::

Måtten i diagrammet beror på typen av funktion.

* Numeriska funktioner

    | Metric | Beskrivning |  
    | ------ | ----------- |  
    | Wasserstein-avstånd | Minsta mängd arbete för att omvandla baslinjefördelningen till målfördelningen. |
    | Medelvärde | Genomsnittligt värde för funktionen. |
    | Minvärde | Minsta värde för funktionen. |
    | Maxvärde | Maximalt värde för funktionen. |

* Kategoriska funktioner
    
    | Metric | Beskrivning |  
    | ------ | ----------- |  
    | Euklidiskt avstånd     |  Beräknas för kategoriska kolumner. Euklidiskt avstånd beräknas på två vektorer, genererade från empirisk fördelning av samma kategoriska kolumn från två datauppsättningar. 0 anger att det inte finns någon skillnad i de empiriska fördelningarna.  Ju mer den avviker från 0, desto mer har den här kolumnen avvikit. Trender kan observeras från en tidsserieritning av det här måttet och kan vara till hjälp när du ska upptäcka en avdriftsfunktion.  |
    | Unika värden | Antalet unika värden (kardinalitet) för funktionen. |

I det här diagrammet väljer du ett enda datum för att jämföra funktionsfördelningen mellan målet och det här datumet för den visade funktionen. För numeriska funktioner visar detta två sannolikhetsfördelningar.  Om funktionen är numerisk visas ett stapeldiagram.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Välj ett datum att jämföra med målet":::

## <a name="metrics-alerts-and-events"></a>Mått, aviseringar och händelser

Du kan fråga mått i den resurs Azure Application Insights som [är](../azure-monitor/app/app-insights-overview.md) associerad med din maskininlärningsarbetsyta. Du har åtkomst till alla funktioner i Application Insights inklusive konfigurerade för anpassade aviseringsregler och åtgärdsgrupper för att utlösa en åtgärd, till exempel en e-post/SMS/Push/röst eller Azure-funktion. Mer information finns i den Application Insights dokumentationen. 

Kom igång genom att gå till [Azure Portal](https://portal.azure.com) och välja arbetsytans **översiktssida.**  Den associerade Application Insights resursen finns längst till höger:

[![Översikt över Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Välj Loggar (analys) under Övervakning i den vänstra rutan:

![Översikt över Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Övervakningsmåtten för datauppsättningen lagras som `customMetrics` . Du kan skriva och köra en fråga när du har konfigurerat en datauppsättningsövervakare för att visa dem:

[![Log Analytics-fråga](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

När du har identifierat mått för att konfigurera aviseringsregler skapar du en ny aviseringsregel:

![Ny aviseringsregel](./media/how-to-monitor-datasets/alert-rule.png)

Du kan använda en befintlig åtgärdsgrupp eller skapa en ny för att definiera vilken åtgärd som ska vidtas när de konfigurerade villkoren uppfylls:

![Ny åtgärdsgrupp](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Felsökning

Begränsningar och kända problem för övervakare av dataavdrift:

* Tidsintervallet vid analys av historiska data är begränsat till 31 intervall av övervakarens frekvensinställning. 
* Begränsning på 200 funktioner, såvida inte en funktionslista inte har angetts (alla funktioner som används).
* Beräkningsstorleken måste vara tillräckligt stor för att hantera data.
* Se till att datauppsättningen innehåller data inom start- och slutdatumet för en viss övervakarkörning.
* Datamängdsövervakare fungerar bara på datauppsättningar som innehåller 50 rader eller mer.
* Kolumner, eller funktioner, i datauppsättningen klassificeras som kategoriska eller numeriska baserat på villkoren i följande tabell. Om funktionen inte uppfyller dessa villkor, till exempel en kolumn av typen sträng med >100 unika värden, tas funktionen bort från vår algoritm för dataavdrift, men profileras fortfarande. 

    | Funktionstyp | Datatyp | Villkor | Begränsningar | 
    | ------------ | --------- | --------- | ----------- |
    | Kategoriska | string, bool, int, float | Antalet unika värden i funktionen är mindre än 100 och mindre än 5 % av antalet rader. | Null behandlas som en egen kategori. | 
    | Numeriska | int, float | Värdena i funktionen är av en numerisk datatyp och uppfyller inte villkoret för en kategorisk funktion. | Funktionen ignoreras om >15 % av värdena är null. | 

* När du har skapat en övervakare för  dataavdrift men inte kan se data på sidan Datamängdsövervakare i Azure Machine Learning-studio provar du följande.

    1. Kontrollera om du har valt rätt datumintervall överst på sidan.  
    1. På fliken **Datamängdsövervakare** väljer du experimentlänken för att kontrollera körningsstatusen.  Den här länken finns längst till höger i tabellen.
    1. Om körningen har slutförts kontrollerar du drivrutinsloggarna för att se hur många mått som har genererats eller om det finns några varningsmeddelanden.  Hitta drivrutinsloggar på **fliken Utdata +** loggar när du har klickat på ett experiment.

* Om `backfill()` SDK-funktionen inte genererar förväntade utdata kan det bero på ett autentiseringsproblem.  Använd inte när du skapar beräkningen som ska överföras till den här `Run.get_context().experiment.workspace.compute_targets` funktionen.  Använd i stället [ServicePrincipalAuthentication,](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) till exempel följande, för att skapa den beräkning som du skickar till den `backfill()` funktionen: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Från modelldatainsamlaren kan det ta upp till (men vanligtvis mindre än) 10 minuter innan data kommer till ditt bloblagringskonto. Vänta i 10 minuter i ett skript eller en notebook-dator för att se till att cellerna nedan körs.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Nästa steg

* Gå till Azure Machine Learning-studio [Python-anteckningsboken](https://ml.azure.com) [för](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) att konfigurera en datauppsättningsövervakare.
* Se hur du ställer in dataavdrift på [modeller som distribueras till Azure Kubernetes Service](./how-to-enable-data-collection.md).
* Konfigurera övervakare av datamängdsavdrift med [event grid](how-to-use-event-grid.md).
