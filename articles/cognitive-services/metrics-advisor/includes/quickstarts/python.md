---
title: Mått rådgivare python snabb start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2ea2e24049a2ff57e284894bc13c44d617f3c661
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445421"
---
[Referens dokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  [Paket (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* När du har en Azure-prenumeration kan du <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" skapa en Metrics Advisor-resurs "  target="_blank"> som skapar en Metric Advisor-resurs </a> i Azure Portal för att distribuera din Metric Advisor-instans.  
* Din egen SQL-databas med Time Series-data.
  
> [!TIP]
> * Du kan hitta python Metrics Advisor-exempel på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Det kan ta 10 till 30 minuter för din Metric Advisor-resurs att distribuera en tjänst instans som du kan använda. Klicka på **gå till resurs** när den har distribuerats. Efter distributionen kan du börja använda Metric Advisor-instansen med både webb portalen och REST API.
> * Du hittar URL: en för REST API i Azure Portal i avsnittet **Översikt** i din resurs. Resultatet ser ut så här:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa en ny python-fil och importera följande bibliotek.

```python
import os
import datetime
```

Skapa variabler för resursens Azure-slutpunkt och nyckel.

> [!IMPORTANT]
> Gå till Azure-portalen. Om den mått Advisor-resurs som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar prenumerations nycklar och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. <br><br>För att hämta din API-nyckel måste du gå till [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Välj lämplig: **katalog**, **prenumeration** och **arbets yta** för resursen och välj **Kom igång**. Du kommer sedan att kunna hämta dina API-nycklar från [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i python SDK för Metrics Advisor.

|Name|Beskrivning|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Används för**: <br> – Visar incidenter <br> – Visar rotor saken till incidenter <br> -Hämtar ursprungliga tids serie data och tids serie data som har berikats av tjänsten. <br> – Visa aviseringar <br> – Lägga till feedback för att finjustera din modell |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Gör att du kan:** <br> – Hantera datafeeds <br> -Skapa, konfigurera, Hämta, lista och ta bort konfigurationer för avvikelse identifiering <br> -Skapa, konfigurera, Hämta, lista och ta bort avvikelser för varningar <br> -Hantera hookar  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Vilka mått rådgivare inhämtar från data källan. A `DataFeed` innehåller rader med:** <br> – Tidsstämplar <br> -Noll eller flera dimensioner <br> -En eller flera mått  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | A `DataFeedMetric` är ett mätbart mått som används för att övervaka och utvärdera status för en enskild affärs process. Det kan vara en kombination av flera tids serie värden indelade i dimensioner. Till exempel kan ett webb hälso mått innehålla dimensioner för antal användare och en-US-marknad. |

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med klient biblioteket Metrics Advisor för python:

* [Autentisera klienten](#authenticate-the-client)
* [Lägg till en datafeed](#add-a-data-feed)
* [Kontrol lera inmatnings status](#check-the-ingestion-status)
* [Konfiguration av identifiering och aviserings konfiguration](#configure-anomaly-detection)
* [Skapa en aviserings konfiguration](#create-an-alert-configuration)
* [Fråga om avvikelse identifierings resultat](#query-the-alert)

## <a name="authenticate-the-client"></a>Autentisera klienten

Klienten i det här exemplet är ett `MetricsAdvisorAdministrationClient` objekt som använder din slut punkt och ett `MetricsAdvisorKeyCredential` objekt som innehåller dina nycklar. Du behöver inte kopiera det här kod exemplet. De metoder som du skapar senare kommer att instansiera klienten. Den alternativa klienten kallas `MetricsAdvisorClient` Mer information på den här klienten finns i [referens dokumentationen](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Lägg till en datafeed

I en ny metod skapar du import-uttryck som exemplet nedan. Ersätt `sql_server_connection_string` med din egen SQL Server-anslutningssträng och Ersätt `query` med en fråga som returnerar dina data med en enda tidsstämpel. Du måste också justera `DataFeedmetric` `DataFeedDimension` värdena och utifrån dina anpassade data.

> [!IMPORTANT]
> Frågan ska returnera högst en post för varje dimensions kombination vid varje tidsstämpel. Och alla poster som returneras av frågan måste ha samma tidsstämplar. Metrics Advisor kör den här frågan för varje tidsstämpel för att mata in dina data. Mer information och exempel finns i [avsnittet Vanliga frågor och svar om frågor](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

Skapa en klient med dina nycklar och din slut punkt och Använd `client.create_data_feed()` för att konfigurera namn, källa, granularitet och schema. Du kan också ange tid för inhämtning, sammanslagnings inställningar med mera.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Kontrol lera inmatnings status

I en ny metod skapar du en import-instruktion som exemplet nedan. Ersätt `data_feed_id` med ID för datafeeden som du skapade. Skapa en klient med dina nycklar och din slut punkt och Använd `client.list_data_feed_ingestion_status()` för att hämta inmatnings processen. Skriv ut informationen, till exempel senaste aktiva och slutförda tidsstämplar.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Konfigurera avvikelse identifiering

I en ny metod skapar du import-uttryck som exemplet nedan. Ersätt `metric_id` med ID för det mått som du vill konfigurera. Skapa en klient med dina nycklar och din slut punkt och Använd `client.create_detection_configuration` för att skapa en ny identifierings konfiguration. Tröskel villkoren anger parametrarna för avvikelse identifiering.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Skapa en Hook

I en ny metod skapar du import-uttryck som exemplet nedan. Skapa en klient med dina nycklar och din slut punkt och Använd `client.create_hook()` för att skapa en Hook. Ange en beskrivning, en lista med e-postmeddelanden som aviseringen ska skickas till och en extern länk för att ta emot aviseringen.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Skapa en aviserings konfiguration

I en ny metod skapar du import-uttryck som exemplet nedan. Ersätt `detection_configuration_id` med ID för konfigurationen av avvikelse identifiering och Ersätt `hook_id` med hooken som du skapade tidigare. Skapa en klient med dina nycklar och din slut punkt och Använd `client.create_alert_configuration()` för att skapa en aviserings konfiguration. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Fråga aviseringen

I en ny metod skapar du en import-instruktion som exemplet nedan. Ersätt `alert_id` med ID för aviseringen och Ersätt `alert_config_id` med aviserings konfigurationens ID. Skapa en klient med dina nycklar och din slut punkt och Använd `client.list_anomalies` för att visa en lista över avvikelser för en avisering. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```