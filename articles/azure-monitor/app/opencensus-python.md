---
title: Övervaka python-program med Azure Monitor | Microsoft Docs
description: Innehåller instruktioner för att ansluta till openräkningar python med Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 69472da4f774a1dfae86e1891255907ad711175a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047430"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurera Azure Monitor för ditt python-program

Azure Monitor stöder distribuerad spårning, Metric-insamling och loggning av python-program via integrering med [openräkning](https://opencensus.io). Den här artikeln vägleder dig genom processen att konfigurera openräkning för python och skicka övervaknings data till Azure Monitor.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Python-installation. I den här artikeln används [python-3.7.0](https://www.python.org/downloads/release/python-370/), även om andra versioner sannolikt kommer att fungera med mindre ändringar. SDK: n stöder bara python-versionerna 2,7 och 3.6 +.
- Skapa en Application Insights- [resurs](./create-new-resource.md). Du tilldelas din egen Instrumentation-nyckel (iKey) för din resurs.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument med python SDK för openräkning för Azure Monitor

Installera Azure Monitor-exportörer för Open-räkning:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure`Kommandot förutsätter att du har en `PATH` miljö variabel uppsättning för python-installationen. Om du inte har konfigurerat den här variabeln måste du ge fullständig katalog Sök väg till den plats där din python-körbara fil finns. Resultatet är ett kommando som detta: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

SDK använder tre Azure Monitor exportörer för att skicka olika typer av telemetri till Azure Monitor. De är spårning, mått och loggar. Mer information om dessa typer av telemetri finns i [Översikt över data plattformen](../data-platform.md). Använd följande instruktioner för att skicka dessa typer av telemetri via de tre exportörerna.

## <a name="telemetry-type-mappings"></a>Typ mappningar för telemetri

Här är de exportörer som openräkning ger mappat till de typer av telemetri som visas i Azure Monitor.

| Pelare för iakttagithet | Typ av telemetri i Azure Monitor    | Förklaring                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Loggar                    | Spår, undantag, customEvents   | Logga telemetri, undantag telemetri, telemetri för händelser |
| Mått                 | customMetrics, performanceCounters | Prestanda räknare för anpassade mått                |
| Spårning                 | Begär beroenden             | Inkommande begär Anden, utgående begär Anden                |

### <a name="logs"></a>Loggar

1. Först ska vi generera vissa lokala loggdata.

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

1. Koden uppmanar kontinuerligt att ange ett värde. En loggpost genereras för varje angivet värde.

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

1. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera loggdata till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget. Du kan också ange det i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kod exempel:

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

1. Export verktyget skickar loggdata till Azure Monitor. Du kan hitta data under `traces` . 

    > [!NOTE]
    > I det här sammanhanget är `traces` inte samma som `tracing` . Här `traces` refererar till den typ av telemetri som visas i Azure Monitor när du använder `AzureLogHandler` . Men `tracing` avser ett koncept i openräkning och relaterar till [distribuerad spårning](./distributed-tracing.md).

    > [!NOTE]
    > Rot loggaren har kon figurer ATS med VARNINGs nivån. Det innebär att alla loggar som du skickar som har mindre av allvarlighets graden ignoreras och i sin tur inte skickas till Azure Monitor. Mer information finns i [dokumentationen](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Du kan också lägga till anpassade egenskaper till dina logg meddelanden i argumentet *extra* nyckelord genom att använda fältet custom_dimensions. Dessa egenskaper visas som nyckel/värde-par i `customDimensions` i Azure Monitor.
    > [!NOTE]
    > För att den här funktionen ska fungera måste du skicka en ord lista till fältet custom_dimensions. Om du skickar argument av någon annan typ ignorerar loggaren dem.

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

#### <a name="configure-logging-for-django-applications"></a>Konfigurera loggning för django-program

Du kan konfigurera loggningen explicit i program koden, till exempel ovan för dina django-program, eller så kan du ange den i loggnings konfigurationen för django. Den här koden kan gå till den fil som du använder för konfiguration av Django-inställningar. Information om hur du konfigurerar django-inställningar finns i [django-inställningar](https://docs.djangoproject.com/en/3.0/topics/settings/). Mer information om hur du konfigurerar loggning finns i [django-loggning](https://docs.djangoproject.com/en/3.0/topics/logging/).

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

Se till att du använder loggaren med samma namn som den som anges i konfigurationen.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Skicka undantag

Openräkning python spårar inte automatiskt och skickar `exception` telemetri. De skickas genom `AzureLogHandler` att använda undantag via python-loggnings biblioteket. Du kan lägga till anpassade egenskaper precis som med normal loggning.

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
Eftersom du måste logga undantag explicit är det upp till användaren hur de vill logga ohanterade undantag. Openräkning begränsar inte begränsningar för hur en användare vill göra detta, så länge de uttryckligen loggar en telemetri om undantag.

#### <a name="send-events"></a>Skicka händelser

Du kan skicka `customEvent` telemetri på exakt samma sätt som du skickar `trace` telemetri förutom att använda `AzureEventHandler` i stället.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Samling

Om du vill ha mer information om sampling i openinventering kan du titta på [sampling i openräkning](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Loggkorrelation

Mer information om hur du kan utöka dina loggar med spårnings kontext data finns i openräkningar python [logs integration](./correlation.md#log-correlation).

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python [telemetri-processorer](./api-filtering-sampling.md#opencensus-python-telemetry-processors).


### <a name="metrics"></a>Mått

Openaggity. stats stöder 4 agg regerings metoder men ger delvis stöd för Azure Monitor:

- **Antal:** Antalet mått punkter. Värdet är kumulativt, kan bara öka och återställa till 0 vid omstart. 
- **Sum:** En summering av mått punkterna. Värdet är kumulativt, kan bara öka och återställa till 0 vid omstart. 
- **LastValue:** Behåller det senast registrerade värdet och släpper allt annat.
- **Distribution:** Histogrammets distribution av mått punkter. Den här metoden **stöds inte av Azure-exporten**.

### <a name="count-aggregation-example"></a>Exempel på antal sammansättningar

1. Först ska vi generera några lokala mått data. Vi ska skapa ett enkelt mått för att spåra hur många gånger användaren väljer nyckeln **Enter** .

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
1. Om du kör koden upprepade gånger visas en uppmaning om att välja **RETUR**. Ett mått skapas för att spåra antalet gånger som **RETUR** är markerat. Med varje post ökar värdet och mått informationen visas i-konsolen. Informationen omfattar det aktuella värdet och den aktuella tidsstämpeln när måttet uppdaterades.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Även om det är praktiskt att ange värden i demonstrations syfte, vill vi i slut ändan generera mått data till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget. Du kan också ange det i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kod exempel:

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

1. Export verktyget skickar mått data till Azure Monitor med ett fast intervall. Standardvärdet är var 15: e sekund. Vi håller på att spåra ett enda mått, så dessa mått data, med det värde och den tidsstämpel som den innehåller, skickas varje intervall. Värdet är kumulativt, kan bara öka och återställa till 0 vid omstart. Du kan hitta data under `customMetrics` , men `customMetrics` egenskaperna ValueCount, ValueSum, ValueMin, VALUEMAX och valueStdDev används inte på ett effektivt sätt.

#### <a name="performance-counters"></a>Prestandaräknare

Som standard skickar mått export verktyget en uppsättning prestanda räknare till Azure Monitor. Du kan inaktivera detta genom att ställa in `enable_standard_metrics` flaggan på `False` i konstruktorn för mått export verktyget.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Dessa prestanda räknare skickas för närvarande:

- Tillgängligt minne (byte)
- PROCESSOR processor tid (i procent)
- Frekvens för inkommande begäran (per sekund)
- Genomsnittlig körnings tid för inkommande begäran (millisekunder)
- Processor användning för processor (procent andel)
- Privata byte för process (byte)

Du bör kunna se dessa mått i `performanceCounters` . Mer information finns i [prestanda räknare](./performance-counters.md).

#### <a name="modify-telemetry"></a>Ändra telemetri

Information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python telemetri- [processorer](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

### <a name="tracing"></a>Spårning

> [!NOTE]
> I Open-inventering, `tracing` avser [distribuerad spårning](./distributed-tracing.md). `AzureExporter`Skickar `requests` och `dependency` telemetri till Azure Monitor.

1. Först ska vi generera vissa spårnings data lokalt. I python-inaktivitet, eller valfritt redigerings program, anger du följande kod:

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

1. Om du kör koden upprepade gånger visas en uppmaning om att ange ett värde. För varje post skrivs värdet till gränssnittet. Python-modulen för openräkning genererar en motsvarande del av `SpanData` . Projektet openspanion definierar en [spårning som ett träd över spänner](https://opencensus.io/core-concepts/tracing/).
    
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

1. Även om det är praktiskt att ange värden i demonstrations syfte, vi vill att vi ska generera `SpanData` till Azure Monitor. Skicka anslutnings strängen direkt till export verktyget. Du kan också ange det i en miljö variabel `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ändra koden från föregående steg baserat på följande kod exempel:

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

1. Nu när du kör python-skriptet bör du fortfarande uppmanas att ange värden, men bara värdet skrivs ut i gränssnittet. Det skapade `SpanData` skickas till Azure Monitor. Du hittar de utgivna span-data under `dependencies` . Mer information om utgående begär Anden finns i openräkningar python- [beroenden](./opencensus-python-dependency.md).
Mer information om inkommande begär Anden finns i openräkningar python- [begäranden](./opencensus-python-request.md).

#### <a name="sampling"></a>Samling

Om du vill ha mer information om sampling i openinventering kan du titta på [sampling i openräkning](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Spåra korrelation

För mer information om telemetri-korrelation i dina spårnings data, ta en titt på python- [korrelationen](./correlation.md#telemetry-correlation-in-opencensus-python)för openräkning.

#### <a name="modify-telemetry"></a>Ändra telemetri

Mer information om hur du ändrar spårad telemetri innan det skickas till Azure Monitor finns i openräkningar python telemetri- [processorer](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Konfigurera Azure Monitor exportörer

Som det visas finns det tre olika Azure Monitor exportörer som stöder openräkning. Varje skickar flera olika typer av telemetri till Azure Monitor. Information om vilka typer av telemetri som varje exportör skickar, finns i följande lista.

Varje exportör accepterar samma argument för konfiguration, som skickas genom konstruktörerna. Du kan se information om var och en:

- `connection_string`: Anslutnings strängen som används för att ansluta till din Azure Monitor-resurs. Prioriteras `instrumentation_key` .
- `enable_standard_metrics`: Används för `AzureMetricsExporter` . Signalerar export verktyget att skicka [prestanda räknar](../essentials/app-insights-metrics.md#performance-counters) mått automatiskt till Azure Monitor. Standardvärdet är `True` .
- `export_interval`: Används för att ange frekvensen i sekunder för exporten.
- `instrumentation_key`: Instrumentation-nyckeln som används för att ansluta till din Azure Monitor-resurs.
- `logging_sampling_rate`: Används för `AzureLogHandler` . Innehåller en samplings frekvens [0, 1.0] för att exportera loggar. Standardvärdet är 1,0.
- `max_batch_size`: Anger den maximala storleken på telemetri som exporteras samtidigt.
- `proxies`: Anger en sekvens med proxyservrar som ska användas för att skicka data till Azure Monitor. Mer information finns i [proxyservrar](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: En sökväg till platsen där den lokala lagringsmappen finns (ingen telemetri har skickats). Från och med `opencensus-ext-azure` v 1.0.3 är standard Sök vägen den tillfälliga katalogen för operativ systemets Temp + `opencensus-python`  +  `your-ikey` . Före v-1.0.3 är standard Sök vägen $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Visa dina data med frågor

Du kan visa telemetri-data som har skickats från ditt program via fliken **loggar (Analytics)** .

![Skärm bild av översikts fönstret med "loggar (analyser)" markerade i en röd ruta](./media/opencensus-python/0010-logs-query.png)

I listan under **Active**:

- För telemetri som skickas med Azure Monitor trace-export ören visas inkommande begär Anden under `requests` . Utgående eller aktiva begär Anden visas under `dependencies` .
- För telemetri som skickas med Azure Monitor Metrics-exporten visas de skickade måtten under `customMetrics` .
- För telemetri som skickas med Azure Monitor loggar exportör visas loggar under `traces` . Undantag visas under `exceptions` .

Mer detaljerad information om hur du använder frågor och loggar finns [i loggar i Azure Monitor](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Läs mer om openräkning för python

* [Python-räkningar på GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Anpassning](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor exportörer på GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integreringar med openräkning](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor exempel program](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Nästa steg

* [Spåra inkommande begär Anden](./opencensus-python-dependency.md)
* [Spåra pågående förfrågningar](./opencensus-python-request.md)
* [Program karta](./app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](../app/tutorial-performance.md)

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](./monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](./proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Mått aviseringar](../alerts/alerts-log.md): Ställ in aviseringar för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

