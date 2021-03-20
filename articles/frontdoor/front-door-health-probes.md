---
title: Azure front dörr – hälso övervakning för Server delen | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azures front dörr övervakar hälso tillståndet för dina arbets ändar
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449245"
---
# <a name="health-probes"></a>Hälsotillståndsavsökningar

För att fastställa hälso tillståndet och närheten av varje server del för en specifik front dörr miljö skickar varje front dörrs miljö regelbundet en syntetisk HTTP/HTTPS-begäran till var och en av dina konfigurerade Server delar. Front dörren använder sedan dessa svar från avsökningen för att fastställa de "bästa" Server dels resurserna för att dirigera dina klient förfrågningar. 

> [!WARNING]
> Eftersom front dörren har många gräns miljöer globalt, kan hälso avsöknings volymen för dina Server delar vara ganska hög från 25 förfrågningar varje minut till så hög som 1200 begär Anden per minut, beroende på den angivna hälso avsöknings frekvensen. Med standard avsöknings frekvensen på 30 sekunder bör avsöknings volymen på Server delen vara cirka 200 förfrågningar per minut.

## <a name="supported-protocols"></a>Protokoll som stöds

Front dörr stöder sändning av avsökningar via antingen HTTP-eller HTTPS-protokoll. Dessa avsökningar skickas över samma TCP-portar som konfigurerats för att dirigera klientbegäranden och kan inte åsidosättas.

## <a name="supported-http-methods-for-health-probes"></a>HTTP-metoder som stöds för hälso avsökningar

Frontend-dörren stöder följande HTTP-metoder för att skicka hälso avsökningar:

1. **Hämta:** GET-metoden innebär att hämta vilken information (i form av en entitet) som identifieras av URI: n för begäran.
2. **Huvud:** HEAD-metoden är identisk med GET, förutom att servern inte får returnera en meddelande text i svaret. För nya profiler för front dörren är avsöknings metoden som standard inställd som huvud.

> [!NOTE]
> För att minska belastningen och kostnaden på dina Server delar rekommenderar front dörren att använda HEAD-begäranden för hälso avsökningar.

## <a name="health-probe-responses"></a>Svar på hälso avsökning

| Svar  | Beskrivning | 
| ------------- | ------------- |
| Fastställa hälsa  |  En status på 200 OK anger att Server delen är felfri. Allt annat anses vara ett haveri. Om ett giltigt HTTP-svar inte tas emot för en avsökning räknas inte avsökningen som ett fel (inklusive nätverks fel).|
| Mäta svars tid  | Svars tiden är den tid i väggen som mäts från tiden omedelbart innan vi skickar en avsöknings förfrågan till den tidpunkt då vi får de sista byten av svaret. Vi använder en ny TCP-anslutning för varje begäran, så denna mätning är inte prioriterad mot Server delar med befintliga varma anslutningar.  |

## <a name="how-front-door-determines-backend-health"></a>Hur front dörren avgör Server dels hälsa

Azures front dörr använder samma tre stegs process nedan för alla algoritmer för att fastställa hälso tillståndet.

1. Uteslut inaktiverade Server delar.

2. Uteslut Server delar med hälso avsöknings fel:
    * Valet görs genom att titta på de senaste _n_ hälso avsöknings svaren. Om minst _x_ är felfritt betraktas Server delen som felfri.

    * _n_ konfigureras genom att ändra egenskapen SampleSize i inställningarna för belastnings utjämning.

    * _x_ konfigureras genom att ändra egenskapen SuccessfulSamplesRequired i inställningarna för belastnings utjämning.

3. För uppsättningarna med felfria Server delar i backend-poolen, mäter front dörren också och bibehåller svars tiden (fördröjnings tid) för varje server del.


## <a name="complete-health-probe-failure"></a>Slutför hälso avsöknings fel

Om hälso avsökningar inte kan utföras för varje server del i en backend-pool, anser front-dörren att alla backar är felfria och dirigerar trafik i en Round Robin-distribution över alla.

När en server del återgår till ett felfritt tillstånd, återupptar front dörren den normala belastnings Utjämnings algoritmen.

## <a name="disabling-health-probes"></a>Inaktivera hälso avsökningar

Om du har en enda server del i din backend-pool kan du välja att inaktivera hälso avsökningar som minskar belastningen på din program Server del. Även om du har flera Server delar i backend-poolen men bara en av dem är i aktiverat läge, kan du inaktivera hälso avsökningar.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
