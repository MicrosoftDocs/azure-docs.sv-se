---
title: Nyheter Viktig information – Azure Event Grid
description: Lär dig mer om vad som är nytt med Azure Event Grid, till exempel senaste versions information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 7528cef7007704bbf434f16622707da8920eec2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350015"
---
# <a name="whats-new-in-azure-event-grid"></a>Vad är nytt i Azure Event Grid?

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` i din ![ RSS feed reader-ikon ](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure Event Grid tar emot förbättringar regelbundet. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Lägg till stöd för den nya allmänt tillgängliga Service API-versionen 2020-06-01.
- De nya funktionerna som blev GA:
    - [Mappningar för indatamängd](input-mappings.md)
    - [Anpassat indatamängds schema](input-mappings.md)
    - [Moln Event v10-schema](cloud-event-schema.md)
    - [Service Bus ämnet som mål](handler-service-bus.md)
    - [Azure Function som mål](handler-functions.md)
    - [Webhook-batch](./edge/delivery-output-batching.md)
    - [Säker webhook (Azure Active Directory support)](secure-webhook-delivery.md)
    - [IP-filtrering](configure-firewall.md)
    - [Stöd för privata länk tjänster](configure-private-endpoints.md)
    - [Schema för händelse leverans](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-för hands version (2020-05)
- Den här versionen innehåller ytterligare fel korrigeringar för bättre kvalitet.
- Som version 5.3.1 – för hands version motsvarar den här versionen 2020-04-01-Preview API-versionen, som innehåller följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [System ämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelse leverans med hanterad tjänst identitet](managed-service-identity.md) 
    - [Stöd för privata länk tjänster](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-för hands version (2020-04)
- Den här versionen innehåller flera fel korrigeringar för att förbättra kvaliteten.
- Som version 5.3.0 – för hands version motsvarar den här versionen 2020-04-01-Preview API-versionen, som innehåller följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [System ämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelse leverans med hanterad tjänst identitet](managed-service-identity.md) 
    - [Stöd för privata länk tjänster](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0 – för hands version (2020-03)
- Vi introducerar nya funktioner ovanpå funktioner som redan har lagts till i version 5.2.0 – Preview. 
- Som version 5.2.0 – för hands version motsvarar den här versionen 2020-04-01-Preview API-versionen.
- Den lägger till stöd för följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [System ämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelse leverans med hanterad tjänst identitet](managed-service-identity.md) 
    - [Stöd för privata länk tjänster](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0 – för hands version (2020-01)
- Den här versionen motsvarar API-versionen 2020-04-01-Preview.
- Den lägger till stöd för följande nya funktioner:
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Den här versionen motsvarar API- `2019-06-01` versionen.
- Den lägger till stöd för följande nya funktioner:
    * [Domäner](event-domains.md)
    * Sid brytning och Sök filter för åtgärder i resurs listan. Ett exempel finns i [avsnitten-lista efter prenumeration](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Service Bus kö som mål](handler-service-bus.md)
    * [Avancerad filtrering](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0 – för hands version (2019-03)
- Den här versionen motsvarar API-versionen 2019-02-01-Preview.
- Den lägger till stöd för följande nya funktioner:
    * Sid brytning och Sök filter för åtgärder i resurs listan. Ett exempel finns i [avsnitten-lista efter prenumeration](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Manuellt skapande/borttagning av domän ämnen](how-to-event-domains.md)
    * [Service Bus kö som mål](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Den här versionen motsvarar den `2019-01-01` stabila API-versionen.
- Den stöder allmän tillgänglighet (GA) av följande funktioner relaterade till händelse prenumerationer:
    * [Mål för obeställbara meddelanden](manage-event-delivery.md)
    * [Azure Storage kö som mål](handler-storage-queues.md)
    * [Azure Relay hybrid anslutning som mål](handler-relay-hybrid-connections.md)
    * [Manuell verifiering av hand skakning](webhook-event-delivery.md)
    * [Stöd för principer för återförsök](delivery-and-retry.md)
- Funktioner som fortfarande är i förhands granskning (till exempel [Event Grid domäner](event-domains.md) eller [Avancerade filter](event-filtering.md#advanced-filtering) kan fortfarande nås med hjälp av 3.0.1-Preview-versionen av SDK: n. "

## <a name="301-preview-2018-10"></a>3.0.1 – för hands version (2018-10)
- Tar beroende av [10.0.3-versionen av Newtonsoft NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0 – för hands version (2018-10)
- Den här versionen är en för hands version av SDK för de nya funktionerna som introduceras i 2018-09-15-Preview API-versionen. – Den här versionen innehåller stöd för:
    - [Avsnitt om domäner och domäner](event-domains.md)
    - Introducerar [förfallo datum för händelse prenumeration](concepts.md#event-subscription-expiration)
    - Introduktion till [avancerad filtrering](event-filtering.md#advanced-filtering) för händelse prenumerationer
    - Den stabila versionen av SDK som riktar sig mot `2018-01-01` API-versionen fortsätter att finnas som version 1.3.0

## <a name="next-steps"></a>Nästa steg