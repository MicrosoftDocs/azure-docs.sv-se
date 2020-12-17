---
title: Identifiera data avvikelser på data uppsättningar (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar data avkänning i Azure Learning. Skapa data uppsättnings skärmar (för hands version), övervaka för data drift och konfigurera aviseringar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: 1622f8ce988c5592ac96cec798617ca6ac37aa8d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617178"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Identifiera data avvikelser (för hands version) på data uppsättningar


> [!IMPORTANT]
> Data identifiering för data uppsättningar är för närvarande en offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Lär dig hur du övervakar data drift och ställer in aviseringar när driften är hög.  

Med Azure Machine Learning data uppsättnings övervakare (för hands version) kan du:
* **Analysera driften i dina data** för att förstå hur den ändras med tiden.
* **Övervaka modell data** för skillnader mellan utbildning och betjäning av data uppsättningar.  Börja med att [samla in modell data från distribuerade modeller](how-to-enable-data-collection.md).
* **Övervaka nya data** för skillnader mellan alla data uppsättningar för bas linjen och målet.
* **Profil funktioner i data** för att spåra hur statistik egenskaper ändras med tiden.
* **Konfigurera aviseringar om data drift** för tidiga varningar till potentiella problem. 

En [Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md) används för att skapa övervakaren. Data mängden måste innehålla en tidsstämpelkolumn.

Du kan visa data drifts mått med python SDK eller i Azure Machine Learning Studio.  Andra mått och insikter är tillgängliga via den [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) -resurs som är kopplad till arbets ytan Azure Machine Learning.

## <a name="prerequisites"></a>Krav

Om du vill skapa och arbeta med data uppsättnings övervakare behöver du:
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Azure Machine Learning SDK för python installerat](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), som innehåller paketet azureml-DataSets.
* Strukturerad (tabell) data med en tidsstämpel som anges i fil Sök vägen, fil namnet eller kolumnen i data.

## <a name="what-is-data-drift"></a>Vad är data avvikelser?

Data drivgarn är en av de främsta orsakerna till modell precisionen över tid.  För maskin inlärnings modeller är data avvikelsen den förändring i modellens indata som leder till försämrade prestanda för modeller.  Övervakning av data driften hjälper till att identifiera de här modell prestanda problemen.

Orsaker till data avvikelse är:

- Ändringar av överordnade processer, till exempel en sensor som byts ut, ändrar mått enheterna från tum till centimeter. 
- Data kvalitets problem, till exempel en bruten sensor, läser alltid 0.
- Naturlig drift av data, t. ex. medel temperatur förändringar med säsongerna.
- Ändring i relationen mellan funktioner eller covariate Shift. 

Azure Machine Learning fören klar drifts identifieringen genom att ett enda mått som sammanfattar komplexiteten för data uppsättningar jämförs.  Dessa data uppsättningar kan innehålla hundratals funktioner och flera tusen rader. När driften har identifierats kan du öka detalj nivån i vilka funktioner som orsakar driften.  Du inspekterar sedan funktions nivå måtten för att felsöka och isolera rotor saken för driften.

Den här metoden gör det enkelt att övervaka data i stället för traditionella regelbaserade tekniker. Reglerbaserade tekniker som tillåtna data intervall eller tillåtna unika värden kan vara tids krävande och fel känsliga.

I Azure Machine Learning använder du data uppsättnings Övervakare för att identifiera och varna för data drift.
  
### <a name="dataset-monitors"></a>Övervakare för data uppsättningar 

Med en data uppsättnings övervakare kan du:

* Identifiera och Avisera data vid nya data i en data uppsättning.
* Analysera historiska data för drift.
* Profilera nya data över tid.

Datanings algoritmen ger ett övergripande mått på förändringar i data och indikering av vilka funktioner som är ansvariga för ytterligare undersökning. Data uppsättnings övervakare producerar ett antal andra mått genom profilering av nya data i `timeseries` data uppsättningen. 

Anpassade aviseringar kan konfigureras för alla mått som genereras av övervakaren genom [Azure Application insikter](../azure-monitor/app/app-insights-overview.md). Data uppsättnings övervakare kan användas för att snabbt fånga in data problem och minska tiden för att felsöka problemet genom att identifiera sannolika orsaker.  

Det finns tre huvudsakliga scenarier för att konfigurera data uppsättnings övervakare i Azure Machine Learning.

Scenario | Beskrivning
---|---
Övervaka en modells betjänande data för att gå från tränings data | Resultat från det här scenariot kan tolkas som övervakning av en proxy för modellens riktighet, eftersom modell precisionen försämras när de betjänar data från tränings data.
Övervaka en Time Series-datauppsättning för avvikelse från en tidigare tids period. | Det här scenariot är allmänt och kan användas för att övervaka data uppsättningar som är överordnade eller underordnade modell skapande.  Mål data uppsättningen måste ha en tidsstämpelkolumn. Bas linje data uppsättningen kan vara valfri tabell data uppsättning som har funktioner som är gemensamma för mål data uppsättningen.
Utför analys av tidigare data. | Det här scenariot kan användas för att förstå historiska data och meddela beslut i inställningar för data uppsättnings övervakare.

Data uppsättnings övervakare är beroende av följande Azure-tjänster.

|Azure-tjänst  |Beskrivning  |
|---------|---------|
| *Datamängd* | Använd Machine Learning data uppsättningar för att hämta tränings data och jämföra data för modell träning.  Generering av data profiler används för att generera några av de rapporterade måtten, t. ex. minsta, högsta, distinkta värden, antal distinkta värden. |
| *Azureml pipeline och Compute* | Jobbet för avvikelse beräkning finns i azureml-pipeline.  Jobbet utlöses på begäran eller enligt schema som ska köras vid en beräkning som kon figurer ATS vid skapande av körnings tid.
| *Application Insights*| Avvikelser genererar mått till Application Insights som hör till Machine Learning-arbetsytan.
| *Azure Blob Storage*| Avvikelser genererar mått i JSON-format till Azure Blob Storage.

## <a name="how-dataset-monitors-data"></a>Hur data uppsättningen övervakar data

Använd Machine Learning data uppsättningar för att övervaka data avvikelser. Ange en bas linje data uppsättning – vanligt vis inlärnings data uppsättningen för en modell. En mål data uppsättning – vanligt vis modell indata, jämförs med tiden för din bas linje data uppsättning. Den här jämförelsen innebär att mål data uppsättningen måste ha en angiven tidsstämpelkolumn.

## <a name="create-target-dataset"></a>Skapa mål data uppsättning

Mål data uppsättningen måste ha `timeseries` angiven trait genom att ange kolumnen tidsstämpelkolumn från en kolumn i data eller en virtuell kolumn härledd från Sök vägs mönstret för filerna. Skapa data uppsättningen med en tidsstämpel genom [python SDK](#sdk-dataset) eller [Azure Machine Learning Studio](#studio-dataset). En kolumn som representerar en "tidstämpel" måste anges för att lägga till `timeseries` trait i data uppsättningen. Om dina data är partitionerade i mappstrukturen med tidsinformation, till exempel {ÅÅÅÅ/MM/DD}, skapar du en virtuell kolumn med hjälp av banans mönster inställning och anger den som "partitionens tidstämpel" för att förbättra vikten av tids serie funktionen.

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

[`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)Klass [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metoden definierar tids stämplings kolumnen för data uppsättningen.

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

Ett komplett exempel på hur du `timeseries` kan använda data uppsättnings egenskaper finns i [exempel på Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) eller [data uppsättningarna SDK-dokumentationen](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure Machine Learning Studio

Om du skapar din data uppsättning med Azure Machine Learning Studio, se till att sökvägen till dina data innehåller tidsstämpel-information, inkludera alla undermappar med data och ange partitionens format.

I följande exempel tas alla data under undermappen *NoaaIsdFlorida/2019* och partitions formatet anger Tidsstämpelns år, månad och dag.

[![Partition format](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

I **schema** inställningarna anger du kolumnen tidsstämpelkolumn från en virtuell eller verklig kolumn i den angivna data uppsättningen:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Ange tidsstämpel":::

Om dina data är partitionerade efter datum, som är fallet här, kan du också ange partition_timestamp.  Detta ger en mer effektiv bearbetning av datum.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Partitionera tidstämpel":::


## <a name="create-dataset-monitors"></a>Skapa data uppsättnings övervakare

Skapa data uppsättnings Övervakare för att identifiera och varna data vid en ny data uppsättning.  Använd antingen [python SDK](#sdk-monitor) eller [Azure Machine Learning Studio](#studio-monitor).

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

Mer information finns i [referens dokumentationen för python SDK för data](/python/api/azureml-datadrift/azureml.datadrift) drift. 

I följande exempel visas hur du skapar en data uppsättnings övervakare med python SDK

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

Ett komplett exempel på hur du konfigurerar en data `timeseries` uppsättning och data riktnings detektor finns i vårt [exempel antecknings bok](https://aka.ms/datadrift-notebook).

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Azure Machine Learning Studio

1. Gå till [Studios start sida](https://ml.azure.com).
1. Välj fliken **data uppsättningar** till vänster. 
1. Välj **data uppsättnings övervakare**.
   ![Övervaka lista](./media/how-to-monitor-datasets/monitor-list.png)

1. Klicka på knappen **+ Skapa Övervakare** och fortsätt att gå igenom guiden genom att klicka på **Nästa**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Guiden skapa en övervakare":::

* **Välj mål data uppsättning**.  Mål data uppsättningen är en tabell data uppsättning med en tidsstämpel-kolumn som ska analyseras för data avvikelser. Mål data uppsättningen måste ha funktioner gemensamt med bas linje data uppsättningen och måste vara en `timeseries` data uppsättning som nya data läggs till i. Historiska data i mål data uppsättningen kan analyseras eller också kan nya data övervakas.

* **Välj bas linje data uppsättning.**  Välj den tabell data uppsättning som ska användas som bas linje för jämförelse av mål data uppsättningen över tid.  Bas linjens data uppsättning måste ha funktioner som är gemensamma för mål data uppsättningen.  Välj ett tidsintervall om du vill använda ett segment av mål data uppsättningen eller ange en separat data mängd som ska användas som bas linje.

* **Övervaka inställningar**.  De här inställningarna gäller för den schemalagda data behandlings övervakaren, som kommer att skapas. 

    | Inställningen | Beskrivning | Tips | Föränderlig | 
    | ------- | ----------- | ---- | ------- |
    | Name | Namnet på data uppsättnings övervakaren. | | No |
    | Funktioner | Lista över funktioner som kommer att analyseras för data drift över tid. | Ställ in till en modells utmatnings funktion (er) för att mäta begrepps avvikelsen. Inkludera inte funktioner som används naturligt över tid (månad, år, index osv.). Du kan fylla på och befintlig data riktnings övervakning när du har justerat listan med funktioner. | Yes | 
    | Beräkningsmål | Azure Machine Learning Compute Target för att köra data uppsättnings övervaknings jobben. | | Yes | 
    | Aktivera | Aktivera eller inaktivera schemat i pipelinen för data uppsättnings övervakaren | Inaktivera schemat för att analysera historiska data med den egna fyllnings inställningen. Den kan aktive ras när data uppsättnings övervakaren har skapats. | Yes | 
    | Frekvens | Den frekvens som används för att schemalägga pipeline-jobbet och analysera historiska data om en hel fyllning körs. Alternativen omfattar varje dag, varje vecka eller varje månad. | Varje körning jämför data i mål data uppsättningen enligt frekvensen: <li>Varje dag: jämför senaste fullständiga dag i mål data uppsättningen med bas linje <li>Varje vecka: jämför den senaste fullständiga veckan (måndag – söndag) i mål data uppsättningen med bas linje <li>Varje månad: jämför den senaste fullständiga månaden i mål data uppsättningen med bas linje | No | 
    | Svarstid | Tid i timmar tar det för data att komma in i data uppsättningen. Till exempel, om det tar tre dagar innan data tas emot i SQL DB-datauppsättningen inkapslade, ställer du in svars tiden på 72. | Kan inte ändras efter att data uppsättnings övervakaren har skapats | No | 
    | E-postadresser | E-postadresser för aviseringar baserat på överträdelse av tröskelvärdet för data avvikelse i procent. | E-postmeddelanden skickas via Azure Monitor. | Yes | 
    | Tröskelvärde | Tröskelvärde för data avvikelse i procent för e-postavisering. | Ytterligare aviseringar och händelser kan anges för många andra mått i arbets ytans associerade Application Insights-resurs. | Yes |

När du har slutfört guiden visas den resulterande data uppsättnings övervakaren i listan. Välj den för att gå till den övervakarens informations sida.

## <a name="understand-data-drift-results"></a>Förstå data avvikelse resultat

Det här avsnittet visar resultatet av övervakning av en data uppsättning, som finns på sidan **data** uppsättnings  /  **data uppsättning övervakare** i Azure Studio.  Du kan uppdatera inställningarna samt analysera befintliga data för en viss tids period på den här sidan.  

Börja med insikter på den översta nivån i data driftens storlek och en högdager av funktioner som ska undersökas ytterligare.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Avvikelse översikt":::


| Mått | Beskrivning | 
| ------ | ----------- | 
| Data riktnings storlek | En procent andel av driften mellan bas linjen och mål data uppsättningen över tid. Mellan 0 och 100 anger 0 identiska data mängder och 100 anger att den Azure Machine Learning data riktnings modellen kan se till att de två data uppsättningarna skiljer sig. Brus i exakt uppmätt procents ATS förväntas på grund av maskin inlärnings tekniker som används för att generera den här storleken. | 
| Top drivgarn-funktioner | Visar funktionerna från den data uppsättning som har förfallit mest och som därför bidrar till måttet för drifts storlek. På grund av covariate Shift behöver inte den underliggande distributionen av en funktion nödvändigt vis ändra för att ha en relativt hög funktions betydelse. |
| Tröskelvärde | Data växlings storlek utöver det inställda tröskelvärdet utlöser aviseringar. Detta kan konfigureras i övervaknings inställningarna. | 

### <a name="drift-magnitude-trend"></a>Tendens för drifts storlek

Se hur data uppsättningen skiljer sig från mål data uppsättningen under den angivna tids perioden.  Närmare 100% är fler än de två data uppsättningarna olika.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Tendens för drifts storlek":::

### <a name="drift-magnitude-by-features"></a>Funktions storlek efter funktioner

Det här avsnittet innehåller insikter på funktions nivå i ändringen av den valda funktionens distribution, samt annan statistik, över tid.

Mål data uppsättningen är också profilerad över tid. Det statistiska avståndet mellan bas linje fördelningen för varje funktion jämförs med mål data uppsättningen över tid.  Det påminner om detta på samma sätt som för data avvikelsens storlek.  Det här statistiska avståndet är dock för en enskild funktion i stället för alla funktioner. Min, max och medelvärde är också tillgängliga.

I Azure Machine Learning Studio klickar du på en stapel i diagrammet för att se information om funktions nivå för det datumet. Som standard visas distribution av bas linjens data uppsättning och den senaste körnings distributionen av samma funktion.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Funktions storlek efter funktioner":::

Dessa mått kan också hämtas i python SDK genom `get_metrics()` metoden för ett `DataDriftDetector` objekt.

### <a name="feature-details"></a>Funktions information

Rulla slutligen nedåt för att visa information om varje enskild funktion.  Använd List rutorna ovanför diagrammet för att välja funktionen och välj sedan det mått som du vill visa.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numeriskt funktions diagram och jämförelse":::

Måtten i diagrammet beror på typen av funktion.

* Numeriska funktioner

    | Mått | Beskrivning |  
    | ------ | ----------- |  
    | Wasserstein avstånd | Minsta arbets mängd för att transformera bas linje distribution till mål distributionen. |
    | Genomsnitts värde | Genomsnittligt värde för funktionen. |
    | Minvärde | Det minsta värdet för funktionen. |
    | Maxvärde | Det maximala värdet för funktionen. |

* Kategoriska-funktioner
    
    | Mått | Beskrivning |  
    | ------ | ----------- |  
    | Euclidian avstånd     |  Beräknad för kategoriska-kolumner. Euclidean avstånd beräknas på två vektorer som genereras från empirisk distribution av samma kategoriska-kolumn från två data uppsättningar. 0 betyder att det inte finns någon skillnad i empiriska distributioner.  Desto mer avviker från 0, desto mer är kolumnen. Trender kan observeras från ett Time Series-område för det här måttet och kan vara till hjälp vid avläsning av en drift funktion.  |
    | Unika värden | Antal unika värden (kardinalitet) för funktionen. |

I det här diagrammet väljer du ett datum för att jämföra funktions fördelningen mellan målet och det här datumet för den visade funktionen. För numeriska funktioner visar detta två sannolikhets distributioner.  Om funktionen är numerisk visas ett stapeldiagram.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Välj ett datum som ska jämföras med målet":::

## <a name="metrics-alerts-and-events"></a>Mått, aviseringar och händelser

Mått kan frågas i den [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) -resurs som är kopplad till Machine Learning-arbetsytan. Du har åtkomst till alla funktioner i Application Insights, inklusive inställningar för anpassade aviserings regler och åtgärds grupper för att utlösa en åtgärd som, t. ex. en e-post/SMS/push/röst eller Azure function. Mer information finns i den fullständiga Application Insights dokumentationen. 

Kom igång genom att gå till [Azure Portal](https://portal.azure.com) och välja **översikts** sidan för din arbets yta.  Den tillhör ande Application Insights resursen är längst till höger:

[![Översikt över Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Välj loggar (analys) under övervakning i den vänstra rutan:

![Översikt över Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Data uppsättnings övervaknings måtten lagras som `customMetrics` . Du kan skriva och köra en fråga när du har ställt in en data uppsättnings Övervakare för att visa dem:

[![Log Analytics-fråga](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

När du har identifierat mått för att ställa in aviserings regler skapar du en ny aviserings regel:

![Ny varnings regel](./media/how-to-monitor-datasets/alert-rule.png)

Du kan använda en befintlig åtgärds grupp eller skapa en ny för att definiera vilken åtgärd som ska vidtas när angivna villkor uppfylls:

![Ny åtgärds grupp](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>Nästa steg

* Gå till [Azure Machine Learning Studio](https://ml.azure.com) eller [python Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) för att ställa in en data uppsättnings övervakare.
* Se hur du konfigurerar data drift på [modeller som har distribuerats till Azure Kubernetes-tjänsten](./how-to-enable-data-collection.md).
* Konfigurera data uppsättnings riktnings övervakare med [Event Grid](how-to-use-event-grid.md). 
* Se dessa vanliga [fel söknings tips](resource-known-issues.md#data-drift) om du har problem.
