---
title: Nyheter Information – Azure Event Grid
description: Lär dig vad som är Azure Event Grid, till exempel den senaste viktiga informationen, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: f135b25f28002c037dd24fa0cb3bb7476a06309f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389595"
---
# <a name="whats-new-in-azure-event-grid"></a>Vad är nytt i Azure Event Grid?

>Få ett meddelande om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL:en: i `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` ![ rss-feedens ](./media/whats-new/feed-icon-16x16.png) läsare-läsare.

Azure Event Grid får kontinuerligt förbättringar. Den här artikeln innehåller information om hur du håller dig uppdaterad med den senaste utvecklingen:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

## <a name="610-preview-2020-10"></a>6.1.0-preview (2020-10)
- [Hanterade identiteter för systemämnen](enable-identity-system-topics.md)
- [Anpassade leveransegenskaper](delivery-properties.md)
- [Lagringskö – TTL -meddelande (Time To Live)](delivery-properties.md#configure-time-to-live-on-outgoing-events-to-azure-storage-queues)
- [Avancerade filtreringsförbättringar](event-filtering.md#advanced-filtering)
    - Stöd för filtrering av matrisdata i inkommande händelser
    - Tillåt filtrering av kontextattribut för CloudEvents-tillägg
    - Nya operatorer
        - StringNotContains
        - StringNotBeginsWith
        - StringNotEndsWith
        - NumberInRange
        - NumberNotInRange
        - Ärnull
        - IsNotNull
- [Tillåt Event Grid schema till CloudEvents 1.0-schematransformationer för anpassade ämnen och domäner](cloudevents-schema.md#configure-event-grid-for-cloudevents)
        

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Lägg till stöd för ny allmänt tillgänglig (GA) tjänst-API version 2020-06-01.
- De nya funktionerna som blev tillgängliga för alla:
    - [Indatamappningar](input-mappings.md)
    - [Anpassat schema för indata](input-mappings.md)
    - [V10-schema för molnhändelse](cloud-event-schema.md)
    - [Service Bus ämne som mål](handler-service-bus.md)
    - [Azure-funktion som mål](handler-functions.md)
    - [Webhook-batchbearbetning](./edge/delivery-output-batching.md)
    - [Säker webhook (Azure Active Directory stöd)](secure-webhook-delivery.md)
    - [IP-filtrering](configure-firewall.md)
    - [Private Link Service-support](configure-private-endpoints.md)
    - [Schema för händelseleverans](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Den här versionen innehåller ytterligare felkorrigeringar för att förbättra kvaliteten.
- Som version 5.3.1-preview motsvarar den här versionen API-versionen 2020-04-01-Preview, som innehåller följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [Systemämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelseleverans med hanterad tjänstidentitet](managed-service-identity.md) 
    - [Private Link servicesupport](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Den här versionen innehåller olika felkorrigeringar för att förbättra kvaliteten.
- Som version 5.3.0-preview motsvarar den här versionen API-versionen 2020-04-01-Preview, som innehåller följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [Systemämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelseleverans med hanterad tjänstidentitet](managed-service-identity.md) 
    - [Private Link servicesupport](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- Vi introducerar nya funktioner ovanpå funktioner som redan har lagts till i version 5.2.0-preview. 
- Som version 5.2.0-preview motsvarar den här versionen API-versionen 2020-04-01-Preview.
- Den lägger till stöd för följande nya funktioner: 
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)
    - [Partnerämnen](./partner-events-overview.md)
    - [Systemämnen som spårade resurser i Azure Portal](system-topics.md)
    - [Händelseleverans med hanterad tjänstidentitet](managed-service-identity.md) 
    - [Private Link servicesupport](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Den här versionen motsvarar API-versionen 2020-04-01-Preview.
- Den lägger till stöd för följande nya funktioner:
    - [Stöd för IP-filtrering vid publicering av händelser till domäner och ämnen](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Den här versionen motsvarar `2019-06-01` API-versionen.
- Den lägger till stöd för följande nya funktioner:
    * [Domäner](event-domains.md)
    * Sidnumrering och sökfilter för åtgärder i resurslistan. Ett exempel finns i Avsnitt [– Lista efter prenumeration.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Service Bus kö som mål](handler-service-bus.md)
    * [Avancerad filtrering](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Den här versionen motsvarar API-versionen 2019-02-01-preview.
- Den lägger till stöd för följande nya funktioner:
    * Sidnumrering och sökfilter för åtgärder i resurslistan. Ett exempel finns i Avsnitt [– Lista efter prenumeration.](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription)
    * [Manuell registrering/borttagning av domänämnen](how-to-event-domains.md)
    * [Service Bus kö som mål](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Den här versionen motsvarar den `2019-01-01` stabila API-versionen.
- Den stöder allmän tillgänglighet (GA) för följande funktioner som rör händelseprenumerationer:
    * [Mål för dead letter](manage-event-delivery.md)
    * [Azure Storage kö som mål](handler-storage-queues.md)
    * [Azure Relay – Hybridanslutning som mål](handler-relay-hybrid-connections.md)
    * [Manuell handskakningsverifiering](webhook-event-delivery.md)
    * [Stöd för återförsöksprinciper](delivery-and-retry.md)
- Funktioner som fortfarande är i förhandsversion (till [](event-filtering.md#advanced-filtering) exempel [Event Grid-domäner](event-domains.md) eller stöd för avancerade filter kan fortfarande nås med hjälp av 3.0.1-förhandsversionen av SDK."

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Beroende på [version 10.0.3 av Newtonsoft NuGet-paketet](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Den här versionen är en förhandsversion av SDK för de nya funktionerna som introducerades i API-versionen 2018-09-15-preview. – Den här versionen innehåller stöd för:
    - [Ämnen om domäner och domäner](event-domains.md)
    - Introduktion [till förfallodatum för händelseprenumeration](concepts.md#event-subscription-expiration)
    - Introduktion [till avancerad filtrering](event-filtering.md#advanced-filtering) för händelseprenumerationer
    - Den stabila versionen av SDK:n som är inriktad `2018-01-01` på API-versionen finns kvar som version 1.3.0

## <a name="next-steps"></a>Nästa steg