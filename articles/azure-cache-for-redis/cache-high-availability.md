---
title: Hög tillgänglighet för Azure Cache for Redis
description: Läs mer Azure Cache for Redis funktioner och alternativ för hög tillgänglighet
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719105"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hög tillgänglighet för Azure Cache for Redis

Azure Cache for Redis har inbyggd hög tillgänglighet. Målet med arkitekturen för hög tillgänglighet är att se till att din hanterade Redis-instans fungerar även när dess underliggande virtuella datorer (VM) påverkas av planerade eller oplanerade avbrott. Den ger mycket högre procentpriser än vad som går att uppnå genom att vara värd för Redis på en enda virtuell dator.

Azure Cache for Redis implementerar hög tillgänglighet genom att använda flera virtuella datorer, som kallas *noder,* för ett cacheminne. Den konfigurerar dessa noder så att datareplikering och redundans sker på ett samordnat sätt. Den orkestrera även underhållsåtgärder som redis-programuppdatering. Olika alternativ för hög tillgänglighet är tillgängliga på nivåerna Standard, Premium och Enterprise:

| Alternativ | Beskrivning | Tillgänglighet | Standard | Premium | Stora företag |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standardreplikering](#standard-replication)| Replikerad konfiguration med dubbla noder i ett enda datacenter med automatisk redundans | 99,9 % (se [information)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Zonredundans](#zone-redundancy) | Replikerad konfiguration med flera noder mellan AZ:er, med automatisk redundans | Upp till 99,99 % (se [information)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|Förhandsgranskning|✔|
| [Geo-replikering](#geo-replication) | Länkade cacheinstanser i två regioner med användarkontrollerad redundans | Upp till 99,999 % (se [information)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|Förhandsgranskning|

## <a name="standard-replication"></a>Standardreplikering

En Azure Cache for Redis på Standard- eller Premium-nivån körs på ett par Redis-servrar som standard. De två servrarna finns på dedikerade virtuella datorer. Redis med öppen källkod tillåter endast en server att hantera dataskrivningsbegäranden. Den här servern är den *primära* noden, medan den andra *repliken*. När servernoderna har tilldelar Azure Cache for Redis primära roller och replikroller till dem. Den primära noden ansvarar vanligtvis för att underhålla skriv- och läsbegäranden från Redis-klienter. Vid en skrivåtgärd genomför den en ny nyckel och en nyckeluppdatering av det interna minnet och svarar omedelbart till klienten. Den vidarebefordrar åtgärden till repliken asynkront.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Konfigurera datareplikering":::
   
>[!NOTE]
>Normalt kommunicerar en Redis-klient med den primära noden i en Redis-cache för alla läs- och skrivbegäranden. Vissa Redis-klienter kan konfigureras för att läsa från repliknoden.
>
>

Om den primära noden i en Redis-cache inte är tillgänglig, befordrar repliken sig själv så att den blir den nya primära automatiskt. Den här processen kallas för en *redundans .* Repliken väntar tillräckligt lång tid innan den tar över om den primära noden återställs snabbt. När en redundans inträffar Azure Cache for Redis en ny virtuell dator och ansluter den till cachen som repliknoden. Repliken utför en fullständig datasynkronisering med den primära så att den har en annan kopia av cachedata.

En primär nod kan gå ur drift som en del av ett planerat underhåll, till exempel Redis-programvara eller uppdatering av operativsystemet. Det kan också sluta fungera på grund av oplanerade händelser, till exempel fel i underliggande maskinvara, programvara eller nätverk. [Redundans och uppdatering för Azure Cache for Redis](cache-failover.md) ger en detaljerad förklaring av redis-redundanstyper. En Azure Cache for Redis går igenom många redundanser under livslängden. Arkitekturen för hög tillgänglighet är utformad för att göra dessa ändringar i ett cacheminne så transparent för sina klienter som möjligt.

>[!NOTE]
>Följande är tillgängligt som en förhandsversion.
>
>

Dessutom kan Azure Cache for Redis ytterligare repliknoder på Premium-nivån. En [cache för flera repliker](cache-how-to-multi-replicas.md) kan konfigureras med upp till tre repliknoder. Att ha fler repliker förbättrar vanligtvis återhämtningen på grund av de ytterligare noder som backar upp den primära repliken. Även med fler repliker kan en Azure Cache for Redis-instans fortfarande påverkas allvarligt av ett avbrott på datacenter- eller AZ-nivå. Du kan öka tillgängligheten för cachen genom att använda flera repliker tillsammans med [zonredundans](#zone-redundancy).

## <a name="zone-redundancy"></a>Zonredundans

Azure Cache for Redis har stöd för zonredundant konfiguration på Premium- och Enterprise-nivåerna. En [zonredundant cache](cache-how-to-zone-redundancy.md) kan placera noderna [i Azure-tillgänglighetszoner](../availability-zones/az-overview.md) i samma region. Det eliminerar datacenter- eller AZ-avbrott som en felpunkt och ökar den övergripande tillgängligheten för ditt cacheminne.

### <a name="premium-tier"></a>Premiumnivå

>[!NOTE]
>Detta är tillgängligt som en förhandsversion.
>
>

Följande diagram illustrerar zonredundant konfiguration för Premium-nivån:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Konfigurera zonredundans":::
   
Azure Cache for Redis distribuerar noder i en zonredundant cache med resursallokering över de AZ:er som du har valt. Den avgör också vilken nod som ska fungera som primär initialt.

En zonredundant cache ger automatisk redundans. När den aktuella primära noden inte är tillgänglig tar en av replikerna över. Programmet kan uppleva högre cachesvarstid om den nya primära noden finns i en annan AZ. AZs är geografiskt åtskilda. Om du växlar från en AZ till en annan ändras det fysiska avståndet mellan programmet och cachen. Den här ändringen påverkar svarstiderna i nätverket för tur och retur från ditt program till cachen. Den extra svarstiden förväntas ligga inom ett acceptabelt intervall för de flesta program. Vi rekommenderar att du testar ditt program för att säkerställa att det kan fungera bra med zonredundant cache.

### <a name="enterprise-tiers"></a>Företagsnivåer

En cache på någon av Enterprise-nivåerna körs på ett Redis Enterprise-kluster. Det krävs ett udda antal servernoder hela tiden för att bilda ett kvorum. Som standard består den av tre noder, som var och en finns på en dedikerad virtuell dator. Ett företagscache har två datanoder av *samma storlek och* en mindre kvorumnod.  En Enterprise Flash cache har tre datanoder av samma storlek. Enterprise-klustret delar in Redis-data i partitioner internt. Varje partition har en *primär* och minst en *replik.* Varje datanod innehåller en eller flera partitioner. Enterprise-klustret säkerställer att de primära replikerna och replikerna av en partition aldrig samplaceras på samma datanod. Partitioner replikerar data asynkront från försök till motsvarande repliker.

När en datanod blir otillgänglig eller en nätverksdelning sker sker en redundans som liknar den som beskrivs [i Standardreplikering.](#standard-replication) Enterprise-klustret använder en kvorumbaserad modell för att avgöra vilka kvarvarande noder som ska delta i ett nytt kvorum. Den befordrar även replikpartitioner inom dessa noder till primära försök efter behov.

## <a name="geo-replication"></a>Geo-replikering

[Geo-replikering](cache-how-to-geo-replication.md) är en mekanism för att länka två eller Azure Cache for Redis instanser, som vanligtvis omfattar två Azure-regioner. 

### <a name="premium-tier"></a>Premiumnivå

>[!NOTE]
>Geo-replikering på Premium-nivån är främst utformad för haveriberedskap.
>
>

Två cacheinstanser på Premium-nivå kan anslutas [via geo-replikering](cache-how-to-geo-replication.md) så att du kan kopier dina cachedata till en annan region. När en instans har länkats till varandra betecknas den som den primära länkade cachen och den andra som den sekundära länkade cachen. Endast den primära cachen accepterar läs- och skrivbegäranden. Data som skrivs till den primära cachen replikeras till den sekundära cachen. Ett program kommer åt cachen via separata slutpunkter för primära och sekundära cacheminnen. Programmet måste skicka alla skrivbegäranden till den primära cachen när det distribueras i flera Azure-regioner. Den kan läsa från antingen den primära eller sekundära cachen. I allmänhet vill du att programmets beräkningsinstanser ska läsa från närmaste cacheminnen för att minska svarstiden. Dataöverföring mellan de två cacheinstanserna skyddas av TLS.

Geo-replikering ger inte automatisk redundans på grund av problem över nätverkets tur och retur-tid mellan regioner om resten av ditt program finns kvar i den primära regionen. Du måste hantera och initiera redundansen genom att ta bort länken till den sekundära cachen. Detta gör att den blir den nya primära instansen.

### <a name="enterprise-tiers"></a>Företagsnivåer

>[!NOTE]
>Detta är tillgängligt som en förhandsversion.
>
>

Enterprise-nivåerna stöder en mer avancerad form av geo-replikering, som kallas [aktiv geo-replikering.](cache-how-to-active-geo-replication.md) Med hjälp av konfliktfria replikerade datatyper stöder Redis Enterprise-programvaran skrivningar till flera cacheinstanser och tar hand om sammanslagning av ändringar och löser konflikter vid behov. Två eller fler cacheinstanser på företagsnivå i olika Azure-regioner kan vara sammanflicerade för att bilda en aktiv geo-replikerad cache. Ett program som använder en sådan cache kan läsa och skriva till de geo-distribuerade cacheinstanserna via motsvarande slutpunkter. Den bör använda det som är närmast varje beräkningsinstans, vilket ger den lägsta svarstiden. Programmet måste också övervaka cacheinstanserna och växla till en annan region om en av instanserna blir otillgänglig. Mer information om hur aktiv geo-replikering fungerar finns i [Active-Active Geo-Distriubtion (CRDTs-Based)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar Azure Cache for Redis alternativ för hög tillgänglighet.

* [Azure Cache for Redis Premium-tjänstnivåer](cache-overview.md#service-tiers)
* [Lägga till repliker i Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [Aktivera zonredundans för Azure Cache for Redis](cache-how-to-zone-redundancy.md)
* [Konfigurera geo-replikering för Azure Cache for Redis](cache-how-to-geo-replication.md)
