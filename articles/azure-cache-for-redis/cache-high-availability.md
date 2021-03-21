---
title: Hög tillgänglighet för Azure cache för Redis
description: Lär dig mer om Azure cache för Redis funktioner och alternativ för hög tillgänglighet
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: ad6696fc4fe2af7047c25a3a9c260d3b12588ee2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203308"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hög tillgänglighet för Azure cache för Redis

Azure cache för Redis har inbyggd hög tillgänglighet. Målet med en arkitektur med hög tillgänglighet är att se till att den hanterade Redis-instansen fungerar även när de underliggande virtuella datorerna (VM) påverkas av planerade eller oplanerade avbrott. Det ger mycket högre pris taxa än vad som kan uppnås genom att vara värd för Redis på en enda virtuell dator.

Azure cache för Redis implementerar hög tillgänglighet genom att använda flera virtuella datorer, som kallas *noder*, för en cache. Den konfigurerar noderna så att datareplikering och redundans sker på koordinerade sätt. Den dirigerar också underhålls åtgärder som Redis-program uppdatering. Det finns olika alternativ för hög tillgänglighet på nivåerna standard, Premium och Enterprise:

| Alternativ | Beskrivning | Tillgänglighet | Standard | Premium | Stora företag |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standardreplikering](#standard-replication)| Replikerad konfiguration med dubbla noder i ett enda data Center med automatisk redundans | 99,9 % |✔|✔|-|
| [Zonredundans](#zone-redundancy) | Replikerad konfiguration med flera noder över AZs, med automatisk redundans | 99,95% (Premium-nivå), 99,99% (företags nivåer) |-|Förhandsgranskning|Förhandsgranskning|
| [Geo-replikering](#geo-replication) | Länkade cache-instanser i två regioner med användarspecifik redundans | 99,999% (företags nivå) |-|✔|Förhandsgranskning|

## <a name="standard-replication"></a>Standardreplikering

En Azure-cache för Redis på standard-eller Premium-nivån körs på ett par Redis-servrar som standard. De två servrarna finns på dedikerade virtuella datorer. Med Redis med öppen källkod kan endast en server hantera data skrivnings begär Anden. Den här servern är den *primära* noden, medan den andra *repliken*. När den har etablerat Server noderna tilldelar Azure cache för Redis primär-och replik roller till dem. Den primära noden ansvarar vanligt vis för att underhålla Skriv-och Läs begär Anden från Redis-klienter. Vid en Skriv åtgärd, allokerar den en ny nyckel och en nyckel uppdatering till dess interna minne och svarar omedelbart på klienten. Den vidarebefordrar åtgärden till repliken asynkront.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Installation av datareplikering":::
   
>[!NOTE]
>Normalt kommunicerar en Redis-klient med den primära noden i ett Redis-cacheminne för alla Läs-och skriv förfrågningar. Vissa redis-klienter kan konfigureras att läsa från noden replikering.
>
>

Om den primära noden i en Redis-cache inte är tillgänglig, kommer repliken att befordra sig själv för att bli den nya primära servern automatiskt. Den här processen kallas för en *redundansväxling*. Repliken väntar i tillräckligt lång tid innan den tar över om den primära noden återställs snabbt. När en redundansväxling sker etablerar Azure cache för Redis en ny virtuell dator och kopplar den till cachen som noden replik. Repliken utför en fullständig datasynkronisering med den primära så att den har en annan kopia av data i cachen.

En primär nod kan gå ur drift som en del av en planerad underhålls aktivitet, till exempel Redis program vara eller operativ Systems uppdatering. Det kan också sluta fungera på grund av oplanerade händelser, till exempel fel i underliggande maskin vara, program vara eller nätverk. [Redundans och korrigering för Azure cache för Redis](cache-failover.md) innehåller en detaljerad förklaring av olika typer av Redis-redundans. En Azure-cache för Redis kommer att gå igenom många olika redundans under dess livs längd. Arkitekturen för hög tillgänglighet är utformad för att göra dessa ändringar i en cache som transparent för dess klienter som möjligt.

>[!NOTE]
>Följande är tillgängligt som en för hands version.
>
>

Dessutom tillåter Azure cache för Redis ytterligare replik-noder på Premium-nivån. En [cachelagring med flera repliker](cache-how-to-multi-replicas.md) kan konfigureras med upp till tre noder i replikeringen. Att ha fler repliker förbättrar ofta återhämtnings förmågan på grund av de ytterligare noderna som säkerhetskopierar den primära. Även om du har fler repliker kan en Azure-cache för Redis-instans fortfarande påverkas allvarligt av ett Data Center-eller AZ-nivå avbrott. Du kan öka cachens tillgänglighet genom att använda flera repliker tillsammans med [zon redundans](#zone-redundancy).

## <a name="zone-redundancy"></a>Zonredundans

Azure cache för Redis har stöd för Zone-redundanta konfigurationer på Premium-och Enterprise-nivåerna. En [redundant cache i zonen](cache-how-to-zone-redundancy.md) kan placera sina noder i olika [Azure-tillgänglighetszoner](../availability-zones/az-overview.md) i samma region. Den eliminerar Data Center eller AZ avbrott som en enskild felpunkt och ökar den övergripande tillgängligheten för din cache.

### <a name="premium-tier"></a>Premiumnivå

>[!NOTE]
>Detta är tillgängligt som en för hands version.
>
>

Följande diagram illustrerar zonens redundanta konfiguration för Premium-nivån:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Installation av zon redundans":::
   
Azure cache för Redis distribuerar noder i en redundant cache för zonen i ett avrundnings sätt över den AZs som du har valt. Den avgör också vilken nod som ska fungera som primär första.

En redundant cache i zonen ger automatisk redundans. När den aktuella primära noden inte är tillgänglig tas en av replikerna över. Programmet kan få högre svars tid för cachen om den nya primära noden finns i en annan AZ. AZs är geografiskt åtskilda. Om du växlar från en AZ till en annan ändras det fysiska avståndet mellan var programmet och cachen finns. Den här ändringen påverkar fördröjning av nätverks fördröjning från ditt program till cacheminnet. Den extra svars tiden förväntas ligga inom ett acceptabelt intervall för de flesta program. Vi rekommenderar att du testar ditt program för att säkerställa att det fungerar bra med en zon-redundant cache.

### <a name="enterprise-tiers"></a>Företagsnivåer

En cache på företags nivå körs på ett Redis Enterprise-kluster. Det måste alltid finnas ett udda antal noder för att skapa ett kvorum. Som standard består den av tre noder, som var och en är värd för en dedikerad virtuell dator. En företags-cache har två *datanoder* med samma storlek och en mindre *nod*. En Enterprise Flash-cache har tre data noder med samma storlek. Företags klustret delar upp Redis-data i partitioner internt. Varje partition har en *primär* och minst en *replik*. Varje datanod innehåller en eller flera partitioner. Företags klustret säkerställer att den primära och repliken av en partition aldrig befinner sig på samma datanod. Partitioner replikerar data asynkront från presidentval till sina motsvarande repliker.

När en datanoden blir otillgänglig eller om en nätverks delning sker, sker en redundansväxling som liknar den som beskrivs i [standardreplikeringen](#standard-replication) . Företags klustret använder en kvorum modell för att avgöra vilka kvarvarande noder som ska ingå i ett nytt kvorum. Den befordrar också diskpartitioner inom dessa noder till presidentval efter behov.

## <a name="geo-replication"></a>Geo-replikering

[Geo-replikering](cache-how-to-geo-replication.md) är en mekanism för att länka två eller flera Azure-cache-instanser för Redis-instanser, som vanligt vis spänner över två Azure-regioner. 

### <a name="premium-tier"></a>Premiumnivå

>[!NOTE]
>Geo-replikering på Premium-nivån har utformats huvudsakligen för haveri beredskap.
>
>

Två cache-instanser på Premium-nivå kan anslutas via [geo-replikering](cache-how-to-geo-replication.md) så att du kan säkerhetskopiera dina cache-data till en annan region. När den väl har länkats, anges en instans som den primära länkade cachen och den andra som den sekundära länkade cachen. Endast den primära cachen accepterar Läs-och skriv förfrågningar. Data som skrivs till den primära cachen replikeras till den sekundära cachen. Ett program får åtkomst till cacheminnet via separata slut punkter för de primära och sekundära cacheminnena. Programmet måste skicka alla Skriv förfrågningar till det primära cacheminnet när det har distribuerats i flera Azure-regioner. Den kan läsa antingen från den primära eller sekundära cachen. I allmänhet vill du att programmets beräknings instanser ska läsa från de närmaste cacheminnen för att minska svars tiden. Data överföring mellan de två cache-instanserna skyddas av TLS.

Geo-replikering ger inte automatisk redundans på grund av problem med den extra tiden för nätverks fördröjning mellan regioner om resten av programmet finns kvar i den primära regionen. Du måste hantera och initiera redundansväxlingen genom att ta bort länken till det sekundära cacheminnet. Detta upphöjer den till den nya primära instansen.

### <a name="enterprise-tiers"></a>Företagsnivåer

>[!NOTE]
>Detta är tillgängligt som en för hands version.
>
>

Företags nivåerna har stöd för en mer avancerad form av geo-replikering, som kallas [aktiv geo-replikering](cache-how-to-active-geo-replication.md). Genom att dra nytta av motstridiga replikerade data typer kan Redis Enterprise-programvaran skriva till flera cache-instanser och ta hand om sammanslagning av ändringar och lösa konflikter vid behov. Två eller flera cache-instanser på företags nivå i olika Azure-regioner kan kopplas till en aktiv geo-replikerad cache. Ett program som använder sådan cache kan läsa och skriva till de geo-distribuerade cache-instanserna via motsvarande slut punkter. Den bör använda vad som är närmast varje beräknings instans, vilket ger den lägsta svars tiden. Programmet måste också övervaka cache-instanserna och byta till en annan region om någon av instanserna blir otillgänglig. Mer information om hur aktiv geo-replikering fungerar finns i [Active-active Geo-Distriubtion (CRDTs-based)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar Azure cache för Redis-alternativ med hög tillgänglighet.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)
* [Lägga till repliker i Azure cache för Redis](cache-how-to-multi-replicas.md)
* [Aktivera zon redundans för Azure cache för Redis](cache-how-to-zone-redundancy.md)
* [Konfigurera geo-replikering för Azure cache för Redis](cache-how-to-geo-replication.md)
