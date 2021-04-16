---
title: Övervaka Python-program med Azure Monitor | Microsoft Docs
description: Innehåller anvisningar för att ställa in OpenCensus Python med Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 92d954a865a2d4a8c55177b132139dcd7d0444ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515931"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurera Azure Monitor python-program

Azure Monitor stöd för distribuerad spårning, måttinsamling och loggning av Python-program via integrering [med OpenCensus](https://opencensus.io). Den här artikeln beskriver steg för steg hur du ställer in OpenCensus för Python och skickar dina övervakningsdata till Azure Monitor.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Python-installation. I den här [artikeln används Python 3.7.0](https://www.python.org/downloads/release/python-370/), men andra versioner kommer troligen att fungera med mindre ändringar. SDK stöder endast Python v2.7 och v3.4-v3.7.
- Skapa en Application Insights [resurs](./create-new-resource.md). Du tilldelas din egen instrumentationsnyckel (ikey) för din resurs.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentera med OpenCensus Python SDK för Azure Monitor

Installera OpenCensus-Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> Kommandot `python -m pip install opencensus-ext-azure` förutsätter att du har en `PATH` miljövariabel inställd för Python-installationen. Om du inte har konfigurerat den här variabeln måste du ange den fullständiga katalogsökvägen till den plats där din körbara Python-fil finns. Resultatet är ett kommando som liknar följande: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

SDK:n använder tre Azure Monitor för att skicka olika typer av telemetri till Azure Monitor. De är spårning, mått och loggar. Mer information om dessa telemetrityper finns i [översikten över dataplattformen.](../data-platform.md) Följ anvisningarna nedan om du vill skicka dessa telemetrityper via de tre typerna.

## <a name="telemetry-type-mappings"></a>Mappningar av telemetrityper

Här är de exempel som OpenCensus tillhandahåller mappade till de typer av telemetri som du ser i Azure Monitor.

| Grundpelare för observerbarhet | Telemetrityp i Azure Monitor    | Förklaring                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Loggar                    | Spårningar, undantag, customEvents   | Loggtelemetri, undantagstelemetri, händelsetelemetri |
| Mått                 | customMetrics, performanceCounters | Prestandaräknare för anpassade mått                |
| Spårning                 | Begär beroenden             | Inkommande begäranden, utgående begäranden                |

### <a name="logs"></a>Loggar

1. Först ska vi generera några lokala loggdata.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Koden frågar kontinuerligt efter ett värde som ska anges. En loggpost genereras för varje angivet värde.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Även om det är användbart att ange värden i demonstrationssyfte vill vi i slutänden generera loggdata till Azure Monitor. Skicka anslutningssträngen direkt till exportören. Eller så kan du ange den i en miljövariabel, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kodexempel:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Exportören skickar loggdata till Azure Monitor. Du hittar data under `traces` . 

    > [!NOTE]
    > I det här `traces` sammanhanget är inte samma sak som `tracing` . Här `traces` refererar till den typ av telemetri som du ser i Azure Monitor du använder `AzureLogHandler` . Men `tracing` refererar till ett begrepp i OpenCensus och relaterar till [distribuerad spårning](./distributed-tracing.md).

    > [!NOTE]
    > Rotloggaren konfigureras med nivån VARNING. Det innebär att alla loggar som du skickar som har mindre allvarlighetsgrad ignoreras och i sin tur inte skickas till Azure Monitor. Mer information finns i [dokumentationen](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Du kan också lägga till anpassade egenskaper i dina loggmeddelanden i *det extra nyckelordsargumentet* med hjälp custom_dimensions-fältet. Dessa egenskaper visas som nyckel/värde-par i `customDimensions` i Azure Monitor.
    > [!NOTE]
    > För att den här funktionen ska fungera måste du skicka en ordlista till custom_dimensions fältet. Om du skickar argument av någon annan typ ignorerar loggaren dem.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Konfigurera loggning för Django-program

Du kan konfigurera loggning explicit i programkoden som ovan för dina Django-program, eller så kan du ange den i Djangos loggningskonfiguration. Den här koden kan gå till den fil som du använder för konfiguration av Django-inställningar. Information om hur du konfigurerar Django-inställningar finns [i Django-inställningar.](https://docs.djangoproject.com/en/3.0/topics/settings/) Mer information om hur du konfigurerar loggning finns [i Django-loggning.](https://docs.djangoproject.com/en/3.0/topics/logging/)

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Se till att du använder loggaren med samma namn som det som angavs i konfigurationen.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Skicka undantag

OpenCensus Python spårar och skickar inte `exception` telemetri automatiskt. De skickas med hjälp `AzureLogHandler` av undantag via Python-loggningsbiblioteket. Du kan lägga till anpassade egenskaper precis som med normal loggning.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Eftersom du måste logga undantag explicit är det upp till användaren hur de vill logga ohanterade undantag. OpenCensus har inga begränsningar för hur en användare vill göra detta, så länge de uttryckligen loggar en undantagstelemetri.

#### <a name="send-events"></a>Skicka händelser

Du kan skicka `customEvent` telemetri på exakt samma sätt som du skickar `trace` telemetri förutom med hjälp av `AzureEventHandler` i stället.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Samling

Information om sampling i OpenCensus finns i [sampling i OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Loggkorrelation

Mer information om hur du utökar loggar med spårningskontextdata finns i OpenCensus [Python-loggintegrering](./correlation.md#log-correlation).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus [Python-telemetriprocessorer.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)


### <a name="metrics"></a>Mått

OpenCensus.stats stöder 4 aggregeringsmetoder men ger partiellt stöd för Azure Monitor:

- **Antal:** Antalet måttpunkter. Värdet är kumulativt, kan bara öka och återställas till 0 vid omstart. 
- **Summa:** En sammanfattning av måttpunkterna. Värdet är kumulativt, kan bara öka och återställas till 0 vid omstart. 
- **LastValue:** Behåller det senast registrerade värdet och släpper allt annat.
- **Distribution:** Histogramfördelning för måttpunkterna. Den här metoden **stöds INTE av Azure Exporter**.

### <a name="count-aggregation-example"></a>Exempel på antal sammansättningar

1. Först ska vi generera några lokala måttdata. Vi skapar ett enkelt mått för att spåra hur många gånger användaren väljer **returnyckeln.**

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Om du kör koden upprepade gånger uppmanas du att välja **Retur.** Ett mått skapas för att spåra hur många gånger **Retur** har valts. Med varje post ökas värdet och måttinformationen visas i konsolen. Informationen innehåller det aktuella värdet och den aktuella tidsstämpeln när måttet uppdaterades.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Även om det är användbart att ange värden i demonstrationssyfte vill vi i slutänden generera måttdata till Azure Monitor. Skicka anslutningssträngen direkt till exportören. Eller så kan du ange den i en miljövariabel, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kodexempel:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. Exportören skickar måttdata till Azure Monitor med ett fast intervall. Standardvärdet är var 15:e sekund. Vi spårar ett enda mått, så dessa måttdata, oavsett värde och tidsstämpel, skickas varje intervall. Värdet är kumulativt, kan bara öka och återställas till 0 vid omstart. Du hittar data under `customMetrics` , men egenskaperna `customMetrics` valueCount, valueSum, valueMin, valueMax och valueStdDev används inte på ett effektivt sätt.

### <a name="setting-custom-dimensions-in-metrics"></a>Ange anpassade dimensioner i mått

Med Opencensus Python SDK kan du lägga till anpassade dimensioner i dina måtttelemetri via , som i princip är en ordlista med `tags` nyckel/värde-par. 

1. Infoga de taggar som du vill använda i taggkartan. Taggkartan fungerar som en sorts "pool" av alla tillgängliga taggar som du kan använda.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. För en specifik `View` anger du de taggar som du vill använda när du registrerar mått med den vyn via taggnyckeln.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. Se till att använda taggkartan när du spelar in i måttkartan. Taggnycklarna som anges i måste `View` finnas i taggkartan som används för att registrera.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. Under tabellen `customMetrics` har alla måttposter som genereras med hjälp av `prompt_view` anpassade dimensioner `{"url":"http://example.com"}` .

1. Om du vill skapa taggar med olika värden med samma nycklar skapar du nya taggmappningar för dem.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Prestandaräknare

Som standard skickar den måttexporter en uppsättning prestandaräknare till Azure Monitor. Du kan inaktivera detta genom att `enable_standard_metrics` ange flaggan till i `False` konstruktorn för måttexporteraren.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Dessa prestandaräknare skickas för närvarande:

- Tillgängligt minne (byte)
- Processortid (procent)
- Frekvens för inkommande begäranden (per sekund)
- Genomsnittlig körningstid för inkommande begäranden (millisekunder)
- Bearbeta CPU-användning (procent)
- Bearbeta privata byte (byte)

Du bör kunna se dessa mått i `performanceCounters` . Mer information finns i [prestandaräknare.](./performance-counters.md)

#### <a name="modify-telemetry"></a>Ändra telemetri

Information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus [Python-telemetriprocessorer.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)

### <a name="tracing"></a>Spårning

> [!NOTE]
> I OpenCensus `tracing` refererar till [distribuerad spårning](./distributed-tracing.md). skickar `AzureExporter` `requests` och `dependency` telemetri till Azure Monitor.

1. Först ska vi generera spårningsdata lokalt. I Python IDLE eller valfri redigerare anger du följande kod:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Om du kör koden upprepade gånger uppmanas du att ange ett värde. Med varje post skrivs värdet ut till gränssnittet. OpenCensus Python-modulen genererar en motsvarande del av `SpanData` . OpenCensus-projektet definierar [en spårning som ett träd med sträcker sig över](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Även om det är användbart att ange värden i demonstrationssyfte, vill vi i slutänden `SpanData` generera till Azure Monitor. Skicka anslutningssträngen direkt till exportören. Eller så kan du ange den i en miljövariabel, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kodexempel:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Nu när du kör Python-skriptet bör du fortfarande uppmanas att ange värden, men endast värdet skrivs ut i gränssnittet. Den skapade `SpanData` skickas till Azure Monitor. Du hittar de utgivna spannsdata under `dependencies` . Mer information om utgående begäranden finns i OpenCensus [Python-beroenden.](./opencensus-python-dependency.md)
Mer information om inkommande begäranden finns i OpenCensus [Python-begäranden.](./opencensus-python-request.md)

#### <a name="sampling"></a>Samling

Information om sampling i OpenCensus finns i [sampling i OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Spårningskorrelation

Mer information om telemetrikorrelation i dina spårningsdata finns i OpenCensus [Python-telemetrikorrelation](./correlation.md#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan den skickas till Azure Monitor finns i OpenCensus [Python-telemetriprocessorer.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)

## <a name="configure-azure-monitor-exporters"></a>Konfigurera Azure Monitor för inkonfigurering

Som du ser finns det tre olika Azure Monitor stöder OpenCensus. Var och en skickar olika typer av telemetri till Azure Monitor. Om du vill se vilka typer av telemetri varje exporter skickar kan du se följande lista.

Varje exporter accepterar samma argument för konfiguration som skickas via konstruktorerna. Du kan se information om var och en här:

- `connection_string`: Anslutningssträngen som används för att ansluta Azure Monitor resurs. Prioriterar `instrumentation_key` över .
- `enable_standard_metrics`: Används för `AzureMetricsExporter` . Signalerar exportören till att [skicka prestandaräknarmått](../essentials/app-insights-metrics.md#performance-counters) automatiskt till Azure Monitor. Standardvärdet är `True` .
- `export_interval`: Används för att ange frekvensen i sekunder för export.
- `instrumentation_key`: Instrumenteringsnyckeln som används för att ansluta Azure Monitor resurs.
- `logging_sampling_rate`: Används för `AzureLogHandler` . Visar samplingsfrekvensen [0,1.0] för export av loggar. Standardvärdet är 1.0.
- `max_batch_size`: Anger den maximala storleken på telemetri som exporteras samtidigt.
- `proxies`: Anger en sekvens med proxys som ska användas för att skicka data till Azure Monitor. Mer information finns i [proxys](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: En sökväg till den lokala lagringsmappen (icke-telemetri). Från och `opencensus-ext-azure` med v1.0.3 är standardsökvägen operativsystemets temp-katalog + `opencensus-python`  +  `your-ikey` . Före v1.0.3 är standardsökvägen $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Visa dina data med frågor

Du kan visa telemetridata som har skickats från ditt program via **fliken Loggar (Analys).**

![Skärmbild av översiktsfönstret med "Loggar (Analytics)" markerat i en röd ruta](./media/opencensus-python/0010-logs-query.png)

I listan under **Aktiv**:

- För telemetri som skickas med Azure Monitor spårningsexporter visas inkommande begäranden under `requests` . Utgående eller pågående begäranden visas under `dependencies` .
- För telemetri som skickas med Azure Monitor måttexporter visas skickade mått under `customMetrics` .
- För telemetri som skickas med Azure Monitor loggexporter visas loggarna under `traces` . Undantag visas under `exceptions` .

Mer detaljerad information om hur du använder frågor och loggar finns i [Loggar i Azure Monitor](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Läs mer om OpenCensus för Python

* [OpenCensus Python på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Anpassning](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor på GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-integreringar](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor exempelprogram](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Nästa steg

* [Spåra inkommande begäranden](./opencensus-python-dependency.md)
* [Spåra out-going-begäranden](./opencensus-python-request.md)
* [Programkarta](./app-map.md)
* [Prestandaövervakning från end-to-end](../app/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](./monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](./proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Måttaviseringar:](../alerts/alerts-log.md)Ställ in aviseringar för att varna dig om ett mått passerar ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

