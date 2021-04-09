---
title: Data modell för Azure Application Insights | Microsoft Docs
description: Beskriver egenskaper som exporteras från löpande export i JSON och som används som filter.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: b4609d54c1c3c33a654dd58a3bceaca4974fda15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584214"
---
# <a name="application-insights-export-data-model"></a>Application Insights exportera data modell
Den här tabellen innehåller egenskaperna för telemetri som skickas från [Application Insights](./app-insights-overview.md) SDK: er till portalen.
Du ser dessa egenskaper i data utdata från [kontinuerlig export](export-telemetry.md).
De visas också i egenskaps filter i [Metric Explorer](../essentials/metrics-charts.md) och [diagnostisk sökning](./diagnostic-search.md).

Poäng till Anmärkning:

* `[0]` i dessa tabeller anger du en punkt i sökvägen där du måste infoga ett index. men det är inte alltid 0.
* Tids längden är i tiondelar av en mikrosekund, så 10000000 = = 1 sekund.
* Datum och tid är UTC och anges i ISO-format `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Exempel

```json
// A server report about an HTTP request
{
  "request": [
    {
      "urlData": { // derived from 'url'
        "host": "contoso.org",
        "base": "/",
        "hashTag": ""
      },
      "responseCode": 200, // Sent to client
      "success": true, // Default == responseCode<400
      // Request id becomes the operation id of child events
      "id": "fCOhCdCnZ9I=",  
      "name": "GET Home/Index",
      "count": 1, // 100% / sampling rate
      "durationMetric": {
        "value": 1046804.0, // 10000000 == 1 second
        // Currently the following fields are redundant:
        "count": 1.0,
        "min": 1046804.0,
        "max": 1046804.0,
        "stdDev": 0.0,
        "sampledValue": 1046804.0
      },
      "url": "/"
    }
  ],
  "internal": {
    "data": {
      "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
      "documentVersion": "1.61"
    }
  },
  "context": {
    "device": { // client browser
      "type": "PC",
      "screenResolution": { },
      "roleInstance": "WFWEB14B.fabrikam.net"
    },
    "application": { },
    "location": { // derived from client ip
      "continent": "North America",
      "country": "United States",
      // last octagon is anonymized to 0 at portal:
      "clientip": "168.62.177.0",
      "province": "",
      "city": ""
    },
    "data": {
      "isSynthetic": true, // we identified source as a bot
      // percentage of generated data sent to portal:
      "samplingRate": 100.0,
      "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
    },
    "user": {
      "isAuthenticated": false,
      "anonId": "us-tx-sn1-azr", // bot agent id
      "anonAcquisitionDate": "0001-01-01T00:00:00Z",
      "authAcquisitionDate": "0001-01-01T00:00:00Z",
      "accountAcquisitionDate": "0001-01-01T00:00:00Z"
    },
    "operation": {
      "id": "fCOhCdCnZ9I=",
      "parentId": "fCOhCdCnZ9I=",
      "name": "GET Home/Index"
    },
    "cloud": { },
    "serverDevice": { },
    "custom": { // set by custom fields of track calls
      "dimensions": [ ],
      "metrics": [ ]
    },
    "session": {
      "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
      "isFirst": true
    }
  }
}
```

## <a name="context"></a>Kontext
Alla typer av telemetri åtföljs av ett kontext avsnitt. Alla dessa fält överförs inte med varje data punkt.

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| Context. Custom. dimensions [0] |objekt [] |Nyckel/värde-värdepar som anges av parametern anpassade egenskaper. Nyckelns max längd 100, värdenas max längd 1024. Om det finns fler än 100 unika värden kan egenskapen sökas, men kan inte användas för segmentering. Högst 200 nycklar per iKey. |
| Context. Custom. Metrics [0] |objekt [] |Nyckel/värde-par som anges av anpassade mått parametrar och av TrackMetrics. Nyckelns max längd 100, värdena kan vara numeriska. |
| Context. data. eventTime |sträng |UTC |
| Context. data. isSynthetic |boolean |Förfrågan verkar komma från ett robot-eller webbtest. |
| Context. data. samplingRate |antal |Procent andel telemetri som genererats av SDK som skickas till portalen. Intervallet 0,0-100,0. |
| Context. Device |objekt |Klienten het |
| kontext. Device. browser |sträng |IE, Chrome,... |
| Context. Device. browserVersion |sträng |Chrome 48,0,... |
| Context. Device. deviceModel |sträng | |
| Context. Device. enhets namn |sträng | |
| context.device.id |sträng | |
| Context. Device. locale |sträng |en-GB, de-DE,... |
| Context. Device. Network |sträng | |
| Context. Device. oemName |sträng | |
| Context. Device. OS |sträng | |
| Context. Device. osVersion |sträng |Värdoperativsystem |
| Context. Device. roleInstance |sträng |ID för Server värd |
| Context. Device. roleName |sträng | |
| Context. Device. screenResolution |sträng | |
| Context. Device. Type |sträng |PC, webbläsare,... |
| kontext. plats |objekt |Härledd från `clientip` . |
| kontext. location. ort |sträng |Härledd från `clientip` , om känd |
| Context. location. clientip |sträng |Senaste Åttahörning är anonymiserats till 0. |
| Context. location. kontinent |sträng | |
| Context. location. Country |sträng | |
| kontext. location. provins |sträng |Region |
| context.operation.id |sträng |Objekt som har samma `operation id` visas som relaterade objekt i portalen. Vanligt vis `request id` . |
| context.operation.name |sträng |URL eller namn på begäran |
| Context. operation. |sträng |Tillåter kapslade relaterade objekt. |
| context.session.id |sträng |`Id` för en grupp med åtgärder från samma källa. En period på 30 minuter utan en åtgärd signalerar slutet av en session. |
| Context. session. isFirst |boolean | |
| Context. user. accountAcquisitionDate |sträng | |
| Context. user. accountId |sträng | |
| Context. user. anonAcquisitionDate |sträng | |
| Context. user. anonId |sträng | |
| Context. user. authAcquisitionDate |sträng |[Autentiserad användare](./api-custom-events-metrics.md#authenticated-users) |
| Context. user. authId |sträng | |
| Context. user. isAuthenticated |boolean | |
| Context. user. storeRegion |sträng | |
| internal.data.documentVersion |sträng | |
| internal.data.id |sträng | `Unique id` som tilldelas när ett objekt matas in till Application Insights |

## <a name="events"></a>Händelser
Anpassade händelser som genererats av [TrackEvent ()](./api-custom-events-metrics.md#trackevent).

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| antal händelser [0] |heltal |100/([samplings](./sampling.md) frekvens). Till exempel 4 = &gt; 25%. |
| namn på händelse [0] |sträng |Händelse namn.  Maxlängd 250. |
| URL för händelse [0] |sträng | |
| händelse [0] urlData. Base |sträng | |
| händelse [0] urlData. Host |sträng | |

## <a name="exceptions"></a>Undantag
Rapporterar [undantag](./asp-net-exceptions.md) på servern och i webbläsaren.

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| basicException [0]-sammansättning |sträng | |
| antal basicException [0] |heltal |100/([samplings](./sampling.md) frekvens). Till exempel 4 = &gt; 25%. |
| basicException [0] exceptionGroup |sträng | |
| basicException [0] undantag |sträng | |
| basicException [0] failedUserCodeMethod |sträng | |
| basicException [0] failedUserCodeAssembly |sträng | |
| basicException [0] handledAt |sträng | |
| basicException [0] hasFullStack |boolean | |
| basicException [0] `id` |sträng | |
| metoden basicException [0] |sträng | |
| basicException [0] meddelande |sträng |Undantags meddelande. Max längd 10 000. |
| basicException [0] outerExceptionMessage |sträng | |
| basicException [0] outerExceptionThrownAtAssembly |sträng | |
| basicException [0] outerExceptionThrownAtMethod |sträng | |
| basicException [0] outerExceptionType |sträng | |
| basicException [0] outerId |sträng | |
| basicException [0] parsedStack [0] sammansättning |sträng | |
| basicException [0] parsedStack [0] filnamn |sträng | |
| basicException [0] parsedStack [0] nivå |heltal | |
| basicException [0] parsedStack [0] rad |heltal | |
| basicException [0] parsedStack [0] metod |sträng | |
| basicException [0] stack |sträng |Max längd 10 000 |
| basicException [0] typeName |sträng | |

## <a name="trace-messages"></a>Spåra meddelanden
Skickas av [TrackTrace](./api-custom-events-metrics.md#tracktrace)och av [loggnings korten](./asp-net-trace-logs.md).

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| meddelande [0] loggerName |sträng | |
| meddelande [0] parametrar |sträng | |
| meddelande [0] RAW |sträng |Logg meddelandet, max längd 10. |
| meddelande [0] severityLevel |sträng | |

## <a name="remote-dependency"></a>Fjärrberoende
Skickat av TrackDependency. Används för att rapportera prestanda och användning av [anrop till beroenden](./asp-net-dependencies.md) i servern och AJAX-anrop i webbläsaren.

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| remoteDependency [0] asynkron |boolean | |
| remoteDependency [0] baseName |sträng | |
| remoteDependency [0] commandName |sträng |Till exempel "Start/index" |
| antal remoteDependency [0] |heltal |100/([samplings](./sampling.md) frekvens). Till exempel 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |sträng |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |antal |Tid från anrop till slut för ande av svar genom beroende |
| remoteDependency [0] `id` |sträng | |
| remoteDependency [0] namn |sträng |Adresser. Maxlängd 250. |
| remoteDependency [0] resultCode |sträng |från HTTP-beroende |
| remoteDependency [0] lyckades |boolean | |
| remoteDependency [0] typ |sträng |Http, SQL,... |
| URL för remoteDependency [0] |sträng |Maxlängd 2000 |
| remoteDependency [0] urlData. Base |sträng |Maxlängd 2000 |
| remoteDependency [0] urlData. hashtagg |sträng | |
| remoteDependency [0] urlData. Host |sträng |Maxlängd 200 |

## <a name="requests"></a>Begäranden
Skickat av [TrackRequest](./api-custom-events-metrics.md#trackrequest). Standardmodulerna använder detta för att rapportera Server svars tid, mätt på servern.

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| antal begär Anden [0] |heltal |100/([samplings](./sampling.md) frekvens). Till exempel: 4 = &gt; 25%. |
| begäran [0] durationMetric. Value |antal |Tid från begäran som kommer till svar. 1e7 = = 1s |
| begäran [0] `id` |sträng |`Operation id` |
| begär ande [0] namn |sträng |Hämta/publicera + URL-bas.  Maxlängd 250 |
| begäran [0] responseCode |heltal |HTTP-svar som skickats till klienten |
| begäran [0] lyckades |boolean |Standard = = (responseCode &lt; 400) |
| URL för begäran [0] |sträng |Inkluderar inte värd |
| begäran [0] urlData. Base |sträng | |
| begäran [0] urlData. hashtagg |sträng | |
| begäran [0] urlData. Host |sträng | |

## <a name="page-view-performance"></a>Sid visnings prestanda
Skickas av webbläsaren. Mäter tiden för bearbetning av en sida, från användare som initierar begäran att Visa fullständig (exklusive asynkrona AJAX-anrop).

Kontext värden visar klientens OS och webbläsarens version.

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |heltal |Tid från slutet av mottagning av HTML till visning av sidan. |
| clientPerformance [0] namn |sträng | |
| clientPerformance [0] networkConnection. Value |heltal |Åtgången tid för att upprätta en nätverks anslutning. |
| clientPerformance [0] receiveRequest. Value |heltal |Tid från slutet av att skicka begäran om att ta emot HTML-koden i svaret. |
| clientPerformance [0] sendRequest. Value |heltal |Den tid det tar att skicka HTTP-begäran. |
| clientPerformance [0] totalt. värde |heltal |Tid från början att skicka begäran om att visa sidan. |
| URL för clientPerformance [0] |sträng |URL för denna begäran |
| clientPerformance [0] urlData. Base |sträng | |
| clientPerformance [0] urlData. hashtagg |sträng | |
| clientPerformance [0] urlData. Host |sträng | |
| clientPerformance [0] urlData. Protocol |sträng | |

## <a name="page-views"></a>Sidvisningar
Skickat av trackPageView () eller [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| Visa [0] antal |heltal |100/([samplings](./sampling.md) frekvens). Till exempel 4 = &gt; 25%. |
| Visa [0] durationMetric. Value |heltal |Värdet kan anges i trackPageView () eller av startTrackPage ()-stopTrackPage (). Inte samma som clientPerformance-värden. |
| Visa [0] namn |sträng |Sid rubrik.  Maxlängd 250 |
| Visa [0] URL |sträng | |
| Visa [0] urlData. Base |sträng | |
| Visa [0] urlData. hashtagg |sträng | |
| Visa [0] urlData. Host |sträng | |

## <a name="availability"></a>Tillgänglighet
Reports för [webb test för tillgänglighet](./monitor-web-app-availability.md).

| Sökväg | Typ | Kommentarer |
| --- | --- | --- |
| tillgänglighet [0] availabilityMetric.name |sträng |availability |
| tillgänglighet [0] availabilityMetric. Value |antal |1,0 eller 0,0 |
| antal tillgänglighets [0] |heltal |100/([samplings](./sampling.md) frekvens). Till exempel 4 = &gt; 25%. |
| tillgänglighet [0] dataSizeMetric.name |sträng | |
| tillgänglighet [0] dataSizeMetric. Value |heltal | |
| tillgänglighet [0] durationMetric.name |sträng | |
| tillgänglighet [0] durationMetric. Value |antal |Testets varaktighet. 1e7 = = 1s |
| tillgänglighets meddelande [0] |sträng |Fel diagnostik |
| tillgänglighet [0] resultat |sträng |Godkänn/Underkänn |
| tillgänglighet [0] runLocation |sträng |Geo-källa för http-REQ |
| tillgänglighet [0] testName |sträng | |
| tillgänglighet [0] testRunId |sträng | |
| tillgänglighet [0] testTimestamp |sträng | |

## <a name="metrics"></a>Mått
Genererad av TrackMetric ().

Metric-värdet finns i context. Custom. Metrics [0]

Exempel:

```json
{
  "metric": [ ],
  "context": {
  ...
    "custom": {
      "dimensions": [
        { "ProcessId": "4068" }
      ],
      "metrics": [
        {
          "dispatchRate": {
            "value": 0.001295,
            "count": 1.0,
            "min": 0.001295,
            "max": 0.001295,
            "stdDev": 0.0,
            "sampledValue": 0.001295,
            "sum": 0.001295
          }
        }
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>Om mått värden
Metriska värden, både i mått rapporter och andra, rapporteras med en standard objekt struktur. Exempel:

```json
"durationMetric": {
  "name": "contoso.org",
  "type": "Aggregation",
  "value": 468.71603053650279,
  "count": 1.0,
  "min": 468.71603053650279,
  "max": 468.71603053650279,
  "stdDev": 0.0,
  "sampledValue": 468.71603053650279
}
```

För närvarande – även om detta kan ändras i framtiden – i alla värden som har rapporter ATS från standard-SDK-modulerna, `count==1` och endast `name` `value` fälten och är användbara. Det enda fallet är om du skriver dina egna TrackMetric-anrop där du anger de andra parametrarna.

Syftet med de andra fälten är att tillåta att måtten aggregeras i SDK, för att minska trafiken till portalen. Du kan till exempel beräkna flera efterföljande läsningar innan du skickar varje mått rapport. Sedan beräknar du den minsta, högsta, standard avvikelsen och det sammanlagda värdet (summa eller genomsnitt) och anger antalet läsningar som representeras av rapporten.

I tabellerna ovan har vi utelämnat antal sällan använda fält, min, Max, stdDev och sampledValue.

I stället för att i förväg aggregera mått kan du använda [sampling](./sampling.md) om du behöver minska mängden telemetri.

### <a name="durations"></a>Varaktigheter
Om inget annat anges visas varaktigheterna i tiondelar av en mikrosekund, så att 10000000,0 betyder 1 sekund.

## <a name="see-also"></a>Se även
* [Application Insights](./app-insights-overview.md)
* [Löpande export](export-telemetry.md)
* [Kodexempel](export-telemetry.md#code-samples)

