---
title: Händelsekällor för strömningsinmatning – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om att strömma data Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 499cb3c978a67f9ef71e6ad9dd03be9f05b45729
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726978"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2-händelsekällor

Din Azure Time Series Insights Gen2-miljö kan ha upp till två strömmande händelsekällor. Två typer av Azure-resurser stöds som indata:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Händelser måste skickas som UTF-8-kodad JSON.

## <a name="create-or-edit-event-sources"></a>Skapa eller redigera händelsekällor

Händelsekällan är länken mellan din hubb och din Azure Time Series Insights Gen2-miljö och en separat resurs av typen `Time Series Insights event source` skapas i resursgruppen. Resurserna IoT Hub eller händelsehubben kan vara live i samma Azure-prenumeration som din Azure Time Series Insights Gen2-miljö eller en annan prenumeration. Det är dock bästa praxis att ha din Azure Time Series Insights miljö och IoT Hub eller Händelsehubb i samma Azure-region.

Du kan använda [Azure Portal,](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source) [Azure Resource Manager-mallar](time-series-insights-manage-resources-using-azure-resource-manager-template.md)och [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) för att skapa, redigera eller ta bort din miljös händelsekällor.

> [!WARNING]
> Begränsa inte offentlig Internetåtkomst till en hubb eller händelsekälla som används av Time Series Insights, annars bryts den nödvändiga anslutningen.

## <a name="start-options"></a>Startalternativ

När du skapar en händelsekälla kan du ange vilka befintliga data som ska samlas in. Den här inställningen är valfri. Följande alternativ är tillgängliga:

| Name   |  Beskrivning  |  Azure Resource Manager mallexempel |
|----------|-------------|------|
| EarliestAvailable | Mata in alla befintliga data som lagras i IoT eller Händelsehubb | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Börja mata in data som tas emot när händelsekällan har skapats. Alla befintliga data som strömmades innan händelsekällan skapades ignoreras. Det här är standardinställningen i Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Din miljö matar in data från din anpassade tidssekvens (UTC) framåt. Alla händelser som har förts in i din IoT eller händelsehubb vid eller efter din anpassade iqueued-tid kommer att matas in och lagras. Alla händelser som ankom före din anpassade tidpunkt ignoreras. Observera att "tid iqueued" avser den tid (i UTC) som händelsen ankom till i din IoT eller Event Hub. Detta skiljer sig från en anpassad [tidsstämpelegenskap](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) som finns i händelsens brödtext. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Om du väljer EarliestAvailable och har många befintliga data kan det uppstå lång svarstid när din Azure Time Series Insights Gen2-miljö bearbetar alla dina data.
> - Den här höga svarstiden bör så småningom minska när data indexeras. Skicka en supportbiljett via Azure Portal om du upplever kontinuerligt hög svarstid.

- EarliestAvailable

![TidigastTillgängligt diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![EventSourceCreationTime-diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![CustomEnqueuedTime-diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>Metodtips för strömningsinmatning

- Skapa alltid en unik konsumentgrupp för din Azure Time Series Insights Gen2-miljö för att använda data från din händelsekälla. Återanvändning av konsumentgrupper kan orsaka slumpmässiga frånkopplingar och kan leda till dataförlust.

- Konfigurera din Azure Time Series Insights Gen2-miljö och IoT Hub och/eller Event Hubs i samma Azure-region. Även om det är möjligt att konfigurera en händelsekälla i en separat region stöds inte det här scenariot och vi kan inte garantera hög tillgänglighet.

- Gå inte utöver din miljös gräns [för dataflöde eller](./concepts-streaming-ingress-throughput-limits.md) per partitionsgräns.

- Konfigurera en [fördröjningsavisering](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) för att få ett meddelande om din miljö har problem med att bearbeta data. Se [Produktionsarbetsbelastningar nedan](./concepts-streaming-ingestion-event-sources.md#production-workloads) för föreslagna aviseringsvillkor.

- Använd strömningsinmatning endast för data i nära realtid och senaste, men strömning av historiska data stöds inte.

- Förstå hur egenskaper kommer att kommas över och [JSON-data plattas ut och lagras.](./concepts-json-flattening-escaping-rules.md)

- Följ principen om minsta behörighet när du tillhandahåller anslutningssträngar för händelsekälla. Konfigurera Event Hubs princip för delad åtkomst endast  med skicka anspråket och för IoT Hub endast *behörigheten för tjänståtkomst.*

> [!CAUTION]
> Om du tar bort din IoT Hub eller händelsehubb och skapar en ny resurs med samma namn måste du skapa en ny händelsekälla och koppla den nya IoT Hub eller händelsehubben. Data matas inte in förrän du har slutfört det här steget.

## <a name="production-workloads"></a>Produktionsarbetsbelastningar

Förutom metodtipsen ovan rekommenderar vi att du implementerar följande för affärskritiska arbetsbelastningar.

- Öka din IoT Hub eller Event Hub-datalagringstid till högst sju dagar.

- Skapa miljöaviseringar i Azure Portal. Med aviseringar baserade på [plattformsmått](./how-to-monitor-tsi-reference.md#metrics) kan du verifiera pipelinebeteendet från slutet till slut. Anvisningarna för att skapa och hantera aviseringar finns [här.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Föreslagna aviseringsvillkor:

  - IngressReceivedMessagesTimeLag är större än 5 minuter
  - IngressReceivedBytes är 0
- Håll inmatningsbelastningen balanserad mellan dina IoT Hub partitioner eller Event Hub-partitioner.

### <a name="historical-data-ingestion"></a>Historisk datainmatning

Användning av strömningspipelinen för att importera historiska data stöds för närvarande inte i Azure Time Series Insights Gen2. Om du behöver importera tidigare data till din miljö följer du riktlinjerna nedan:

- Strömma inte livedata och historiska data parallellt. Om data matas in i ordningsföljd resulterar det i försämrad frågeprestanda.
- Mata in historiska data i tidsbeställda sätt för bästa prestanda.
- Håll dig inom dataflödesbegränsningarna för datainmatning nedan.
- Inaktivera varmlagring om data är äldre än kvarhållningsperioden för varmlagring.

## <a name="event-source-timestamp"></a>Tidsstämpel för händelsekälla

När du konfigurerar en händelsekälla uppmanas du att ange en egenskap för tidsstämpel-ID. Tidsstämpelegenskapen används för att spåra händelser över tid. Det är den tid som används som tidsstämpel i fråge-API:erna och för att rita serier `$ts` i Azure Time Series Insights Explorer. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Om ingen egenskap anges när den skapas, eller om tidsstämpelegenskapen saknas i en händelse, används händelsens IoT Hub- eller Event Hubs-queued-tid som standard. Egenskapsvärden för tidsstämpel lagras i UTC.

I allmänhet väljer användarna att anpassa egenskapen för tidsstämpeln och använda den tid då sensorn eller taggen genererade läsningen i stället för att använda standardvärdet för hubbensqueued-tid. Detta är särskilt nödvändigt när enheter har tillfälliga anslutningsförluster och en batch med fördröjda meddelanden vidarebefordras till Azure Time Series Insights Gen2.

Om din anpassade tidsstämpel finns i ett kapslat JSON-objekt eller en matris måste du ange rätt egenskapsnamn efter våra [namngivningskonventioner](concepts-json-flattening-escaping-rules.md)för utplattning och undantag. Till exempel ska tidsstämpeln för händelsekällan [](concepts-json-flattening-escaping-rules.md#example-a) för JSON-nyttolasten som visas här anges som `"values.time"` .

### <a name="time-zone-offsets"></a>Tidszonsförskjutningar

Tidsstämplar måste skickas i ISO 8601-format och lagras i UTC. Om en tidszonsförskjutning anges tillämpas förskjutningen och sedan den tid som lagras och returneras i UTC-format. Om förskjutningen är felaktigt formaterad ignoreras den. I situationer där din lösning kanske inte har kontexten för den ursprungliga förskjutningen kan du skicka förskjutningsdata i ytterligare en separat händelseegenskap för att säkerställa att de bevaras och att programmet kan referera till i ett frågesvar.

Tidszonsförskjutningen ska formateras som något av följande:

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>Nästa steg

- Läs [reglerna för JSON-utplattning och undantag](./concepts-json-flattening-escaping-rules.md) för att förstå hur händelser kommer att lagras.

- Förstå miljöns [dataflödesbegränsningar](./concepts-streaming-ingress-throughput-limits.md)
