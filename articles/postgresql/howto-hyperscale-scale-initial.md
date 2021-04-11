---
title: Ursprunglig Server grupp storlek – citus)-Azure Database for PostgreSQL
description: Välj rätt initial storlek för ditt användnings fall
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012514"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Välj ursprunglig storlek för citus-Server grupp

Storleken på en Server grupp, både antal noder och deras maskin varu kapacitet, är [enkel att ändra](howto-hyperscale-scale-grow.md). Du behöver dock fortfarande välja en ursprunglig storlek för en ny server grupp. Här följer några tips för ett rimligt val.

## <a name="use-cases"></a>Användnings fall

Citus (storskalig) används ofta på följande sätt.

### <a name="multi-tenant-saas"></a>SaaS med flera klientorganisationer

När du migrerar till storskalig (citus) från en befintlig instans av en PostgreSQL-databas, väljer du ett kluster där antalet arbets virtuella kärnor och RAM-minne totalt är lika med den ursprungliga instansen. I sådana scenarier har vi sett prestanda förbättringar på 2 – 3 – 3 gånger eftersom horisontell Partitionering förbättrar resursutnyttjande och ger mindre index osv.

Antalet vCore är faktiskt det enda beslutet. RAM-allokeringen fastställs för närvarande baserat på vCore-antal, enligt beskrivningen i sidan [konfigurations alternativ för citus ()](concepts-hyperscale-configuration-options.md) .
Koordinator-noden kräver inte lika mycket RAM som arbetare, men det finns inget sätt att välja RAM-och virtuella kärnor separat.

### <a name="real-time-analytics"></a>Realtidsanalys

Totalt antal virtuella kärnor: när du arbetar med data i RAM-minnet kan du förvänta en linjär prestanda förbättring på storskaligheten (citus) som är proportionell mot antalet arbets kärnor. Om du vill fastställa rätt antal virtuella kärnor för dina behov kan du tänka på den aktuella svars tiden för frågor i databasen med en nod och den svars tid som krävs i storskalig (citus). Dela den nuvarande svarstiden med den önskade svarstiden och avrunda resultatet.

Arbets minne i arbets minnet: det bästa fallet skulle ge tillräckligt med minne som de flesta arbets uppsättningar passar i minnet. Vilken typ av frågor som programmet använder påverkar minneskraven. Du kan köra FÖRKLARINGs analys av en fråga för att avgöra hur mycket minne som krävs. Kom ihåg att virtuella kärnor och RAM skalas tillsammans enligt beskrivningen i artikeln om [konfigurations alternativ för storskaliga (citus)](concepts-hyperscale-configuration-options.md) .

## <a name="choosing-a-hyperscale-citus-tier"></a>Välja en storskalig nivå (citus)

> [!IMPORTANT]
> Nivån för den storskaliga (citus) Basic-nivån är för närvarande en för hands version.  Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

I avsnitten ovan får du en uppfattning om hur många virtuella kärnor och hur mycket RAM-minne som behövs för varje användnings fall. Du kan uppfylla dessa krav genom att välja mellan två citus-nivåer: Basic-nivån och standard nivån.

Basic-nivån använder en enda databas-nod för att utföra bearbetningen, medan standard nivån tillåter fler noder. Nivåerna är i övrigt identiska och erbjuder samma funktioner. I vissa fall kan en enskild nods virtuella kärnor och disk utrymme skalas till tillräckligt, och i andra fall kräver det att man samarbetar med flera noder.

En jämförelse av nivåerna finns på sidan begrepp på [grundläggande nivå](concepts-hyperscale-tiers.md) .

## <a name="next-steps"></a>Nästa steg

- [Skala en servergrupp](howto-hyperscale-scale-grow.md)
- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
