---
title: 'Dela anpassade vyer med parametriserade URL: er – Azure Time Series Insights | Microsoft Docs'
description: 'Lär dig hur du skapar parametriserade URL: er för att enkelt dela anpassade Explorer-vyer i Azure Time Series Insights.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91665335"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Dela en anpassad vy med en parametriserad URL

Om du vill dela en anpassad vy i Azure Time Series Insights Explorer kan du program mässigt skapa en parametriserad URL för den anpassade vyn.

Azure Time Series Insights Explorer stöder parametrar för URL-frågor för att ange vyer i upplevelsen direkt från URL: en. Med bara URL:en kan du till exempel ange en målmiljö, ett sökpredikat och önskat tidsintervall. När en användare väljer den anpassade URL: en, tillhandahåller gränssnittet en länk direkt till den till gången i Azure Time Series Insights portalen. Dataåtkomstprinciper tillämpas.

> [!TIP]
>
> * Visa den kostnads fria [Azure Time Series Insights demonstrationen](https://insights.timeseries.azure.com/samples).
> * Läs dokumentationen om den medföljande [Azure Time Series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Miljö-ID

Parametern `environmentId=<guid>` anger målmiljö-ID:t. Det är en komponent i data åtkomst-FQDN, och du hittar den i det övre högra hörnet i miljö översikten i Azure Portal. Det är allt som föregår `env.timeseries.azure.com` .

Ett exempel på miljö-ID-parametern är `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tid

Du kan ange absoluta eller relativa tidsvärden med en parametriserad URL.

### <a name="absolute-time-values"></a>Absoluta tidsvärden

För absoluta tidsvärden använder du parametrarna `from=<integer>` och `to=<integer>`.

* `from=<integer>` är ett värde i JavaScript-millisekunder för starttiden för sökintervallet.
* `to=<integer>` är ett värde i JavaScript-millisekunder för sluttiden för sökintervallet.

> [!TIP]
> Om du enkelt vill översätta datum till JavaScript-millisekunder, kan du prova med den [epok & UNIX timestamp-konverteraren](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relativa tidsvärden

För ett relativt tids värde använder `relativeMillis=<value>` , där *värdet* är i Java Script millisekunder från den senaste tidsstämpeln som togs emot från API: et.

`&relativeMillis=3600000` visar till exempel data från de senaste 60 minuterna.

Godkända värden motsvarar **snabb tids** menyn i Azure Time Series Insights Explorer och inkluderar:

* `1800000` (Senaste 30 minuterna)
* `3600000` (Senaste 60 minuter)
* `10800000` (Senaste 3 timmarna)
* `21600000` (Senaste 6 timmarna)
* `43200000` (Senaste 12 timmarna)
* `86400000` (Senaste 24 timmarna)
* `604800000` (Senaste 7 dagarna)
* `2592000000` (Senaste 30 timmarna)

### <a name="optional-parameters"></a>Valfria parametrar

`timeSeriesDefinitions=<collection of term objects>`Parametern anger vilka predikat som ska visas i en Azure Time Series Insights vy:

| Parameter | URL-objekt | Beskrivning |
| --- | --- | --- |
| **Namn** | `\<string>` | Namnet på *villkoret*. |
| **splitBy** | `\<string>` | Kolumnnamnet att *dela med*. |
| **measureName** | `\<string>` | Kolumnnamnet för *mått*. |
| **predikatet** | `\<string>` | *Where*-sats för filtrering på serversidan. |
| **useSum** | `true` | En valfri parameter som anger att summan ska används för måttet. |

> [!NOTE]
> Om `Events` är valt **useSum** mått är Count valt som standard.  
> Om `Events` inte är valt väljs Average som standard. |

* `multiChartStack=<true/false>`Nyckel värdes paret aktiverar stackning i diagrammet.
* `multiChartSameScale=<true/false>`Nyckel/värde-paret möjliggör samma skala för Y-axeln mellan termer inom en valfri parameter.  
* Med `timeBucketUnit=<Unit>&timeBucketSize=<integer>` kan du justera skjutreglaget intervall så att det ger en mer detaljerad eller smidigare, mer sammanslagen vy av diagrammet.  
* `timezoneOffset=<integer>`Parametern låter dig ange tids zonen för diagrammet som ska visas som en förskjutning till UTC.

| Par (er) | Beskrivning |
| --- | --- |
| `multiChartStack=false` | `true` är aktiverat som standard för att skicka `false` till stack. |
| `multiChartStack=false&multiChartSameScale=true` | Stapling måste aktiveras att använda samma skala för y-axeln i villkoren.  Det är `false` som standard, så överföring `true` aktiverar den här funktionen. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units =,,, `days` `hours` `minutes` `seconds` , `milliseconds` .  Inled alltid enheten med versal. </br> Definiera antalet enheter genom att skicka önskat heltal för **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Heltalet är alltid i millisekunder. |

> [!NOTE]
> **timeBucketUnit** -värden kan jämnas upp till 7 dagar.
> **timezoneOffset** -värden är varken UTC eller lokal tid.

### <a name="examples"></a>Exempel

Lägg till tids serie definitioner i en Azure Time Series Insights miljö som en URL-parameter genom att lägga till:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Använd exempel tid serie definitioner för:

* Miljö-ID
* De senaste 60 minuterna data
* Villkoren (**F1PressureID**, **F2TempStation** och **F3VibrationPL**) som utgör de valfria parametrarna

Du kan skapa följande parametriserade URL för en vy:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL för Azure Time Series Insights Explorer-parameter](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Se Explorer Live [med hjälp av URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) -exemplet ovan.

URL: en ovan beskriver och visar den parameterstyrda Azure Time Series Insights Explorer-vyn.

* Parametriserade predikat.

  [![Azure Time Series Insights Explorer-parametriserade predikat.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Vyn delat fullständigt diagram.

  [![Vyn delat fullständigt diagram.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [frågar data med C#](time-series-insights-query-data-csharp.md).

* Lär dig mer om [Azure Time Series Insights Explorer](./time-series-insights-explorer.md).
