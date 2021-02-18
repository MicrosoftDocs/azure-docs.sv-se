---
title: Azure front dörr standard/Premium (för hands version) övervakning av hälso avsökning
description: Den här artikeln hjälper dig att förstå hur Azures front dörr övervakar hälso tillståndet för din server del.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100533"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure front dörr standard/Premium (för hands version) övervakning av hälso avsökning

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azures front dörr skickar regelbundet en HTTP-eller HTTPS-begäran till var och en av din server del. Dessa begär Anden tillåter Azure-frontend att fastställa hälso tillståndet för varje slut punkt i backend-poolen. Front dörren använder sedan dessa svar från avsökningen för att fastställa de "bästa" Server dels resurserna för att dirigera dina klient förfrågningar. 

> [!WARNING]
> Eftersom front dörren har många gräns miljöer globalt, kan hälso avsöknings volymen för dina Server delar vara ganska hög från 25 förfrågningar varje minut till så hög som 1200 begär Anden per minut, beroende på den angivna hälso avsöknings frekvensen. Med standard avsöknings frekvensen på 30 sekunder bör avsöknings volymen på Server delen vara cirka 200 förfrågningar per minut.

## <a name="supported-protocols"></a>Protokoll som stöds

Front dörr stöder sändning av avsökningar via antingen HTTP-eller HTTPS-protokoll. Dessa avsökningar skickas över samma TCP-portar som konfigurerats för att dirigera klientbegäranden och kan inte åsidosättas.

## <a name="supported-http-methods-for-health-probes"></a>HTTP-metoder som stöds för hälso avsökningar

Frontend-dörren stöder följande HTTP-metoder för att skicka hälso avsökningar:

* **Hämta:** GET-metoden innebär att hämta vilken information (i form av en entitet) som identifieras av URI: n för begäran.
* **Huvud:** HEAD-metoden är identisk med GET, förutom att servern inte får returnera en meddelande text i svaret. För nya profiler för front dörren är avsöknings metoden som standard inställd som huvud.

> [!NOTE]
> För att minska belastningen och kostnaden på dina Server delar rekommenderar front dörren att använda HEAD-begäranden för hälso avsökningar.

## <a name="health-probe-responses"></a>Svar på hälso avsökning

| Svar  | Description | 
| ------------- | ------------- |
| Fastställa hälsa  |  En status på 200 OK anger att Server delen är felfri. Allt annat anses vara ett haveri. Om ett giltigt HTTP-svar inte tas emot för en avsökning räknas inte avsökningen som ett fel (inklusive nätverks fel).|
| Mäta svars tid  | Svars tiden är den tid i väggen som mäts från tiden omedelbart innan vi skickar en avsöknings förfrågan till den tidpunkt då vi får de sista byten av svaret. Vi använder en ny TCP-anslutning för varje begäran, så denna mätning är inte prioriterad mot Server delar med befintliga varma anslutningar.  |

## <a name="how-front-door-determines-backend-health"></a>Hur front dörren avgör Server dels hälsa

Azures front dörr använder samma tre stegs process nedan för alla algoritmer för att fastställa hälso tillståndet.

1. Uteslut inaktiverade Server delar.

1. Uteslut Server delar med hälso avsöknings fel:

    * Valet görs genom att titta på de senaste _n_ hälso avsöknings svaren. Om minst _x_ är felfritt betraktas Server delen som felfri.

    * _n_ konfigureras genom att ändra egenskapen SampleSize i inställningarna för belastnings utjämning.

    * _x_ konfigureras genom att ändra egenskapen SuccessfulSamplesRequired i inställningarna för belastnings utjämning.

1. För uppsättningarna med felfria Server delar i backend-poolen, mäter front dörren också och bibehåller svars tiden (fördröjnings tid) för varje server del.


## <a name="complete-health-probe-failure"></a>Slutför hälso avsöknings fel

Om hälso avsökningar inte kan utföras för varje server del i en backend-pool, anser front-dörren att alla backar är felfria och dirigerar trafik i en Round Robin-distribution över alla.

När en server del återgår till ett felfritt tillstånd, återupptar front dörren den normala belastnings Utjämnings algoritmen.

## <a name="disabling-health-probes"></a>Inaktivera hälso avsökningar

Om du har en enda server del i din backend-pool eller bara en server del är aktiv i en backend-pool kan du välja att inaktivera hälso avsökningarna. På så sätt kan du minska belastningen på din program Server del.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
