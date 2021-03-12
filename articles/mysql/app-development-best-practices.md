---
title: Metod tips för App-utveckling – Azure Database for MySQL
description: Lär dig mer om metod tips för att skapa en app med hjälp av Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611971"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Metodtips för att skapa ett program med Azure Database for MySQL 

Här följer några metod tips som hjälper dig att bygga ett moln klart program med hjälp av Azure Database for MySQL. Dessa metod tips kan minska utvecklings tiden för din app. 

## <a name="configuration-of-application-and-database-resources"></a>Konfiguration av program-och databas resurser

### <a name="keep-the-application-and-database-in-the-same-region"></a>Behåll programmet och databasen i samma region
Se till att alla dina beroenden finns i samma region när du distribuerar ditt program i Azure. Genom att sprida instanser över regioner eller tillgänglighets zoner skapas nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 

### <a name="keep-your-mysql-server-secure"></a>Skydda MySQL-servern
Konfigurera MySQL-servern så att den är [säker](./concepts-security.md) och inte tillgänglig offentligt. Använd något av dessa alternativ för att skydda servern: 
- [Brandväggsregler](./concepts-firewall-rules.md)
- [Virtuella nätverk](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

För säkerhet måste du alltid ansluta till MySQL-servern via SSL och Konfigurera MySQL-servern och ditt program för att använda TLS 1,2. Se [så här konfigurerar du SSL/TLS](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Justera dina Server parametrar
För att finjustera Server parametrar med Läs-och hög `tmp_table_size` belastning `max_heap_table_size` kan du optimera för bättre prestanda. Om du vill beräkna de värden som krävs för dessa variabler tittar du på de totala minnes värdena per anslutning och bas minnet. Summan av minnes parametrarna per anslutning, exklusive `tmp_table_size` , tillsammans med de grundläggande minnes kontona för det totala minnet på servern.

Om du vill beräkna den största möjliga storleken för `tmp_table_size` och `max_heap_table_size` använder du följande formel:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Totalt minne anger den totala mängden minne som servern har för den etablerade virtuella kärnor.  Till exempel är det totala minnet 5 GB * 2 i en Generell användning Azure Database for MySQL server med två vCore. Du hittar mer information om minne för varje nivå i [pris nivå](./concepts-pricing-tiers.md) dokumentationen.
>
> I bas minnet anges de minnesmoduler, till exempel `query_cache_size` och `innodb_buffer_pool_size` , som MySQL initierar och allokerar vid Server start. Minne per anslutning, till exempel `sort_buffer_size` och `join_buffer_size` , är minne som tilldelas bara när en fråga behöver det.

### <a name="create-non-admin-users"></a>Skapa användare som inte är administratörer 
[Skapa användare som inte är administratörer](./howto-create-users.md) för varje databas. Normalt identifieras användar namnen som databas namn.

### <a name="reset-your-password"></a>Återställa lösenordet
Du kan [återställa ditt lösen ord](./howto-create-manage-server-portal.md#update-admin-password) för MySQL-servern med hjälp av Azure Portal. 

Om du återställer Server lösen ordet för en produktions databas kan du ta bort programmet. Det är en bra idé att återställa lösen ordet för produktions arbets belastningar vid låg belastnings tider för att minimera påverkan på programmets användare.

## <a name="performance-and-resiliency"></a>Prestanda och återhämtning 
Här följer några verktyg och metoder som du kan använda för att felsöka prestanda problem med programmet.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Aktivera långsamma Query-loggar för att identifiera prestanda problem
Du kan aktivera [långsamma fråge loggar](./concepts-server-logs.md) och [gransknings loggar](./concepts-audit-logs.md) på servern. Analys av långsamma frågeuttryck kan hjälpa dig att identifiera Flask halsar i prestanda för fel sökning. 

Gransknings loggar är också tillgängliga via Azure-diagnostik loggar i Azure Monitor loggar, Azure Event Hubs och lagrings konton. Se [fel sökning av problem med frågans prestanda](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Använd anslutningspoolen
Hantering av databas anslutningar kan ha en betydande inverkan på programmets prestanda som helhet. För att optimera prestanda måste du minska antalet gånger som anslutningarna upprättas och tiden för att upprätta anslutningar i nyckel kods Sök vägar. Använd [anslutningspoolen](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) för att ansluta till Azure Database for MySQL för att förbättra återhämtning och prestanda. 

Du kan använda [ProxySQL](https://proxysql.com/) -anslutningspoolen för att effektivt hantera anslutningar. Att använda en anslutningspool kan minska inaktiva anslutningar och återanvända befintliga anslutningar, vilket bidrar till att undvika problem. Mer information finns i [så här konfigurerar du ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Försök igen med logik för att hantera tillfälliga fel
Ditt program kan uppleva [tillfälliga fel](./concepts-connectivity.md#handling-transient-errors) där anslutningar till databasen släpps eller försvinner tillfälligt. I sådana situationer är servern igång efter en och två nya försök i 5 till 10 sekunder. 

En bra idé är att vänta i 5 sekunder innan ditt första försök. Följ sedan varje nytt försök genom att öka vänte tiden gradvis, upp till 60 sekunder. Begränsa det maximala antalet återförsök som programmet anser att åtgärden misslyckades, så att du kan undersöka dem ytterligare. Mer information finns i [Felsöka anslutnings fel](./howto-troubleshoot-common-connection-issues.md) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Aktivera Läs replikering för att minimera redundans
Du kan använda [datareplikering](./howto-data-in-replication.md) för scenarier med växling vid fel. När du använder Läs repliker sker ingen automatisk redundans mellan käll-och replik servrar. 

Du ser en fördröjning mellan källan och repliken eftersom replikeringen är asynkron. Nätverks fördröjning kan påverkas av många faktorer, t. ex. storleken på arbets belastningen som körs på käll servern och fördröjningen mellan data Center. I de flesta fall är replik fördröjningen från några sekunder till några minuter.

## <a name="database-deployment"></a>Databasdistribution 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Konfigurera en Azure Database för MySQL-aktivitet i din CI/CD distributions pipeline
Ibland måste du distribuera ändringar i databasen. I sådana fall kan du använda kontinuerlig integrering (CI) och kontinuerlig leverans (CD) via [Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/) och använda en aktivitet för [MySQL-servern](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) för att uppdatera databasen genom att köra ett anpassat skript mot den.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Använd en effektiv process för manuell databas distribution 
Under en manuell databas distribution följer du dessa steg för att minimera stillestånds tiden eller minska risken för misslyckad distribution: 

1. Skapa en kopia av en produktions databas i en ny databas med hjälp av [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) eller [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html). 
2. Uppdatera den nya databasen med dina nya schema ändringar eller uppdateringar som krävs för din databas. 
3. Flytta produktions databasen i skrivskyddat läge. Du bör inte ha Skriv åtgärder i produktions databasen förrän distributionen är klar. 
4. Testa ditt program med den nyligen uppdaterade databasen från steg 1.
5. Distribuera dina program ändringar och se till att programmet nu använder den nya databasen som har de senaste uppdateringarna. 
6. Behåll den gamla produktions databasen så att du kan återställa ändringarna. Du kan sedan utvärdera för att antingen ta bort den gamla produktions databasen eller exportera den Azure Storage vid behov. 

>[!NOTE]
>Om programmet är som en app för e-handel och du inte kan försätta det i skrivskyddat läge, distribuerar du ändringarna direkt i produktions databasen när du har gjort en säkerhets kopia. De här-ändring bör ske under låg belastnings tider med låg trafik till appen för att minimera påverkan, eftersom vissa användare kan uppleva misslyckade förfrågningar. 
>
>Kontrol lera att program koden även hanterar misslyckade begär Anden.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Använd MySQLs interna mått för att se om din arbets belastning överskrider den temporära tabell storleken i minnet
Med en Läs intensiv arbets belastning kan frågor som körs mot din MySQL-server överskrida de temporära tabell storlekarna i minnet. En Read-stor arbets belastning kan orsaka att servern växlar till att skriva temporära tabeller till disken, vilket påverkar programmets prestanda. Ta reda på om servern skrivs till disk på grund av att den tillfälliga tabell storleken är större genom att titta på följande mått:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`Måttet anger hur många tabeller som skapades på disken. `created_tmp_table`Måttet visar hur många temporära tabeller som måste bildas i minnet, med tanke på arbets belastningen. För att avgöra om en speciell fråga ska använda temporära tabeller, kör du [förklaringen](https://dev.mysql.com/doc/refman/8.0/en/explain.html) i frågan. Informationen i `extra` kolumnen anger `Using temporary` om frågan ska köras med tillfälliga tabeller.

Om du vill beräkna procent andelen av din arbets belastning med frågor som tar med diskar, använder du mått värden i följande formel:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Helst bör den här procent andelen vara mindre 25%. Om du ser att procent andelen är 25% eller högre, rekommenderar vi att du ändrar två server parametrar, tmp_table_size och max_heap_table_size.

## <a name="database-schema-and-queries"></a>Databasschemat och-frågor

Här följer några tips att tänka på när du skapar ditt databas schema och dina frågor.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Använd rätt datatyp för tabell kolumnerna
Genom att använda rätt datatyp baserat på den typ av data som du vill lagra kan du optimera lagringen och minska antalet fel som kan uppstå på grund av felaktiga data typer.

### <a name="use-indexes"></a>Använda index
Du kan använda index för att undvika långsamma frågor. Index kan hjälpa dig att snabbt hitta rader med vissa kolumner. Se [hur du använder index i MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Använd förklaring för dina URVALs frågor
Använd `EXPLAIN` instruktionen för att få insikter om vad MySQL gör för att köra frågan. Det kan hjälpa dig att identifiera Flask halsar eller problem med din fråga. Se [hur du använder förklaring för att profilera frågor om prestanda](./howto-troubleshoot-query-performance.md).