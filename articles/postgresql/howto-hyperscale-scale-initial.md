---
title: Ursprunglig Server grupp storlek – citus)-Azure Database for PostgreSQL
description: Välj rätt initial storlek för ditt användnings fall
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026411"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Välj ursprunglig storlek för citus-Server grupp

Storleken på en Server grupp, både dess antal noder och deras maskin varu kapacitet, är [enkel att ändra](howto-hyperscale-scale-grow.md). Du behöver dock fortfarande välja en ursprunglig storlek för en ny server grupp. Här följer några tips för ett rimligt val.

## <a name="multi-tenant-saas-use-case"></a>Användnings fall för SaaS för flera innehavare

När du migrerar till storskalig (citus) från en befintlig instans av en PostgreSQL-databas, väljer du ett kluster där antalet arbets virtuella kärnor och RAM-minne totalt är lika med den ursprungliga instansen. I sådana scenarier har vi sett prestanda förbättringar på 2 – 3 – 3 gånger eftersom horisontell Partitionering förbättrar resursutnyttjande och ger mindre index osv.

Antalet vCore är faktiskt det enda beslutet. RAM-allokeringen fastställs för närvarande baserat på vCore-antal, enligt beskrivningen i sidan [konfigurations alternativ för citus ()](concepts-hyperscale-configuration-options.md) .
Koordinator-noden kräver inte lika mycket RAM som arbetare, men det finns inget sätt att välja RAM-och virtuella kärnor separat.

## <a name="real-time-analytics-use-case"></a>Användnings fall för real tids analys

Totalt antal virtuella kärnor: när du arbetar med data i RAM-minnet kan du förvänta en linjär prestanda förbättring på storskaligheten (citus) som är proportionell mot antalet arbets kärnor. Om du vill fastställa rätt antal virtuella kärnor för dina behov kan du tänka på den aktuella svars tiden för frågor i databasen med en nod och den svars tid som krävs i storskalig (citus). Dela den nuvarande svarstiden med den önskade svarstiden och avrunda resultatet.

RAM-arbetsminne: det bästa är att ha så mycket minne att majoriteten av arbetsuppsättningen får plats i minnet. Vilken typ av frågor som programmet använder påverkar minneskraven. Du kan köra FÖRKLARINGs analys av en fråga för att avgöra hur mycket minne som krävs. Kom ihåg att virtuella kärnor och RAM skalas tillsammans enligt beskrivningen i artikeln om [konfigurations alternativ för storskaliga (citus)](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Nästa steg

- [Skala en servergrupp](howto-hyperscale-scale-grow.md)
- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
