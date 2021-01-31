---
title: Metod tips för prestanda – Azure Database for MySQL
description: I den här artikeln beskrivs några rekommendationer för att övervaka och justera prestanda för Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 46c7952247babd528b230dfa0e70b0eb47878912
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217762"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Bästa praxis för optimala prestanda för din Azure Database for MySQL-enskild server

Lär dig hur du får bästa möjliga prestanda när du arbetar med Azure Database for MySQL-en server. När vi lägger till nya funktioner till plattformen fortsätter vi att förfina våra rekommendationer i det här avsnittet.

## <a name="physical-proximity"></a>Fysisk närhet

 Se till att distribuera ett program och databasen i samma region. En snabb kontroll innan du startar en prestanda mätnings körning är att fastställa nätverks fördröjningen mellan klienten och databasen med hjälp av en enkel SELECT 1-fråga. 

## <a name="accelerated-networking"></a>Accelererat nätverk

Använd accelererat nätverk för program servern om du använder Azure Virtual Machine, Azure Kubernetes eller App Services. Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Den här högpresterande sökvägen kringgår värden från Datapath, minskar svars tiden, skakningarna och CPU-användningen, för användning med de mest krävande nätverks belastningarna på VM-typer som stöds.

## <a name="connection-efficiency"></a>Anslutnings effektivitet

Att upprätta en ny anslutning är alltid en dyr och tids krävande uppgift. När ett program begär en databas anslutning prioriteras tilldelningen av befintliga inaktiva databas anslutningar i stället för att skapa en ny.  Här följer några alternativ för metoder för lämplig anslutning:

- **ProxySQL**: Använd [ProxySQL](https://proxysql.com/) som tillhandahåller inbyggd anslutningspoolen och [belastningsutjämna din arbets belastning](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) till flera Läs repliker efter behov på begäran med eventuella ändringar i program koden.

- **Heimdall-dataproxy**: du kan också använda Heimdall-dataproxy, en leverantörs oberoende patentskyddad proxy-lösning. Det stöder cachelagring av frågor och Läs/skriv-delning med fördröjning av replikeringsfördröjning. Du kan också se hur du [påskyndar MySQL-prestanda med Heimdall proxy](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Beständig eller Long-Lived anslutning**: om ditt program har korta transaktioner eller frågor normalt med körnings tid < 5-10 MS, ersätter du korta anslutningar med permanenta anslutningar. Ersättnings korta anslutningar med permanenta anslutningar kräver bara mindre ändringar i koden, men det har en stor effekt vad gäller att förbättra prestanda i många typiska program scenarier. Se till att ange tids gränsen eller Stäng anslutningen när transaktionen är klar.

- **Replik**: om du använder repliker kan du använda [ProxySQL](https://proxysql.com/) för att utjämna belastningen mellan den primära servern och den läsbara sekundära replik servern. Lär dig [hur du konfigurerar ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Konfigurationer för data import

- Du kan tillfälligt skala instansen till en högre SKU-storlek innan du startar en data import åtgärd och sedan skalar ned den när importen har slutförts.
- Du kan importera dina data med minimal nedtid genom att använda [Azure Database migration service (DMS)](https://datamigration.microsoft.com/) för online-eller offline-migrering. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL minnes rekommendationer

Den bästa metoden för Azure Database for MySQL prestanda är att allokera tillräckligt med RAM-minne, så att du kan arbeta i nästan helt i minnet. 

- Kontrol lera om minnes procent andelen som används för att nå [gränserna](./concepts-pricing-tiers.md) med hjälp av [måtten för MySQL-servern](./concepts-monitoring.md). 
- Ställ in aviseringar på sådana siffror för att säkerställa att när servrarna når gränser kan du vidta åtgärder för att åtgärda problemet. Baserat på de gränser som definieras kontrollerar du om du skalar upp databas-SKU: n, antingen till högre beräknings storlek eller till en bättre pris nivå, vilket resulterar i en dramatisk ökning av prestandan. 
- Skala upp tills prestanda numren inte längre sjunker efter en skalnings åtgärd. Information om hur du övervakar en DB-instanss mått finns i [MySQL db-mått](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>Använd InnoDB buffer uppvärmnings

När du har startat om Azure Database for MySQL-servern laddas data sidorna i lagret som tabeller efter frågas, vilket leder till ökad latens och sämre prestanda för den första körningen av frågorna. Detta är kanske inte acceptabelt för svars känsliga arbets belastningar. 

Användningen av InnoDB buffer uppvärmnings förkortar uppvärmningss perioden genom att läsa in disk sidor som fanns i bufferten innan omstarten, i stället för att vänta på DML-eller SELECT-åtgärder för att få åtkomst till motsvarande rader.

Du kan minska uppvärmnings-perioden efter att du startat om din Azure Database for MySQL-server, vilket motsvarar en prestanda förmån genom att konfigurera [InnoDB-parametrar för buffertpooltillägget](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). InnoDB sparar en procent andel av de senast använda sidorna för varje resurspool när servern stängs av och återställer sidorna vid Server start.

Det är också viktigt att Observera att förbättrad prestanda kommer till kostnaden för längre start tid för servern. När den här parametern är aktive rad förväntas start-och start tid för servern för att öka beroende på den IOPS som har allokerats på servern. 

Vi rekommenderar att du testar och övervakar omstarts tiden för att säkerställa att prestandan för start/omstart är acceptabel eftersom servern inte är tillgänglig under den tiden. Vi rekommenderar inte att du använder den här parametern med mindre än 1000 etablerade IOPS (eller med andra ord när lagrings utrymmet är mindre än 335 GB).

Ange server parametern till om du vill spara statusen för bufferten vid avstängning av `innodb_buffer_pool_dump_at_shutdown` servern `ON` . På samma sätt anger du Server parametern `innodb_buffer_pool_load_at_startup` till `ON` för att återställa buffertens tillstånd vid Server start. Du kan styra påverkan på Start-och återstarts tid genom att sänka och finjustera Värdet för Server parametern `innodb_buffer_pool_dump_pct` . Den här parametern är som standard inställd på `25` .

> [!Note]
> Uppvärmnings-parametrarna för InnoDB buffer stöds bara i generella lagrings servrar med upp till 16 TB lagrings utrymme. Läs mer om [Azure Database for MySQL lagrings alternativ här](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

## <a name="next-steps"></a>Nästa steg

- [Bästa praxis för Server åtgärder med hjälp av Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Bästa praxis för att övervaka din Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Kom igång med Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>