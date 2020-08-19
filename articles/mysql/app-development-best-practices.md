---
title: Metod tips för App-utveckling – Azure Database for MySQL
description: Lär dig mer om metod tips när du skapar en app med Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259258"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Metod tips för att skapa program med Azure Database for MySQL 

Här följer några metod tips som hjälper dig att bygga moln klara program med hjälp av Azure Database for MySQL som kan minska utvecklings tiden för ditt program. 

## <a name="application-and-database-resource-configuration"></a>Resurs konfiguration för program och databas

### <a name="application-and-database-in-the-same-region"></a>Program och databas i samma region
Se till att **alla dina beroenden finns i samma region**  när du distribuerar ditt program i Azure. Genom att sprida instanser över regioner eller tillgänglighets zoner skapas nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 

### <a name="keep-your-mysql-server-secure"></a>Skydda MySQL-servern
MySQL-servern bör konfigureras för att vara [säker](https://docs.microsoft.com/azure/mysql/concepts-security) och inte tillgänglig offentligt. Använd något av följande alternativ för att skydda servern: 
- [Brand Väggs regler](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) eller
- [Virtuella nätverk](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) eller 
- [Private Link](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

För säkerhet måste du alltid ansluta till MySQL-servern via **SSL** och Konfigurera MySQL-servern och ditt program för att använda **TLS 1.2**. Se [så här konfigurerar du SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Justera dina Server parametrar
För läsnings intensiva arbets belastningar kan du justera dessa parametrar, "tmp_table_size och max_heap_table_size" hjälper till att optimera för bättre prestanda. Om du vill beräkna de värden som krävs för tmp_table_size och max_heap_table_size tittar du på de totala minnes värdena per anslutning och bas minnet. Summan av minnes parametrar per anslutning, exklusive tmp_table_size, kombinerat med de grundläggande minnes kontona för det totala minnet på servern.

Om du vill beräkna den största möjliga storleken på tmp_table_size och max_heap_table_size använder du följande formel:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Totalt minne anger den totala mängden minne som servern har för den virtuella kärnor som har tillhandahållits.  Till exempel, i en Generell användning 2-vCore Azure Database for MySQL-server, blir det totala minnet 5 GB * 2.  Mer information om minne för varje nivå finns i [pris nivå](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) dokumentationen.
> I bas minnet anges de minnesmoduler, t. ex. query_cache_size och innodb_buffer_pool_size, som MySQL initierar och allokerar vid Server start.  Per anslutnings minne, t. ex. sort_buffer_size och join_buffer_size, är minne som tilldelas bara när en fråga kräver det.

### <a name="create-a-non-admin-user"></a>Skapa en användare som inte är administratör 
[Skapa användare som inte är administratörer](https://docs.microsoft.com/azure/mysql/howto-create-users) för varje databas. Normalt identifieras användar namnen som databas namn.

### <a name="resetting-your-password"></a>Återställer lösen ordet
Du kan [återställa ditt lösen ord](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) för MySQL-servern med hjälp av Azure Portal. 

Om du återställer Server lösen ordet för en produktions databas kan du ta bort programmet. Det är ett utmärkt mönster att återställa lösen ordet för produktions arbets belastningar vid låg belastnings tider för att minimera påverkan på dina program slutanvändare.

## <a name="performance-and-resiliency"></a>Prestanda och återhämtning 
Här följer några verktyg och mönster som du kan använda för att felsöka prestanda problem med programmet.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Aktivera långsam fråga loggar identifiera prestanda problem
Du kan aktivera [långsamma fråge loggar](https://docs.microsoft.com/azure/mysql/concepts-server-logs) och [gransknings loggar](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) på servern. Analys av långsamma frågeuttryck kan hjälpa dig att identifiera Flask halsar i prestanda för fel sökning. 

Gransknings loggar är också tillgängliga via Azure-diagnostikloggar i Azure Monitor loggar, Event Hubs och lagrings kontot också. Se [fel sökning av problem med frågans prestanda](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>Använd anslutningspoolen
Hantering av databas anslutningar kan ha en betydande inverkan på programmets prestanda som helhet. För att optimera prestanda måste du minska antalet gånger som anslutningarna upprättas och tiden för att upprätta anslutningar i nyckel kods Sök vägar.  Använd [anslutningspoolen](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) för att ansluta till Azure Database for MySQL för att förbättra återhämtning och prestanda. 

[ProxySQL](https://proxysql.com/), som är en anslutningspool, kan användas effektivt för att hantera anslutningar. Att använda en anslutningspool kan minska inaktiva anslutningar och återanvända befintliga anslutningar hjälper till att undvika problem. Läs mer i [Konfigurera ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Försök igen med logik för att hantera tillfälliga fel
Ditt program kan uppleva [tillfälliga fel](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) där anslutningar till databasen släpps eller förloras tillfälligt. I sådana situationer är servern igång efter en och två nya försök om 5-10 sekunder. 

Ett lämpligt mönster att följa med omförsök är att vänta i 5 sekunder innan ditt första försök och sedan följa varje nytt försök genom att öka vänte tiden till 60 sekunder. Begränsa det maximala antalet återförsök som programmet anser att åtgärden misslyckades, så att du kan undersöka dem ytterligare. Mer information finns i [Felsöka anslutnings fel](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Aktivera Läs replikering för att minimera redundans
Du kan använda [datareplikering](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) för failover-scenarier. När du använder Läs repliker sker ingen automatisk redundans mellan huvud-och replik servrar. Du kommer att märka en fördröjning mellan huvud servern och repliken eftersom replikeringen är asynkron. Nätverks fördröjningen kan påverkas av många faktorer, t. ex. hur stor belastningen på huvud servern som körs på huvud servern och fördröjningen mellan data Center. I de flesta fall varierar replikfördröjning mellan några sekunder och några minuter.

## <a name="database-deployment"></a>Databas distribution 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Konfigurera Azure Database för MySQL-uppgift i din CI/CD distributions pipeline
Ibland skulle du behöva distribuera ändringar i databasen. I sådana fall kan du skapa Använd kontinuerlig integrering (CI) och kontinuerlig leverans (CD) via [Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/) och använda en aktivitet för [MySQL-servern](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) för att uppdatera databasen genom att köra ett anpassat skript mot databasen.

### <a name="manual-database-deployment"></a>Manuell databas distribution 
Under den manuella databas distributionen är här ett utmärkt mönster att följa för att minimera stillestånds tiden eller minska risken för misslyckad distribution: 

1. Skapa en kopia av produktions databasen i en ny databas med [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) eller [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 
2. Uppdatera den nya databasen med dina nya schema ändringar eller uppdateringar som krävs för din databas. 
3. Lägg till produktions databasen i skrivskyddat tillstånd. Du bör inte ha Skriv åtgärder i produktions databasen förrän distributionen är klar. 
4. Testa ditt program med den nyligen uppdaterade databasen från steg 1.
5. Distribuera dina program ändringar och se till att programmet nu använder den nya databasen som har de senaste uppdateringarna. 
6. Behåll den gamla produktions databasen så att du kan återställa ändringarna. Du kan sedan utvärdera för att antingen ta bort den gamla produktions databasen eller exportera den i Azure Storage om det behövs. 

>[!NOTE]
>  - Om programmet liknar eCommerce-appen där du kanske inte kan placera det i skrivskyddat tillstånd, distribuera ändringarna direkt i produktions databasen efter att du har gjort en säkerhets kopia.  De här-ändring bör ske under låg belastnings tid med låg trafik till appen för att minimze inverkan på det sätt som vissa användare kan uppleva en misslyckad begäran. 
>  - Kontrol lera att program koden även hanterar misslyckade begär Anden.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>Använd MySQLs interna mått för att se om din arbets belastning överskrider den temporära tabell storleken i minnet
Med en Läs intensiv arbets belastning kan frågor som körs mot din MySQL-server överskrida de temporära InMemory-tabell storlekarna. Det kan orsaka att servern växlar till att skriva temporära tabeller till disken som påverkar programmets prestanda. Ta reda på om servern skrivs till disk på grund av att den tillfälliga tabell storleken är större genom att titta på följande mått:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
Created_tmp_disk_tables måttet anger hur många tabeller som skapades på disken, medan created_tmp_table mått visar hur många temporära tabeller som måste bildas i minnet för din arbets belastning. För att avgöra om en speciell fråga ska använda temporära tabeller, kör du en förklaring på frågan. I kolumnen extra visas "Använd temporär" om frågan ska köras med tillfälliga tabeller.

Om du vill beräkna procent andelen av din arbets belastning med frågor som tar med diskar, använder du värdena i följande formel:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Helst bör den här procent andelen vara mindre 25%. Om du ser att procent andelen är 25% eller högre, rekommenderar vi att du ändrar två server parametrar, tmp_table_size och max_heap_table_si


## <a name="database-schema-and-queries"></a>Databasschemat och-frågor

Här följer några tips och knep att tänka på när du skapar ditt databas schema och dina frågor.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Använd alltid rätt datatyp för tabell kolumnerna
Genom att använda rätt data typer baserat på den typ av data som du vill lagra kan du optimera lagringen och minska eventuella fel som kan uppstå på grund av felaktiga data typer.

### <a name="use-indexes"></a>Använda index
Du kan använda index för att undvika långsamma frågor. Index kan hjälpa dig att snabbt hitta rader med vissa kolumner. Se [hur du använder index i MySQL](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>FÖRKLARA dina URVALs frågor
Använd [förklaringen](https://dev.mysql.com/doc/refman/8.0/en/explain.html) för att få insikter om vad MySQL gör för att köra frågan. Detta kan hjälpa dig att identifiera Flask halsar eller problem med din fråga. Se [hur du använder förklaring för att profilera frågor om prestanda](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).


