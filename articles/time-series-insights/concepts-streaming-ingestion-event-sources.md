---
title: Strömmande inmatnings händelse källor – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om strömmande data till Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504404"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights händelse källor för Gen2

Din Azure Time Series Insights Gen2-miljö kan ha upp till två händelse källor för strömning. Två typer av Azure-resurser stöds som indata:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Händelser måste skickas som UTF-8-kodad JSON.

## <a name="create-or-edit-event-sources"></a>Skapa eller redigera händelse källor

Händelse källan är länken mellan hubben och din Azure Time Series Insights Gen2-miljö och en separat resurs av typen `Time Series Insights event source` skapas i din resurs grupp. IoT Hub-eller Händelsehubben-resurser kan finnas i samma Azure-prenumeration som din Azure Time Series Insights Gen2-miljö eller en annan prenumeration. Det är dock en bra idé att House din Azure Time Series Insights-miljö och IoT Hub eller Händelsehubben i samma Azure-region.

Du kan använda [Azure Portal](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [Azure Resource Manager mallar](time-series-insights-manage-resources-using-azure-resource-manager-template.md)och [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) för att skapa, redigera eller ta bort din miljös händelse källor.

## <a name="start-options"></a>Start alternativ

När du skapar en händelse källa har du möjlighet att ange vilka tidigare befintliga data som ska samlas in. Den här inställningen är valfri. Följande alternativ är tillgängliga:

| Name   |  Beskrivning  |  Exempel på Azure Resource Manager mall |
|----------|-------------|------|
| EarliestAvailable | Mata in alla redan befintliga data som lagras i IoT-eller Händelsehubben | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Börja mata in data som kommer efter att händelse källan har skapats. Alla tidigare data som strömmas innan händelse källan skapades ignoreras. Detta är standardinställningen i Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Din miljö kommer att mata in data från din anpassade köade tid (UTC). Alla händelser som står i kö i IoT-eller Händelsehubben vid eller efter den anpassade tids perioden kommer att matas in och lagras. Alla händelser som anlänt innan din anpassade tid kommer att ignoreras. Observera att "den köade tiden" avser den tid (i UTC) som händelsen anlänt i IoT-eller Händelsehubben. Detta skiljer sig från en anpassad [timestamp-egenskap](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) som ligger inom bröd texten för din händelse. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Om du väljer EarliestAvailable och har massor av redan befintliga data kan du få hög första svars tid eftersom din Azure Time Series Insights Gen2-miljö bearbetar alla dina data.
> - Den här långa fördröjningen bör slutligen ligga under den tid då data indexeras. Skicka in ett support ärende via Azure Portal om du får en kontinuerlig hög latens.

* EarliestAvailable

![EarliestAvailable-diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![EventSourceCreationTime-diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![CustomEnqueuedTime-diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Metod tips för strömning

- Skapa alltid en unik konsument grupp för din Azure Time Series Insights Gen2-miljö för att använda data från din händelse källa. Att återanvända konsument grupper kan orsaka slumpmässiga från kopplingar och kan leda till data förlust.

- Konfigurera din Azure Time Series Insights Gen2-miljö och din IoT Hub och/eller Event Hubs i samma Azure-region. Även om det är möjligt att konfigurera en händelse källa i en separat region, stöds inte det här scenariot och vi kan inte garantera hög tillgänglighet.

- Gå inte utöver din miljös gräns för [data flödes hastighet](./concepts-streaming-ingress-throughput-limits.md) eller per partition.

- Konfigurera en fördröjnings [avisering](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) för att bli meddelad om din miljö har problem med att bearbeta data. Se [produktions arbets belastningar](./concepts-streaming-ingestion-event-sources.md#production-workloads) nedan för föreslagna aviserings villkor.

- Använd strömnings inmatning för nästan endast real tids data och senaste data, så stöds inte direkt uppspelning av historiska data.

- Förstå hur egenskaper kommer att utjämnas och JSON- [data ska utplattas och lagras.](./concepts-json-flattening-escaping-rules.md)

- Följ principen om minsta behörighet när du tillhandahåller anslutnings strängar för händelse källan. För Event Hubs konfigurerar du en princip för delad åtkomst med endast *Skicka* anspråk och för IoT Hub använda endast *tjänst anslutnings* behörighet.

> [!CAUTION]
> Om du tar bort din IoT Hub eller Händelsehubben och återskapar en ny resurs med samma namn måste du skapa en ny händelse källa och koppla den nya IoT Hub eller Händelsehubben. Data kommer inte att matas in förrän du har slutfört det här steget.

## <a name="production-workloads"></a>Produktionsarbetsbelastningar

Utöver de bästa metoderna ovan rekommenderar vi att du implementerar följande för affärs kritiska arbets belastningar.

- Öka data lagrings tiden för IoT Hub-eller Händelsehubben till högst sju dagar.

- Skapa miljö varningar i Azure Portal. Med aviseringar som baseras på plattforms [mått](./how-to-monitor-tsi-reference.md#metrics) kan du validera pipeline-beteendet från slut punkt till slut punkt. Anvisningar för att skapa och hantera aviseringar finns [här](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Föreslagna aviserings villkor:

  - IngressReceivedMessagesTimeLag är större än 5 minuter
  - IngressReceivedBytes är 0
- Se till att din inmatnings belastning fördelas mellan IoT Hub-eller Händelsehubben-partitioner.

### <a name="historical-data-ingestion"></a>Historisk data inmatning

Det finns för närvarande inte stöd för att använda strömnings pipelinen för att importera historiska data i Azure Time Series Insights Gen2. Om du behöver importera tidigare data till din miljö följer du rikt linjerna nedan:

- Strömma inte Live och historiska data parallellt. Om du matar ut data från varandra kommer du att leda till försämrade frågor.
- Mata in historiska data i tidsordnad tid för bästa prestanda.
- Håll koll på gräns värdena för inmatnings flödet nedan.
- Inaktivera varmt Arkiv om data är äldre än lagrings perioden för din varma lagrings tid.

## <a name="event-source-timestamp"></a>Tids stämpling för händelse källa

När du konfigurerar en händelse källa uppmanas du att ange en egenskap för timestamp-ID. Egenskapen timestamp används för att spåra händelser över tid, detta är den tid som ska användas som tidstämpel `$ts` i [fråge-API: erna](/rest/api/time-series-insights/dataaccessgen2/query/execute) och för att rita serier i Azure Time Series Insights Explorer. Om ingen egenskap anges när du skapar, eller om egenskapen timestamp saknas i en händelse, används händelsens IoT Hub-eller Event Hubs-hubbar som standard. Tidsstämpelns egenskaps värden lagras i UTC.

I allmänhet väljer användare att anpassa egenskapen timestamp och använder tiden när sensorn eller taggen genererar läsningen istället för att använda standard navets köade tid. Detta är särskilt nödvändigt när enheter har tillfälligt anslutnings avbrott och en batch med fördröjda meddelanden vidarebefordras till Azure Time Series Insights Gen2.

Om din anpassade tidsstämpel är inom ett kapslat JSON-objekt eller en matris måste du ange rätt egenskaps namn efter vår [förenkling och undantag av namngivnings konventioner](concepts-json-flattening-escaping-rules.md). Till exempel ska händelse källans tidstämpel för den JSON-nyttolast som visas [här](concepts-json-flattening-escaping-rules.md#example-a) anges som `"values.time"` .

### <a name="time-zone-offsets"></a>Tids zons förskjutningar

Tidsstämplar måste skickas i ISO 8601-format och lagras i UTC. Om en tids zons förskjutning anges så används förskjutningen och sedan den tid som lagras och returneras i UTC-format. Om förskjutningen är felaktigt formaterad kommer den att ignoreras. I situationer där din lösning kanske inte har kontext för den ursprungliga förskjutningen kan du skicka förskjutnings data i en ytterligare separat händelse egenskap för att säkerställa att den bevaras och att programmet kan referera till ett fråge svar.

Tids zonens förskjutning ska formateras som något av följande:

± HHMMZ</br>
± HH: MM</br>
± HH: MMZ</br>

## <a name="next-steps"></a>Nästa steg

- Läs om hur du kommer att lagra händelser genom att läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md) .

- Förstå din miljös [data flödes begränsningar](./concepts-streaming-ingress-throughput-limits.md)
