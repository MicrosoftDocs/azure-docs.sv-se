---
title: Metod tips för hantering av MySQL-server – Azure Database for MySQL
description: I den här artikeln beskrivs de bästa metoderna för att använda MySQL-databasen på Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355119"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Metod tips för Server åtgärder på Azure Database for MySQL-enskild server

Lär dig mer om metod tips för att arbeta med Azure Database for MySQL. När vi lägger till nya funktioner till plattformen kommer vi att fortsätta fokusera på att förfina de bästa metoderna som beskrivs i det här avsnittet.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL operativa rikt linjer 

Följande är rikt linjer som bör följas när du arbetar med Azure Database for MySQL för att förbättra databasens prestanda: 

* **Samplacering**: om du vill minska nätverks fördröjningen placerar du klienten och databas servern i samma Azure-region.

* **Övervaka din minnes-, processor-och lagrings användning**: du kan [Konfigurera aviseringar](howto-alert-on-metric.md) som meddelar dig när användnings mönster ändras eller när du närmar dig kapaciteten för distributionen, så att du kan upprätthålla systemets prestanda och tillgänglighet. 

* **Skala upp din databas instans**: du kan [skala upp](howto-create-manage-server-portal.md) när du närmar dig lagrings kapacitets gränser. Du bör ha viss buffert i lagrings utrymme och minne för att kunna hantera oförutsedda ökningar i efter frågan från dina program. Du kan också aktivera funktionen för automatisk [utökning av lagrings](howto-auto-grow-storage-portal.md) funktionen "på" för att säkerställa att tjänsten automatiskt skalar lagringen när den närmar sig lagrings gränserna. 

* **Konfigurera säkerhets kopieringar**: aktivera [lokala eller geo-redundanta säkerhets kopieringar](howto-restore-server-portal.md#set-backup-configuration) baserat på företagets behov. Du kan också ändra kvarhållningsperioden för hur länge säkerhets kopiorna är tillgängliga för affärs kontinuitet. 

* **Öka I/O-kapacitet**: om databas arbets belastningen kräver mer i/o än du har etablerad, återställning eller andra transaktions åtgärder för databasen kommer att bli långsam. Gör något av följande om du vill öka I/O-kapaciteten för en Server instans: 

    * Azure Database för MySQL ger IOPS-skalning med en hastighet av tre IOPS per GB lagrings utrymme. [Öka det allokerade lagrings utrymmet](howto-create-manage-server-portal.md#scale-storage-up) för att skala IOPS för bättre prestanda. 

    * Om du redan använder allokerad IOPS-lagring etablerar du [ytterligare data flödes kapacitet](howto-create-manage-server-portal.md#scale-storage-up). 

* **Skalnings beräkning**: databasens arbets belastning kan också begränsas på grund av processor-eller minnes kapacitet och detta kan ha stor inverkan på transaktions bearbetningen. Observera att Compute (pris nivå) bara kan skalas upp eller ned mellan [generell användning eller](concepts-pricing-tiers.md) minnesoptimerade nivåer. 

* **Testa för redundans**: testa redundansväxlingen manuellt för Server instansen för att förstå hur lång tid processen tar för ditt användnings fall och se till att det program som ansluter till Server instansen automatiskt kan ansluta till den nya Server instansen efter redundansväxlingen.

* **Använd primär nyckel**: kontrol lera att dina tabeller har en primär eller unik nyckel när du arbetar med Azure Database for MySQL. Detta hjälper till att göra säkerhets kopior, repliker osv och bättre prestanda.

* **Konfigurera TTL-värde**: om klient programmet cachelagrar Domain Name Service-data (DNS) för dina Server instanser anger du ett TTL-värde (Time-to-Live) på mindre än 30 sekunder. Eftersom den underliggande IP-adressen för en Server instans kan ändras efter en redundansväxling, kan cachelagring av DNS-data under en längre tid leda till anslutnings fel om ditt program försöker ansluta till en IP-adress som inte längre är i tjänst.

* Använd anslutningspoolen för att undvika att de [maximala anslutnings gränserna upprättas](concepts-server-parameters.md#max_connections)och Använd omprövnings logik för att undvika tillfälliga anslutnings problem. 

* Om du använder repliken använder du [ProxySQL för att utjämna belastningen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) mellan den primära servern och den läsbara sekundära replik servern. Se installations stegen här. </br> 

* När du konfigurerar resursen kontrollerar du att du har [aktiverat autoökningen](howto-auto-grow-storage-portal.md) för Azure Database for MySQL. Detta lägger inte till ytterligare kostnader och skyddar databasen från eventuella Flask halsar i lagrings utrymmen som du kan köra i. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Använda InnoDB med Azure Database for MySQL

*   Om du använder `ibdata1` en funktion, som är en data fil med systemets tabell utrymme kan du inte krympa eller rensa genom att släppa data från tabellen eller flytta tabellen till fil per tabell `tablespaces` .

* För en databas som är större än 1 TB bör du skapa tabellen i **innodb_file_per_table** `tablespace` . För en enskild tabell som är större än 1 TB, bör du ha [partitionstabellen](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

*   För en server som har ett stort antal `tablespace` , kommer motor starten att vara mycket långsamt på grund av genomsökningen av sekventiella tabell utrymmen under MySQL-start eller redundans. 

* Ange innodb_file_per_table = på innan du skapar en tabell, om det totala tabell numret är mindre än 500.

* Om du har fler än 500 tabeller i en databas granskar du tabell storleken för varje enskild tabell. För en stor tabell bör du fortfarande överväga att använda tabell utrymmet per tabell för att undvika den maximala lagrings gränsen för system tabell utrymmet.

> [!NOTE]
> För tabeller med mindre storlek än 5 GB kan du använda systemets tabell utrymme 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Partitionera](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) tabellen när tabellen skapas om du har en mycket stor tabell som kan växa bortom 1 TB.

* Använd flera MySQL-servrar och sprid tabellerna över dessa servrar. Undvik att placera för många tabeller på en enskild server om du har runt 10000 tabeller eller mer. 

## <a name="next-steps"></a>Nästa steg
- [Bästa praxis för prestanda för Azure Database for MySQL](concept-performance-best-practices.md)
- [Bästa praxis för att övervaka din Azure Database for MySQL](concept-monitoring-best-practices.md)
