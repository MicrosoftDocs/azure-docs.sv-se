---
title: Konfigurerbara regler som är baserade på Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder referens data för att få en aviserings lösning som har konfigurerbara regler som är baserade på Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 2f9d132084f0254486be533daea6b54239f4e450
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019983"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processer som kan konfigureras med konfigurerbara regler i Azure Stream Analytics
I den här artikeln beskrivs hur du använder referens data för att få en varnings lösning som använder konfigurerbara regler för tröskel i Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: avisering baserat på justerbara regel trösklar
Du kan behöva skapa en avisering som utdata när inkommande strömmade händelser har nått ett visst värde, eller när ett sammanställt värde som baseras på inkommande strömmade händelser överskrider ett visst tröskelvärde. Det är enkelt att ställa in en Stream Analytics fråga som jämförde värdet med ett statiskt tröskelvärde som är fast och som är fördefinierat. Ett fast tröskelvärde kan vara hårdkodat i den strömmande frågesyntaxen med enkla numeriska jämförelser (större än, mindre än och lika med).

I vissa fall måste tröskelvärdena vara enklare att konfigurera utan att du redigerar frågesyntaxen varje gången ett tröskelvärde ändras. I andra fall kan du behöva flera enheter eller användare som bearbetas av samma fråga med var och en av dem med olika tröskelvärden på varje typ av enhet. 

Det här mönstret kan användas för att konfigurera tröskelvärden dynamiskt, selektivt välja vilken typ av enhet som tröskelvärdet gäller vid filtrering av indata och selektivt välja vilka fält som ska inkluderas i utdata.

## <a name="recommended-design-pattern"></a>Rekommenderat design mönster
Använd referens data inmatning till ett Stream Analytics jobb som en sökning efter tröskelvärden för aviseringar:
- Lagra tröskelvärdena i referens data, ett värde per nyckel.
- Anslut de strömmande data inmatnings händelserna till referens data i nyckel kolumnen.
- Använd det nyckelbaserade värdet från referens data som tröskelvärde.

## <a name="example-data-and-query"></a>Exempel data och fråga
I exemplet genereras aviseringar när en mängd data som strömmas från enheter i ett minut fönster matchar de angivna värdena i regeln som referens data.

I frågan, för varje deviceId och varje metricName under deviceId, kan du konfigurera mellan 0 och 5 dimensioner att gruppera efter. Endast de händelser som har motsvarande filter värden grupperade. När de grupper ATS, beräknas summan av min, Max, AVG, med ett rullande-fönster på 60 sekunder. Filter för de sammanställda värdena beräknas sedan enligt det konfigurerade tröskelvärdet i referensen för att generera händelsen aviserings utdata.

Anta till exempel att det finns ett Stream Analytics jobb som har ett referens data indata med namnet **regler** och strömma data inmatning med namnet **mått**. 

## <a name="reference-data"></a>Referens data
Det här exemplet på referens data visar hur en tröskel-baserad regel kan representeras. En JSON-fil innehåller referens data och sparas i Azure Blob Storage och den Blob storage-behållaren används som referens data indata med hjälp av **regler**. Du kan skriva över den här JSON-filen och ersätta regel konfigurationen när tiden går ut, utan att stoppa eller starta direkt uppspelnings jobbet.

- Regel exemplet används för att representera en justerbar avisering när processor överskrider (genomsnittet är större än eller lika med) värdet `90` procent. `value`Fältet kan konfigureras efter behov.
- Observera att regeln har ett **operator** fält som tolkas dynamiskt i frågesyntaxen senare `AVGGREATEROREQUAL` . 
- Regeln filtrerar data på en viss dimensions nyckel `2` med värde `C1` . Andra fält är tomma strängar, vilket innebär att det inte går att filtrera indataströmmen med dessa händelse fält. Du kan konfigurera ytterligare CPU-regler för att filtrera andra matchande fält efter behov.
- Alla kolumner ska inte ingå i aviserings aviserings händelsen. I det här fallet `includedDim` är nyckel numret `2` aktiverat `TRUE` för att representera att fält nummer 2 av händelse data i data strömmen ingår i de kvalificerande utmatnings händelserna. De andra fälten ingår inte i aviserings resultatet, men fält listan kan justeras.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Exempel fråga för strömning
I det här exemplet Stream Analytics frågan kopplas till **reglerna** referens data från exemplet ovan till en indataströmmen med data som heter **mått**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Exempel på strömmande data för händelse data
Det här exemplet på JSON-data representerar de **mått** som används i den direkt uppspelnings frågan. 

- Tre exempel händelser visas inom 1 minutens TimeSpan, värde `T14:50` . 
- Alla tre har samma `deviceId` värde `978648` .
- Värdena för processor värden varierar i varje händelse, `98` `95` `80` respektive. Endast de två första exempel händelserna överskrider den CPU-varnings regel som fastställs i regeln.
- Fältet includeDim i aviserings regeln var nyckel nummer 2. Motsvarande nyckel 2-fält i exempel händelserna heter `NodeName` . De tre exempel händelserna har värden `N024` , `N024` `N014` respektive. I utdata visas bara noden `N024` som är den enda data som matchar aviserings villkoren för hög processor. `N014` uppfyller inte tröskelvärdet för hög CPU.
- Varnings regeln konfigureras med en `filter` enda på nyckel nummer 2, som motsvarar `cluster` fältet i exempel händelser. De tre exempel händelserna har alla värde `C1` och matchar filter kriterierna.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Exempel på utdata
Detta exempel på utdata av JSON-data visar en enskild varnings händelse som har producerats baserat på regeln för processor tröskel som definierats i referens data. Händelsen utdata innehåller namnet på aviseringen samt sammanlagt (medelvärde, min, max) för fälten som beaktas. Data för utdata-händelsen innehåller fält nyckel nummer 2 `NodeName` värde `N024` på grund av regel konfigurationen. (JSON har ändrats för att Visa rad brytningar för läsbarhet.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
