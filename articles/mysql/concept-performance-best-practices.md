---
title: Metod tips för prestanda – Azure Database for MySQL
description: I den här artikeln beskrivs de bästa metoderna för att övervaka och justera prestanda för Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880414"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Bästa praxis för optimala prestanda för din Azure Database for MySQL-enskild server

Lär dig mer om bästa praxis för att få bästa möjliga prestanda när du arbetar med din Azure Database for MySQL-enskild server. När vi lägger till nya funktioner till plattformen kommer vi att fortsätta att förfina de bästa metoderna som beskrivs i det här avsnittet.

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
- Ställ in aviseringar på sådana siffror för att säkerställa att när servrarna når gränser kan du vidta åtgärder för att åtgärda problemet. Baserat på de gränser som definieras kontrollerar du om du skalar upp databas-SKU: n, antingen till högre beräknings storlek eller till en bättre pris nivå som resulterar i en dramatisk ökning av prestandan. 
- Skala upp tills prestanda numren inte längre sjunker efter en skalnings åtgärd. Information om hur du övervakar en DB-instanss mått finns i [MySQL db-mått](./concepts-monitoring.md#metrics).

## <a name="next-steps"></a>Nästa steg

- [Bästa praxis för Server åtgärder med hjälp av Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Bästa praxis för att övervaka din Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Kom igång med Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>