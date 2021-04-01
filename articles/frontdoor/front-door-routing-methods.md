---
title: Metoder för trafikroutning i Azures front dörr | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de olika metoder för trafikroutning som används av front dörren
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449144"
---
# <a name="front-door-routing-methods"></a>Metoder för routning av front dörr

Azures front dörr stöder olika typer av metoder för trafik cirkulation för att fastställa hur din HTTP/HTTPS-trafik ska dirigeras till olika tjänst slut punkter. När klienten begär en front dörr, tillämpas den konfigurerade routningsmetod för att se till att begär Anden vidarebefordras till den bästa backend-instansen. 

Det finns fyra metoder för trafikroutning som är tillgängliga i front dörren:

* **[Svars tid](#latency):** Latens-baserad routning ser till att begär Anden skickas till de lägsta svars tiderna som accepteras inom ett känslighets intervall. I princip skickas dina användar förfrågningar till "närmsta" uppsättning Server delar i förhållande till nätverks fördröjningen.
* **[Prioritet](#priority):** Du kan tilldela prioriteter till dina Server delar när du vill konfigurera en primär server del för att betjäna all trafik. Den sekundära Server delen kan vara en säkerhets kopia om den primära server delen inte är tillgänglig.
* **[Viktat](#weighted):** Du kan tilldela vikter till dina Server delar när du vill distribuera trafik över en uppsättning Server delar. Om du vill fördela eller enligt vikt koefficienter jämnt.
* **[Tillhörighet mellan sessioner](#affinity):** Du kan konfigurera sessionens tillhörighet för klient dels värdarna eller domänerna för att se till att förfrågningar från samma användare skickas till samma server del.

I alla Front Door-konfigurationer ingår övervakning av hälsotillståndet på serverdelen och automatiserad omedelbar global redundans. Mer information finns i [övervakning av front dörrs Server](front-door-health-probes.md)del. Din front dörr kan fungera baserat på en enda routningsmetod. Beroende på dina program behov kan du också kombinera flera metoder för routning för att bygga en optimal crawltopologi.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Lägsta latens baserad trafik routning

Distribution av Server delar på två eller flera platser över hela världen kan förbättra svars tiden för dina program genom att dirigera trafik till målet som är "närmast" till dina slutanvändare. Standard metoden för trafikroutning för din frontend-konfiguration vidarebefordrar begär Anden från slutanvändarna till den närmaste server delen av den främre dörren som tog emot begäran. I kombination med anycast-arkitekturen i Azures frontend, ser den här metoden till att alla slutanvändare får bästa möjliga prestanda baserat på deras plats.

Server delen "närmast" är inte nödvändigt vis närmast som uppmätt av geografiskt avstånd. I stället fastställer front dörren de närmaste server ändarna genom att mäta nätverks fördröjningen. Läs mer om [routnings arkitekturen för front dörren](front-door-routing-architecture.md). 

Nedan visas det övergripande besluts flödet:

| Tillgängliga Server delar | Prioritet | Svars tids signal (baserad på hälso avsökning) | Lekar |
|-------------| ----------- | ----------- | ----------- |
| Välj först alla Server delar som är aktiverade och returnerade felfria (200 OK) för hälso avsökningen. Om det finns sex Server delar A, B, C, D, E och F, och mellan dem C, är inte felfria och E har inaktiverats. Listan över tillgängliga Server delar är A, B, D och F.  | Därefter väljs de högsta prioritets bakändarna bland de tillgängliga. Om Server delen A, B och D har prioritet 1 och Server del F har prioriteten 2. De valda Server delarna är A, B och D.| Välj server delar med latens intervall (minsta latens & fördröjning i MS angivet). Om Server del A är 15 MS, B är 30 MS och D är 60 MS bort från den främre dörr miljön där begäran landats, och fördröjnings känslighet är 30 MS, så består den lägsta latens poolen av Server del A och B, eftersom D är över 30 MS bort från den närmaste server delen som är en. | Till sist kommer front dörren att runda av trafiken mellan den slutliga valda poolen av Server delar i förhållandet mellan vikter som anges. Anta att om Server delen A har en vikt på 5 och Server del B har en vikt på 8, kommer trafiken att distribueras i förhållandet 5:8 mellan server delar A och B. |

>[!NOTE]
> Som standard är egenskapen svars känslighet inställd på 0 MS, det vill säga alltid vidarebefordra begäran till den snabbast tillgängliga Server delen.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioriterad trafik-routning

En organisation vill ofta tillhandahålla hög tillgänglighet för sina tjänster genom att distribuera fler än en säkerhets kopierings tjänst, om den primära servern går nedåt. I branschen kallas den här topologin även för aktiv/vänte läge eller aktiv/passiv distributions topologi. Med Traffic-metoden Traffic-routing kan Azure-kunder enkelt implementera det här mönstret för växling vid fel.

Din standard-front dörr innehåller en lista med samma prioritet som server delar. Som standard skickar front dörren enbart trafik till Server delens högsta prioritet (lägsta värdet för prioritet) som är den primära uppsättningen med Server delar. Om de primära server delarna inte är tillgängliga dirigerar front dörren trafiken till den sekundära uppsättningen Server delar (andra lägsta värdet för prioritet). Om både den primära och den sekundära Server delen inte är tillgänglig, går trafiken till den tredje osv. Server delens tillgänglighet baseras på konfigurerad status (aktive rad eller inaktive rad) och den kontinuerliga hälso statusen för Server delen som bestäms av hälso avsökningarna.

### <a name="configuring-priority-for-backends"></a>Konfigurera prioritet för Server delar

Varje server del i din backend-pool för konfigurationen av front dörren har en egenskap som kallas Priority, som kan vara ett tal mellan 1 och 5. Med Azures front dörr konfigurerar du Server dels prioriteten uttryckligen med den här egenskapen för varje server del. Den här egenskapen är ett värde mellan 1 och 5. Lägre värden representerar en högre prioritet. Server delar kan dela prioritets värden.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Viktad metod för trafik dragning
Med metoden "viktad" för trafik dragning kan du distribuera trafik jämnt eller använda en fördefinierad viktning.

I den viktade metoden för trafik routning tilldelar du en vikt till varje server del i konfigurationen för den lokala server delen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern använder en standard vikt på "50".

Med en lista över tillgängliga Server delar som har godtagbar svars tid kan trafiken distribueras med en Round Robin-mekanism med hjälp av förhållandet mellan vikter. Om svars tiden för svars tiden anges till 0 millisekunder, börjar den här egenskapen inte gälla om det finns två server delar med samma nätverks fördröjning. 

Den viktade metoden möjliggör vissa användbara scenarier:

* **Gradvis program uppgradering**: ger en procent andel av trafiken som ska vidarebefordras till en ny server del och ökar gradvis trafiken över tid för att ta den i parivärde med andra server delar.
* **Programmigrering till Azure**: skapa en backend-pool med både Azure och externa server delar. Justera vikten på Server delarna för att föredra de nya server delarna. Du kan gradvis konfigurera detta genom att låta de nya server delarna vara inaktiverade, och sedan tilldela dem de lägsta vikterna, så att det blir långsamt att öka det till nivåer där de tar störst trafik. Sedan inaktiverar du de mindre önskade Server delarna och tar bort dem från poolen.  
* **Cloud-burst för ytterligare kapacitet**: utöka snabbt en lokal distribution till molnet genom att placera den bakom en front dörr. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler server delar och ange vilken del av trafiken som går till varje server del.

## <a name="session-affinity"></a><a name = "affinity"></a>Sessionstillhörighet
Som standard, utan tillhörighet över sessioner, vidarebefordrar frontend-dörren begär Anden från samma klient till olika Server delar. Vissa tillstånds känsliga program eller i vissa scenarier som följer begär Anden från samma användare föredrar samma server del som bearbetade den ursprungliga begäran. Cookie-baserad sessionstillhörighet är användbart om du vill behålla en användarsession på samma serverdel. Med hanterade cookies kan Azures front dörr dirigera trafik från en användarsession till samma server del för bearbetning.

Sessionstillhörighet kan aktiveras på nivån för klientdelsvärden, dvs. för var och en av dina konfigurerade domäner (eller underdomäner). När funktionen har aktiverats lägger Front Door till en cookie till användarens session. Med cookie-baserad sessionstillhörighet kan Front Door identifiera olika användare även bakom samma IP-adress, vilket i sin tur möjliggör en mer jämn fördelning av trafik mellan dina olika serverdelar.

Livslängden för cookien är samma som användarens session eftersom Front Door för närvarande endast stöder sessions-cookies. 

> [!NOTE]
> Offentliga proxyservrar kan störa tillhörighet mellan sessioner. Detta beror på att när du upprättar en session måste du lägga till en cookie för sessions-tillhörighet i svaret, som inte kan utföras om svaret är cacheable eftersom det skulle störa cookies från andra klienter som begär samma resurs. För att skydda mot detta, upprättas **ingen** mappning av sessionen om Server delen skickar ett cacheable-svar vid försöket. Om sessionen redan har etablerats spelar det ingen roll om svaret från Server delen är cacheable.
> Sessions-tillhörighet upprättas i följande fall, **om inte** svaret har en status kod för http 304:
> - Svaret har angivna värden som har angetts för ```Cache-Control``` rubriken som förhindrar cachelagring, t. ex. "privat" eller "ingen-butik".
> - Svaret innehåller ett ```Authorization``` huvud som inte har upphört att gälla.
> - Svaret har en status kod för HTTP 302.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
