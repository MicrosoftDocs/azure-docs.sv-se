---
title: Get-Metric i Azure Monitor Application Insights
description: Lär dig hur du effektivt använder GetMetric ()-anropet för att avbilda lokalt församlade mått för .NET-och .NET Core-program med Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719788"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Anpassad mått samling i .NET och .NET Core

Azure Monitor Application Insights .NET-och .NET Core SDK: er har två olika metoder för att samla in anpassade mått, `TrackMetric()` och `GetMetric()` . Den viktigaste skillnaden mellan dessa två metoder är lokal agg regering. `TrackMetric()` saknar föragg regering under `GetMetric()` för-aggregering. Den rekommenderade metoden är att använda agg regering och `TrackMetric()` är därför inte längre den bästa metoden för att samla in anpassade mått. Den här artikeln beskriver hur du använder metoden GetMetric () och några av grunderna bakom hur det fungerar.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric jämfört med GetMetric

`TrackMetric()` skickar RAW-telemetri som anger ett mått. Det är ineffektivt att skicka ett enda telemetri objekt för varje värde. `TrackMetric()` är också ineffektiv i förhållande till prestanda eftersom varje `TrackMetric(item)` går genom den fullständiga SDK-pipelinen för telemetri-initierare och processorer. Till skillnad från `TrackMetric()` , `GetMetric()` hanterar lokal församling för dig och skickar sedan bara ett sammanställt Summary-mått till ett fast intervall på en minut. Så om du behöver övervaka vissa anpassade mått på den andra eller till och med millisekundnivå kan du göra det samtidigt som du bara kommer att kosta lagrings-och nätverks trafiken varje minut. Detta minskar också risken för begränsning som inträffar eftersom det totala antalet telemetridata som måste skickas för ett sammanställt mått minskar avsevärt.

I Application Insights är anpassade mått som samlas in via `TrackMetric()` och `GetMetric()` inte föremål för [sampling](./sampling.md). Sampling av viktiga mått kan leda till scenarier där aviseringar som du kan ha byggt runt dessa mått kan bli otillförlitliga. Genom att aldrig sampla in dina anpassade mått, kan du vanligt vis vara säker på att när aviserings tröskelvärdena har brutits, utlöses en avisering.  Men eftersom anpassade mått inte samplas, finns det några möjliga problem.

Om du behöver spåra trender i ett mått varje sekund eller i ett ännu mer detaljerat intervall kan detta resultera i:

- Ökade kostnader för data lagring. Det finns en kostnad för hur mycket data du skickar till Azure Monitor. (Den mer information som du skickar desto högre är den övergripande kostnaden för övervakning.)
- Ökad nätverks trafik/prestanda kostnader. (I vissa fall kan detta ha både en penning-och program prestanda kostnad.)
- Risk för inmatnings begränsning. (Tjänsten Azure Monitor släpper ("Restore") data punkter när din app skickar en mycket hög telemetri i ett kort tidsintervall.)

Begränsning är särskilt viktigt i likhet med provtagning, kan begränsningen leda till uteblivna aviseringar eftersom villkoret att utlösa en avisering skulle inträffa lokalt och sedan släppas vid inmatnings slut punkten på grund av för mycket data som skickas. Detta är varför för .NET och .NET Core vi rekommenderar att du inte använder `TrackMetric()` om du inte har implementerat din egen lokala agg regerings logik. Om du försöker spåra varje instans som en händelse inträffar under en viss tids period kanske du upptäcker att det [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) passar bättre. Även om det är viktigt att de anpassade händelserna är i åtanke till exempel för anpassade mått. Naturligtvis kan du fortfarande använda kursen `TrackMetric()` även utan att behöva skriva din egen lokala församling, men om du gör det måste du vara medveten om fall GRO par.

I sammanfattning `GetMetric()` är den rekommenderade metoden eftersom den utför för insamlingen, ackumulerar värden från alla spår ()-anrop och skickar en sammanfattning/samling en gång i minuten. Detta kan avsevärt minska kostnaderna och prestanda genom att skicka färre data punkter och samtidigt samla all relevant information.

> [!NOTE]
> Endast .NET-och .NET Core SDK: er har en GetMetric ()-metod. Om du använder Java kan du använda [micrometer-mått](./micrometer-java.md) eller `TrackMetric()` . För Java Script och Node.js du fortfarande använda `TrackMetric()` , men tänk på vilka varningar som beskrivs i föregående avsnitt. För python kan du använda [openinventering. statistik](./opencensus-python.md#metrics) för att skicka anpassade mått, men måtten är annorlunda.

## <a name="getting-started-with-getmetric"></a>Komma igång med GetMetric

I våra exempel kommer vi att använda ett grundläggande .NET Core 3,1 Worker service-program. Om du vill exakt replikera test miljön som användes med de här exemplen följer du steg 1-6 i [artikeln övervakning av arbets tjänst](./worker-service.md#net-core-30-worker-service-application) för att lägga till Application Insights i en Basic Work service-projektmall. Dessa begrepp gäller för alla allmänna program där SDK kan användas, inklusive webbappar och konsol program.

### <a name="sending-metrics"></a>Skickar mått

Ersätt innehållet i `worker.cs` filen med följande:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Om du kör koden ovan och ser att telemetri skickas via Visual Studio-utdatafönstret eller ett verktyg som Telerik-Fiddler, ser du att loopen upprepas upprepade gånger utan att någon telemetri skickas och att ett enda telemetri-objekt skickas med cirka det 60-andra märket, som i fallet med vårt test ser ut så här :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Det här objektet för telemetri representerar en mängd med 41 distinkta mått mått. Eftersom vi skickade samma värde över och över igen, har vi en *standard avvikelse (STDAV)* på 0 med ett identiskt *maximum (max)* och *minst (min)* värden. Egenskapen *Value* representerar summan av alla enskilda värden som aggregerades.

> [!NOTE]
> GetMetric stöder inte spårning av det sista värdet (t. ex. mätare) eller spårning av histogram/distributioner.

Om vi tittar på vår Application Insights-resurs i loggar (analys) kan det här enskilda telemetridata se ut så här:

![Log Analytics frågevy](./media/get-metric/log-analytics.png)

> [!NOTE]
> Även om objektet för den obehandlade telemetri-posten inte innehöll en explicit sum-egenskap/ett fält när vi har infogat ett sådant. I det här fallet `value` representerar både `valueSum` egenskapen och samma sak.

Du kan också få åtkomst till din anpassade metriska telemetri i avsnittet [_mått_](../essentials/metrics-charts.md) i portalen. Både som en [logg-baserad och anpassad mått](pre-aggregated-metrics-log-metrics.md). (Skärm bilden nedan är ett exempel på en log-baserad.) ![Vyn mått Utforskaren](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Cachelagra mått referens för användning med hög data flöde

I vissa fall observeras mått värden mycket ofta. Till exempel kan en hög data flödes tjänst som bearbetar 500 begär Anden/sekund vilja generera 20 telemetri mått för varje begäran. Det innebär att spåra 10 000 värden per sekund. I sådana scenarier med stora data flöden kan användarna behöva hjälpa SDK genom att undvika vissa sökningar.

I det här fallet kan exemplet ovan utföra en sökning efter en referens för måttet "ComputersSold" och sedan spåra det observerade värdet 42. I stället kan referensen cachelagras för flera spåra anrop:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Förutom att cachelagra mått handtaget, minskade exemplet ovan `Task.Delay` till 50 millisekunder så att slingan körs oftare, vilket resulterade i 772- `TrackValue()` anrop.

## <a name="multi-dimensional-metrics"></a>Flerdimensionella mått

I exemplen i föregående avsnitt visas noll mått. Mått kan också vara flerdimensionella. Vi stöder för närvarande 10 dimensioner.

 Här är ett exempel på hur du skapar ett endimensionell mått:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Om du kör den här koden i minst 60 sekunder kommer tre distinkta telemetri-objekt att skickas till Azure, var och en som representerar agg regeringen av en av de tre form faktorerna. Som innan du kan granska dessa i loggar (Analytics) vy:

![Log Analytics-vy över flerdimensionellt mått](./media/get-metric/log-analytics-multi-dimensional.png)

Samt i Metrics Explorer-upplevelsen:

![Anpassade mått](./media/get-metric/custom-metrics.png)

Du kommer dock att märka att du inte kan dela upp måttet med den nya anpassade dimensionen eller Visa din anpassade dimension med vyn mått:

![Delar upp support](./media/get-metric/splitting-support.png)

Som standard är flerdimensionella mått i Metric Explorer-upplevelsen inte aktiverade i Application Insights-resurser.

### <a name="enable-multi-dimensional-metrics"></a>Aktivera flerdimensionella mått

Om du vill aktivera flerdimensionella mått för en Application Insights resurs väljer du **användning och uppskattade kostnader**  >  **anpassade mått**  >  **aktiverar aviseringar för anpassade mått dimensioner**  >  **OK**. Mer information om detta hittar du [här](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

När du har gjort den här ändringen och skickat ny multi-dimensionell telemetri, kan du **tillämpa delning**.

> [!NOTE]
> Endast nyligen skickade mått efter det att funktionen aktiverades i portalen har dimensioner lagrade.

![Använd delning](./media/get-metric/apply-splitting.png)

Och Visa dina mått agg regeringar för varje _FormFactor_ -dimension:

![Form faktorer](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>Så här använder du MetricIdentifier när det finns fler än tre dimensioner

För närvarande stöds 10 dimensioner men större än tre dimensioner kräver användning av `MetricIdentifier` :

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Anpassad mått konfiguration

Om du vill ändra mått konfigurationen måste du göra detta på den plats där måttet initieras.

### <a name="special-dimension-names"></a>Särskilda dimensions namn

Måtten använder inte telemetri-kontexten för det `TelemetryClient` används för att komma åt dem. särskilda dimensions namn som är tillgängliga som konstanter i `MetricDimensionNames` klassen är den bästa lösningen för den här begränsningen.

Mått sammanställningar som skickas av nedan "-mått har **inte** `Context.Operation.Name` angetts till" speciell åtgärd "-mått. `TrackMetric()`Eller andra TrackXXX () har `OperationName` rätt inställning till "särskild åtgärd".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

I detta fall använder du de särskilda dimensions namn som anges i `MetricDimensionNames` klassen för att ange `TelemetryContext` värden.

Om till exempel den mått mängd som skapas från nästa instruktion skickas till Application Insights moln slut punkten, `Context.Operation.Name` anges data fältet till "särskild åtgärd":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Värdena för den här särskilda dimensionen kommer att kopieras till `TelemetryContext` och används inte som en normal-dimension. Om du även vill behålla en åtgärds dimension för normal mått utforskning måste du skapa en separat dimension för detta ändamål:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Capping för dimension och Time-serien

 Du kan kontrol lera det maximala antalet data serier per mått för att förhindra att under systemet för telemetri oavsiktligt använder sig av dina resurser. Standard gränserna är högst 1000 total data serie per mått och högst 100 olika värden per dimension.

 I samband med dimensions-och tids serie capping vi använder `Metric.TrackValue(..)` för att se till att gränserna observeras. Om gränserna redan har nåtts `Metric.TrackValue(..)` returneras "falskt" och värdet spåras inte. Annars returnerar den "true". Detta är användbart om data för ett mått härstammar från användarindata.

`MetricConfiguration`Konstruktorn har vissa alternativ för att hantera olika serier inom respektive mått och ett objekt i en klass som implementerar `IMetricSeriesConfiguration` som anger agg regerings beteende för varje enskild serie av måttet:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` är det maximala antalet data tids serier som ett mått kan innehålla. När den här gränsen har uppnåtts `TrackValue()` spåras inte anrop till.
* `valuesPerDimensionLimit` begränsar antalet distinkta värden per dimension på liknande sätt.
* `restrictToUInt32Values` Anger om endast positiva heltals värden ska spåras eller inte.

Här är ett exempel på hur du skickar ett meddelande för att veta om gräns gränser överskrids:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer ](./worker-service.md)om att övervaka Worker service-program.
* För ytterligare information om [log-baserade och föraggregerade mått](./pre-aggregated-metrics-log-metrics.md).
* [Metric Explorer](../essentials/metrics-getting-started.md)
* Så här aktiverar du Application Insights för [ASP.net Core program](asp-net-core.md)
* Så här aktiverar du Application Insights för [ASP.NET-program](asp-net.md)
