---
title: Redundans och korrigering – Azure Cache for Redis
description: Läs om redundans, uppdatering och uppdaterings processen för Azure cache för Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210741"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Redundans och korrigering för Azure cache för Redis

För att kunna bygga elastiska och lyckade klient program är det viktigt att förstå redundansväxlingen i kontexten för Azure cache för Redis-tjänsten. En redundansväxling kan vara en del av planerade hanterings åtgärder eller kan orsakas av oplanerade maskin-eller nätverks fel. En vanlig användning av cachelagring av cachen kommer när hanterings tjänsten återanvänder Azure-cachen för Redis-binärfiler. Den här artikeln beskriver vad en redundansväxling är, hur det sker under korrigeringen och hur du skapar ett elastiskt klient program.

## <a name="what-is-a-failover"></a>Vad är en redundansväxling?

Vi börjar med en översikt över redundans för Azure cache för Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>En snabb översikt över cache-arkitekturen

En cache är konstruerad av flera virtuella datorer med separata, privata IP-adresser. Varje virtuell dator, som även kallas nod, är ansluten till en delad belastningsutjämnare med en enda virtuell IP-adress. Varje nod kör Redis-serverns process och är tillgänglig med hjälp av värd namnet och Redis-portarna. Varje nod anses vara antingen en primär eller en replikerad nod. När ett klient program ansluter till ett cacheminne går dess trafik genom belastningsutjämnaren och dirigeras automatiskt till den primära noden.

I en grundläggande cache är den enskilda noden alltid en primär. I en standard-eller Premium-cache finns det två noder: en väljs som primär och den andra är repliken. Eftersom standard-och Premium-cachen har flera noder, kan en nod vara otillgänglig medan den andra fortsätter att bearbeta begär Anden. Klustrade cacheminnen består av många Shards, var och en med distinkta primära noder och noder. En Shard kan vara nere medan de andra är tillgängliga.

> [!NOTE]
> En grundläggande cache har inte flera noder och erbjuder inte något service nivå avtal (SLA) för dess tillgänglighet. Basic-cacheminnen rekommenderas endast för utvecklings-och testnings ändamål. Använd en standard-eller Premium-cache för en distribution med flera noder för att öka tillgängligheten.

### <a name="explanation-of-a-failover"></a>Förklaring av redundans

En redundansväxling inträffar när en nod på en nod höjer sig själv för att bli en primär nod och den gamla primära noden stänger befintliga anslutningar. När den primära noden har säkerhetskopierats visas ändringarna i rollerna och degraderas som en replik. Den ansluter sedan till den nya primära och synkroniserar data. En redundansväxling kan vara planerad eller oplanerad.

En *planerad redundansväxling* sker under system uppdateringar, t. ex. Redis korrigering eller uppgraderingar av operativ system samt hanterings åtgärder, till exempel skalning och omstarter. Eftersom noderna tar emot ett meddelande om uppdateringen kan de enkelt byta ut roller och snabbt uppdatera belastnings Utjämnings ändringen. En planerad redundansväxling slutförs normalt på mindre än 1 sekund.

En *oplanerad redundansväxling* kan inträffa på grund av maskin varu fel, nätverks fel eller andra oväntade avbrott till den primära noden. Noden replikering befordras till primär, men processen tar längre tid. En nod för replikering måste först identifiera att den primära noden inte är tillgänglig innan den kan initiera redundansväxlingen. Replik-noden måste också kontrol lera att detta oplanerat fel inte är tillfälligt eller lokalt, för att undvika onödig redundans. Den här fördröjningen innebär att en oplanerad redundansväxling vanligt vis slutförs inom 10 till 15 sekunder.

## <a name="how-does-patching-occur"></a>Hur sker uppdatering?

Azure cache för Redis-tjänsten uppdaterar regelbundet din cache med de senaste plattforms funktionerna och korrigeringarna. För att korrigera en cache följer tjänsten dessa steg:

1. Hanterings tjänsten väljer en nod som ska korrigeras.
1. Om den valda noden är en primär nod, marknadsförs motsvarande nod för replikering av sig själv. Den här kampanjen betraktas som planerad redundansväxling.
1. Den valda noden startas om för att ta de nya ändringarna och kommer att säkerhets kopie ras som en Replica-nod.
1. Noden replikering ansluter till den primära noden och synkroniserar data.
1. När datasynkroniseringen är klar upprepas korrigerings processen för de återstående noderna.

Eftersom korrigeringen är en planerad redundansväxling, höjer noden replikering snabbt sig själv för att bli en primär och påbörjar betjäna begär Anden och nya anslutningar. Basic-cacheminnen har ingen nod för replikering och är inte tillgängliga förrän uppdateringen har slutförts. Varje Shard i en klustrad cache korrigeras separat och stänger inte anslutningar till en annan Shard.

> [!IMPORTANT]
> Noderna korrigeras en i taget för att förhindra data förlust. Grundläggande cacheminnen kommer att ha data förlust. Klustrade cacheminnen korrigeras en Shard i taget.

Flera cacheminnen i samma resurs grupp och region har också uppdaterats en i taget.  Cacheminnen som finns i olika resurs grupper eller olika regioner kan komma att korrigeras samtidigt.

Eftersom fullständig datasynkronisering sker innan processen upprepas, är det osannolikt att data förlust uppstår om du använder en standard-eller Premium-cache. Du kan ytterligare skydda dig mot data förlust genom att [Exportera](cache-how-to-import-export-data.md#export) data och aktivera [persistence](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Ytterligare cache-belastning

När en redundansväxling inträffar måste standard-och Premium-cachen replikera data från en nod till en annan. Den här replikeringen orsakar en belastnings ökning i både server minne och CPU. Om cache-instansen redan är hårt inläst kan klient programmen uppleva en ökad fördröjning. I extrema fall kan klient program ta emot timeout-undantag. För att minska effekten av den här extra belastningen [konfigurerar](cache-configure.md#memory-policies) du inställningen för cacheminnet `maxmemory-reserved` .

## <a name="how-does-a-failover-affect-my-client-application"></a>Hur påverkar en redundansväxling mitt klient program?

Antalet fel som visas av klient programmet beror på hur många åtgärder som var beroende av anslutningen vid redundansväxlingen. Alla anslutningar som dirigeras via noden som stängde dess anslutningar kommer att se fel. Många klient bibliotek kan utlösa olika typer av fel när anslutningar bryts, inklusive timeout-undantag, anslutnings undantag eller socket-undantag. Antalet och typen av undantag beror på var i kod Sök vägen som begäran kommer när cachen stänger dess anslutningar. Till exempel kan en åtgärd som skickar en begäran men inte har fått ett svar när redundansväxlingen inträffar kan få ett timeout-undantag. Nya begär Anden för det stängda anslutningsobjektet tar emot anslutnings undantag tills åter anslutningen har genomförts.

De flesta klient bibliotek försöker återansluta till cachen om de är konfigurerade att göra det. Oväntade buggar kan dock ibland placera biblioteks objekt i ett oåterkalleligt tillstånd. Om felet kvarstår under längre tid än en förkonfigurerad tids period ska anslutningsobjektet återskapas. I Microsoft.NET och andra objektorienterade språk kan du återskapa anslutningen utan att starta om programmet genom att använda [ett Lazy- \<T\> mönster](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Hur gör jag för att göra programmet elastiskt?

Eftersom du inte kan undvika redundans helt kan du skriva dina klient program för återhämtning till anslutnings avbrott och misslyckade förfrågningar. Även om de flesta klient bibliotek automatiskt återansluter till cache-slutpunkten så försöker några av dem att försöka utföra misslyckade förfrågningar igen. Beroende på program scenariot kan det vara klokt att använda omprövnings logik med backoff.

Om du vill testa ett klient programs återhämtning använder du en [omstart](cache-administration.md#reboot) som en manuell utlösare för anslutnings avbrott. Dessutom rekommenderar vi att du [schemalägger uppdateringar](cache-administration.md#schedule-updates) på en cache. Be hanterings tjänsten att tillämpa Redis runtime-korrigeringsfiler under angivna vecko Visa fönster. Dessa fönster är vanligt vis perioder när klient program trafiken är låg, för att undvika potentiella incidenter.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Kan jag få ett meddelande i förväg om ett planerat underhåll?

Azure cache för Redis publicerar nu meddelanden på en publicera/prenumerera-kanal som heter [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) cirka 30 sekunder innan planerade uppdateringar. Detta är körnings meddelanden, och de är särskilt utformade för program som kan använda krets brytare för att kringgå cache-eller buffer-kommandon, till exempel under planerade uppdateringar. Det är inte en mekanism som kan meddela dig om dagar eller timmar i förväg.

### <a name="client-network-configuration-changes"></a>Klient nätverk – konfigurations ändringar

Vissa nätverks konfigurations ändringar på klient sidan kan utlösa fel meddelande om att anslutningen inte är tillgänglig. Sådana ändringar kan vara:

- Växling av klient programmets virtuella IP-adress mellan mellanlagrings-och produktions platser.
- Skalar storlek eller antal instanser av programmet.

Sådana ändringar kan orsaka ett anslutnings problem som varar mindre än en minut. Klient programmet kommer förmodligen att förlora sin anslutning till andra externa nätverks resurser utöver Azure-cachen för Redis-tjänsten.

## <a name="next-steps"></a>Nästa steg

- [Schemalägg uppdateringar](cache-administration.md#schedule-updates) för cacheminnet.
- Testa program återhämtning med en [omstart](cache-administration.md#reboot).
- [Konfigurera](cache-configure.md#memory-policies) minnes reservationer och-principer.
