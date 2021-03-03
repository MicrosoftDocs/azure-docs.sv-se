---
title: Webhook-åtgärder för logg aviseringar i Azure-aviseringar
description: Beskriver hur du konfigurerar en logg avisering push-meddelanden med webhook-åtgärd och tillgängliga anpassningar
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 19cf900b8e943b4f9ee69a6241ebc7ddb5a3dba6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704403"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för loggaviseringsregler

[Logg aviseringen](alerts-log.md) har stöd för [konfiguration av åtgärds grupper för webhook](./action-groups.md#webhook). I den här artikeln beskriver vi vilka egenskaper som är tillgängliga och hur du konfigurerar en anpassad JSON-webhook.

> [!NOTE]
> Anpassad JSON-baserad webhook stöds för närvarande inte i API-versionen `2020-05-01-preview`

> [!NOTE]
> Vi rekommenderar att du använder [vanliga aviserings scheman](../alerts/alerts-common-schema.md) för webhook-integreringar. Det vanliga aviserings schemat ger fördelarna med att ha en enda utöknings bar och enhetlig aviserings nytto last för alla aviserings tjänster i Azure Monitor. För logg aviserings regler som har en definierad Anpassad JSON-nyttolast, så återställer det gemensamma schemat nytto Last schema till det som beskrivs [här](../alerts/alerts-common-schema-definitions.md#log-alerts). Aviseringar med det gemensamma schemat aktiverat har en övre storleks gräns på 256 KB per varning. större aviseringar innehåller inte Sök resultat. När Sök resultaten inte ingår bör du använda `LinkToFilteredSearchResultsAPI` eller `LinkToSearchResultsAPI` för att få åtkomst till frågeresultaten via Log Analytics-API: et.

## <a name="webhook-payload-properties"></a>Egenskaper för webhook-nyttolast

Med webhook-åtgärder kan du anropa en enda HTTP POST-begäran. Tjänsten som anropas bör ha stöd för Webhooks och veta hur du använder den nytto last som den tar emot.

Standard egenskaper för webhook-åtgärd och deras anpassade JSON-parameter namn:

| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Aviserings regelns namn. |
| *Allvarlighetsgrad* |#severity |Allvarlighets grad har angetts för den utlöst logg aviseringen. |
| *AlertThresholdOperator* |#thresholdoperator |Aviserings regelns tröskelvärdes operator. |
| *AlertThresholdValue* |#thresholdvalue |Tröskel värde för varnings regeln. |
| *LinkToSearchResults* |#linktosearchresults |Länk till analys portalen som returnerar poster från frågan som skapade aviseringen. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Länk till analys-API: et som returnerar poster från frågan som skapade aviseringen. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Länk till analys portalen som returnerar poster från frågan som filtrerats efter dimensions värde kombinationer som skapade aviseringen. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Länk till den analys-API som returnerar poster från frågan som filtrerats efter dimensions värde kombinationer som skapade aviseringen. |
| *Resultcount som* |#searchresultcount |Antal poster i Sök resultaten. |
| *Slut tid för Sök intervall* |#searchintervalendtimeutc |Slut tid för frågan i UTC, med formatet MM/DD/ÅÅÅÅ HH: mm: SS. |
| *Sök intervall* |#searchinterval |Tids period för varnings regeln med formatet HH: mm: SS. |
| *Sök intervall StartTime* |#searchintervalstarttimeutc |Start tid för frågan i UTC, med formatet MM/DD/ÅÅÅÅ HH: mm: SS. 
| *SearchQuery* |#searchquery |Loggs öknings fråga som används av varnings regeln. |
| *SearchResults* |"IncludeSearchResults": true|Poster som returneras av frågan som en JSON-tabell, begränsade till de första 1 000 posterna. "IncludeSearchResults": true läggs till i en anpassad JSON webhook-definition som en egenskap på den översta nivån. |
| *Dimensioner* |"IncludeDimensions": true|Dimensions värde kombinationer som utlöste denna avisering som ett JSON-avsnitt. "IncludeDimensions": true läggs till i en anpassad JSON webhook-definition som en egenskap på den översta nivån. |
| *Aviserings typ*| #alerttype | Typ av logg aviserings regel som kon figurer ATS som [mått mätning eller antal resultat](./alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID för din Log Analytics-arbetsyta. |
| *Program-ID* |#applicationid |ID för Application Insights-appen. |
| *Prenumerations-ID* |#subscriptionid |ID för din Azure-prenumeration som används. |

## <a name="custom-webhook-payload-definition"></a>Definition av anpassad webhook-nyttolast

Du kan använda **inkludera anpassad JSON-nyttolast för webhook** för att hämta en anpassad JSON-nyttolast med parametrarna ovan. Du kan också generera ytterligare egenskaper.
Du kan till exempel ange följande anpassade nytto last som innehåller en enda parameter som kallas *text*. Tjänsten som denna webhook anropar förväntar sig följande parameter:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Den här exempel nytto lasten matchas till något som liknar följande när den skickas till webhooken:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Variabler i en anpassad webhook måste anges inom ett JSON-hölje. Till exempel kommer referens till "#searchresultcount" i exemplet ovan webhook ut baserat på aviserings resultatet.

Om du vill inkludera Sök Resultat lägger du till **IncludeSearchResults** som en egenskap på den översta nivån i den anpassade JSON-filen. Sök resultaten ingår som en JSON-struktur, så det går inte att referera till resultat i anpassade definierade fält. 

> [!NOTE]
> Knappen **Visa webhook** bredvid alternativet **inkludera anpassad JSON-nyttolast för webhook** visar förhands granskning av vad som tillhandahölls. Det innehåller inte faktiska data, utan är representativt för det JSON-schema som ska användas. 

## <a name="sample-payloads"></a>Exempel på nytto laster
I det här avsnittet visas exempel på nytto laster för Webhooks för logg aviseringar. Exempel på nytto laster innehåller exempel när nytto lasten är standard och när den är anpassad.

### <a name="log-alert-for-log-analytics"></a>Logg avisering för Log Analytics
Följande exempel på nytto Last är för en standard-webhook-åtgärd som används för aviseringar baserat på Log Analytics:

> [!NOTE]
> Värdet för fältet allvarlighets grad ändras om du har [växlat till det aktuella scheduledQueryRules-API: et](../alerts/alerts-log-api-switch.md) från det [äldre Log Analytics aviserings-API: et](./api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Logg avisering för Application Insights
Följande exempel på nytto Last är för en standard-webhook när den används för logg aviseringar baserat på Application Insights resurser:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Logg avisering för andra resurs loggar (från API `2020-05-01-preview` -version)

> [!NOTE]
> Det finns för närvarande inga ytterligare avgifter för API-versionen `2020-05-01-preview` och resursbaserade logg aviseringar.  Prissättningen för funktioner som finns i förhands granskning kommer att meddelas i framtiden och ett meddelande som visas innan faktureringen påbörjas. Om du väljer att fortsätta använda nya API-versioner och resursbaserade logg aviseringar efter meddelande perioden debiteras du enligt tillämplig taxa.

Följande exempel på nytto Last är för en standard-webhook när den används för logg aviseringar baserade på andra resurs loggar (exklusive arbets ytor och Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Logg avisering med en anpassad JSON-nyttolast
Om du till exempel vill skapa en anpassad nytto last som endast innehåller aviserings namnet och Sök resultaten använder du den här konfigurationen: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Följande exempel på nytto Last är för en anpassad webhook-åtgärd för valfri logg avisering:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [logg aviseringar i Azure-aviseringar](./alerts-unified-log.md).
- Förstå hur du [hanterar logg aviseringar i Azure](alerts-log.md).
- Skapa och hantera [Åtgärds grupper i Azure](./action-groups.md).
- Läs mer om [Application Insights](../logs/log-query-overview.md).
- Läs mer om [logg frågor](../logs/log-query-overview.md).