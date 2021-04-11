---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288580"
---
## <a name="business-disaster-recovery"></a>Katastrof återställning för företag

I det här avsnittet beskrivs funktioner i Azure Time Series Insights som håller appar och tjänster som körs, även om en katastrof inträffar (kallas även för *verksamhets haveri beredskap*).

### <a name="high-availability"></a>Hög tillgänglighet

Som Azure-tjänst tillhandahåller Azure Time Series Insights vissa funktioner för *hög tillgänglighet* genom att använda redundans på region nivån i Azure. Azure stöder exempelvis haveri beredskap via Azures *tillgänglighets funktion över flera regioner* .

Ytterligare funktioner med hög tillgänglighet tillhandahålls via Azure (och även tillgängligt för valfri Azure Time Series Insights instans):

- **Redundans**: Azure tillhandahåller [geo-replikering och belastnings utjämning](/azure/architecture/resiliency/recovery-loss-azure-region).
- **Data återställning** och **lagrings återställning**: Azure tillhandahåller [flera alternativ för att bevara och återställa data](/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure tillhandahåller återställnings funktioner via [Azure Site Recovery](../articles/site-recovery/index.yml).
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md) stöder både lokal och molnad säkerhets kopiering av virtuella Azure-datorer.

Se till att du aktiverar de relevanta Azure-funktionerna för att tillhandahålla global tillgänglighet över flera regioner för dina enheter och användare.

> [!NOTE]
> Om Azure är konfigurerat för att aktivera tillgänglighet mellan regioner krävs ingen ytterligare tillgänglighets konfiguration mellan regioner i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT-och event-hubbar

Vissa Azure IoT-tjänster omfattar även inbyggda funktioner för verksamhets haveri beredskap:

- [Azure IoT Hub haveri beredskap med hög tillgänglighet](../articles/iot-hub/iot-hub-ha-dr.md), vilket omfattar redundans inom regioner
- [Azure Event Hubs-principer](../articles/event-hubs/event-hubs-geo-dr.md)
- [Redundans i Azure Storage](../articles/storage/common/storage-redundancy.md)

Att integrera Azure Time Series Insights med de andra tjänsterna ger ytterligare möjligheter till haveri beredskap. Till exempel kan telemetri som skickas till händelsehubben vara bestående av en säkerhets kopia av Azure Blob Storage-databasen.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Det finns flera sätt att se till att dina Azure Time Series Insights data, appar och tjänster körs, även om de är störda.

Du kan dock bestämma att en fullständig säkerhets kopia av din Azure Time Series-miljö också krävs, i följande syfte:

- Som en *redundansväxling* särskilt för Azure Time Series Insights att omdirigera data och trafik till
- Bevara data och gransknings information

I allmänhet är det bästa sättet att duplicera en Azure Time Series Insights miljö att skapa en andra Azure Time Series Insights-miljö i en Azure Backup-region. Händelser skickas också till den sekundära miljön från den primära händelse källan. Se till att du använder en andra dedikerad konsument grupp. Följ dessa rikt linjer för affärs haveri beredskap för källan, enligt beskrivningen ovan.

Så här skapar du en duplicerad miljö:

1. Skapa en miljö i en andra region. Mer information finns [i skapa en ny Azure Time Series Insights-miljö i Azure Portal](../articles/time-series-insights/time-series-insights-get-started.md).
1. Skapa en andra dedikerad konsument grupp för din händelse källa.
1. Anslut händelse källan till den nya miljön. Se till att du anger den andra dedikerade konsument gruppen.
1. Läs dokumentationen om Azure Time Series Insights [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) och [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) .

Om en händelse inträffar:

1. Om din primära region påverkas under en katastrof incident, dirigerar du om åtgärder till säkerhets kopierings Azure Time Series Insightss miljön.
1. Eftersom nummer för Hubbs ordning startar om från 0 efter redundansväxlingen, återskapar du händelse källan i båda regionerna/miljöerna med olika konsument grupper för att undvika att skapa vad som skulle se ut som dubbletter av händelser.
1. Använd din andra region för att säkerhetskopiera och återställa alla Azure Time Series Insights telemetri och fråga efter data.

> [!IMPORTANT]
> Om en redundansväxling inträffar:
>
> - En fördröjning kan också uppstå.
> - En tillfällig insamling i meddelande bearbetningen kan inträffa, eftersom åtgärder dirigeras om.
>
> Mer information finns i avsnittet [minska svars tiden i Azure Time Series Insights](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).