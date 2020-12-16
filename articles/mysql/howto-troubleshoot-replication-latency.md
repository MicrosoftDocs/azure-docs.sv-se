---
title: Felsöka replikeringsfördröjning – Azure Database for MySQL
description: Lär dig hur du felsöker replikeringsfördröjning genom att använda Azure Database for MySQL Läs repliker.
keywords: MySQL, felsöka, replikeringsfördröjning i sekunder
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/25/2020
ms.openlocfilehash: 30ac28ef996c42e99ebece27ec156777f0d033d2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587884"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Felsök replikeringsfördröjning i Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

Med funktionen [Läs replik](concepts-read-replicas.md) kan du replikera data från en Azure Database for MySQL-server till en skrivskyddad replik Server. Du kan skala ut arbets belastningar genom att dirigera Läs-och rapporterings frågor från programmet till replik servrar. Den här installationen minskar belastningen på käll servern. Det förbättrar också programmets övergripande prestanda och svars tid när den skalas. 

Repliker uppdateras asynkront med hjälp av MySQL-motorns interna binära logg (BinLog) teknik för filplacering-baserad replikering. Mer information finns i [Översikt över MySQL BinLog File position-based Replication Configuration](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

Fördröjningen för replikering på sekundär Läs repliker är beroende av flera faktorer. Dessa faktorer omfattar men är inte begränsade till: 

- Nätverks fördröjning.
- Transaktions volym på käll servern.
- Compute-nivån för käll servern och den sekundära Läs replik servern.
- Frågor som körs på käll servern och den sekundära servern. 

I den här artikeln får du lära dig hur du felsöker replikeringsfördröjning i Azure Database for MySQL. Du kommer också att förstå några vanliga orsaker till ökad replikeringsfördröjning på replik servrar.

> [!NOTE]
> Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="replication-concepts"></a>Metoder för replikering

När en binär logg aktive ras skriver käll servern genomförda transaktioner till den binära loggen. Den binära loggen används för replikering. Den är aktive rad som standard för alla nyligen etablerade servrar som har stöd för upp till 16 TB lagrings utrymme. På replik servrar körs två trådar på varje replik Server. En tråd är *IO-tråden* och den andra är SQL- *tråden*:

- IO-tråden ansluter till käll servern och begär uppdaterade binära loggar. Den här tråden tar emot de binära logg uppdateringarna. Uppdateringarna sparas på en replik server i en lokal logg som kallas *relä loggen*.
- SQL-tråden läser relä loggen och tillämpar sedan data ändringarna på replik servrarna.

## <a name="monitoring-replication-latency"></a>Övervaka replikeringsfördröjning

Azure Database for MySQL tillhandahåller mått för replikerings fördröjning i sekunder i [Azure Monitor](concepts-monitoring.md). Detta mått är bara tillgängligt på Läs replik servrar. Den beräknas av seconds_behind_master mått som är tillgängligt i MySQL. 

Om du vill förstå orsaken till en ökad replikeringsfördröjning ansluter du till replik servern med hjälp av [MySQL Workbench](connect-workbench.md) eller [Azure Cloud Shell](https://shell.azure.com). Kör sedan följande kommando.

>[!NOTE] 
> I din kod ersätter du exempel värden med replik serverns namn och administratörens användar namn. Administratörens användar namn kräver `@\<servername>` för Azure Database for MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Så här ser upplevelsen ut i Cloud Shell terminalen:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

Kör följande kommando i samma Cloud Shell terminal:

```
mysql> SHOW SLAVE STATUS;
```

Här är en typisk utmatning:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Övervaka replikeringsfördröjning":::


Utdata innehåller mycket information. Normalt behöver du bara fokusera på de rader som visas i följande tabell.

|Mått|Beskrivning|
|---|---|
|Slave_IO_State| Representerar IO-trådens aktuella status. Normalt är statusen "väntar på att huvud servern ska skickas" om käll servern (huvud servern) synkroniseras. En status som "anslutning till Master" anger att repliken förlorade anslutningen till käll servern. Se till att käll servern körs eller kontrol lera om en brand vägg blockerar anslutningen.|
|Master_Log_File| Representerar den binära logg filen som käll servern skriver till.|
|Read_Master_Log_Pos| Anger var käll servern skriver i den binära logg filen.|
|Relay_Master_Log_File| Representerar den binära logg filen som replik servern läser från käll servern.|
|Slave_IO_Running| Anger om IO-tråden körs. Värdet ska vara `Yes` . Om värdet är är `NO` replikeringen troligen bruten.|
|Slave_SQL_Running| Indikerar om SQL-tråden körs. Värdet ska vara `Yes` . Om värdet är är `NO` replikeringen troligen bruten.|
|Exec_Master_Log_Pos| Anger positionen för Relay_Master_Log_File som repliken använder. Om det finns en svars tid bör den här positions ordningen vara mindre än Read_Master_Log_Pos.|
|Relay_Log_Space|Anger den övre gränsen för relä logg storleken. Du kan kontrol lera storleken genom att fråga `SHOW GLOBAL VARIABLES` till exempel `relay_log_space_limit` .|
|Seconds_Behind_Master| Visar replikeringsfördröjning i sekunder.|
|Last_IO_Errno|Visar fel koden för IO-tråden, om sådan finns. Mer information om dessa koder finns i [referensen till MySQL-serverns fel meddelande](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Visar fel meddelandet i IO-tråden, om det finns några.|
|Last_SQL_Errno|Visar SQL-trådens felkod, om sådan finns. Mer information om dessa koder finns i [referensen till MySQL-serverns fel meddelande](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Visar fel meddelandet om SQL-tråd, om det finns några.|
|Slave_SQL_Running_State| Anger den aktuella SQL-trådens status. I det här läget `System lock` är normal. Det är också normalt att se status för `Waiting for dependent transaction to commit` . Denna status anger att repliken väntar på att käll servern ska uppdatera genomförda transaktioner.|

Om Slave_IO_Running är `Yes` och Slave_SQL_Running är `Yes` , fungerar replikeringen bra. 

Sedan kontrollerar du Last_IO_Errno, Last_IO_Error, Last_SQL_Errno och Last_SQL_Error.  I dessa fält visas fel numret och ett fel meddelande om det senaste felet som orsakade att SQL-tråden stoppades. Ett fel antal `0` och ett tomt meddelande betyder att det inte finns något fel. Undersök eventuella felvärden som inte är noll genom att kontrol lera felkoden i [fel meddelande referensen MySQL server](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Vanliga scenarier för svars tider med hög replikering

I följande avsnitt beskrivs scenarier där svars tiderna för hög replikering är vanliga.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Nätverks fördröjning eller hög processor förbrukning på käll servern

Om du ser följande värden, orsakas replikeringsfördröjning troligt vis av hög nätverks fördröjning eller hög processor förbrukning på käll servern. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

I det här fallet körs IO-tråden och väntar på käll servern. Käll servern har redan skrivit till binär logg fils nummer 20. Repliken har endast mottagit upp till fil nummer 10. De primära faktorerna för svars tider med hög replikering i det här scenariot är nätverks hastighet eller hög processor användning på käll servern.  

I Azure kan nätverks svars tiden inom en region vanligt vis mätas i millisekunder. Mellan regioner är latens från millisekunder till sekunder. 

I de flesta fall orsakas anslutnings fördröjningen mellan IO-trådar och käll servern av hög processor användning på käll servern. IO-trådarna bearbetas långsamt. Du kan identifiera det här problemet genom att använda Azure Monitor för att kontrol lera CPU-belastningen och antalet samtidiga anslutningar på käll servern.

Om du inte ser hög processor användning på käll servern kan problemet vara nätverks fördröjning. Om nätverks fördröjningen plötsligt är onormalt hög, kontrollerar du [sidan Azure-status](https://status.azure.com/status) för kända problem eller avbrott. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Tung burst-överföring av transaktioner på käll servern

Om du ser följande värden, orsakar en tung överföring av transaktioner på käll servern sannolikt replikeringsfördröjning. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Utdata visar att repliken kan hämta den binära loggen bakom käll servern. Men replikeringens IO-tråd indikerar att relä logg utrymmet redan är fullt. 

Nätverks hastigheten orsakar inte fördröjningen. Repliken försöker fånga upp. Men den uppdaterade binära logg storleken överskrider den övre gränsen för relä logg utrymmet. 

Om du vill felsöka det här problemet aktiverar du den [långsamma frågans logg](concepts-server-logs.md) på käll servern. Använd långsamma Query-loggar för att identifiera tids krävande transaktioner på käll servern. Justera sedan de identifierade frågorna för att minska svars tiden på servern. 

Replikeringsfördröjning för den här sorteringen orsakas vanligt vis av data belastningen på käll servern. När käll servrar har data inläsningar varje vecka eller månatlig, går det tyvärr inte att undvika svar på replikering. Replik servrarna kommer slutligen att fångas upp efter att data belastningen på käll servern har slutförts.


### <a name="slowness-on-the-replica-server"></a>Långsamma på replik servern

Om du observerar följande värden kan problemet vara på replik servern. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

I det här scenariot visar utdata att både IO-tråden och SQL-tråden fungerar bra. Repliken läser samma binära loggfil som käll servern skriver. En viss fördröjning på replik servern återspeglar dock samma transaktion från käll servern. 

I följande avsnitt beskrivs vanliga orsaker till den här typen av svars tid.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Ingen primär nyckel eller unik nyckel för en tabell

Azure Database for MySQL använder Row-baserad replikering. Käll servern skriver händelser till den binära loggen och registrerar ändringar i enskilda tabell rader. SQL-tråden replikerar sedan ändringarna till motsvarande tabell rader på replik servern. När en tabell saknar primär nyckel eller unik nyckel genomsöker SQL-tråden alla rader i mål tabellen för att tillämpa ändringarna. Den här genomsökningen kan orsaka replikeringsfördröjning.

I MySQL är primär nyckeln ett associerat index som säkerställer snabba frågeresultat eftersom det inte får innehålla NULL-värden. Om du använder InnoDB lagrings motor är tabell data fysiskt organiserade för att göra extremt snabba sökningar och sorteras baserat på den primära nyckeln. 

Vi rekommenderar att du lägger till en primär nyckel för tabeller i käll servern innan du skapar replik servern. Lägg till primära nycklar på käll servern och återskapa sedan Läs repliker för att hjälpa till att förbättra replikeringens svars tid.

Använd följande fråga för att ta reda på vilka tabeller som saknar primär nyckel på käll servern:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Tids krävande frågor på replik servern

Arbets belastningen på replik servern kan göra SQL-trådens fördröjning bakom IO-tråden. Tids krävande frågor på replik servern är en av de vanligaste orsakerna till svars tiden för hög replikering. Om du vill felsöka det här problemet aktiverar du den [långsamma frågans logg](concepts-server-logs.md) på replik servern. 

Långsamma frågor kan öka resurs användningen eller sakta ned servern så att repliken inte kan fångas upp med käll servern. I det här scenariot kan du justera de långsamma frågorna. Snabbare frågor förhindrar blockering av SQL-tråden och förbättrar replikeringens fördröjning avsevärt.


#### <a name="ddl-queries-on-the-source-server"></a>DDL-frågor på käll servern
På käll servern kan ett Data Definition Language-kommando (DDL) som [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) kan ta lång tid. Medan DDL-kommandot körs kan tusentals andra frågor köras parallellt på käll servern. 

När DDL replikeras, för att säkerställa databas konsekvens, kör MySQL-motorn DDL: en i en enda replikeringsrelation. Under den här uppgiften blockeras alla andra replikerade frågor och måste vänta tills DDL-åtgärden har slutförts på replik servern. Även DDL-åtgärder online orsakar denna fördröjning. DDL-åtgärder ökar replikeringsfördröjning.

Om du har aktiverat den [långsamma frågans logg](concepts-server-logs.md) på käll servern kan du identifiera fördröjnings problemet genom att söka efter ett DDL-kommando som kördes på käll servern. Genom att indexera, byta namn på och skapa, kan du använda den inplacerade algoritmen för ALTER TABLE. Du kan behöva kopiera tabell data och återskapa tabellen. 

Vanligt vis stöds samtidig DML för den inplacerade algoritmen. Men du kan ta ett exklusivt lås för metadata i tabellen när du förbereder och kör åtgärden. För CREATE INDEX-instruktionen kan du använda-ALGORITMen och låsa för att påverka metoden för tabell kopiering och nivån av samtidighet för läsning och skrivning. Du kan fortfarande förhindra DML-åtgärder genom att lägga till ett full text index eller RUMS index. 

I följande exempel skapas ett index med hjälp av algoritm-och lås satser.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

För en DDL-instruktion som kräver ett lås kan du tyvärr inte undvika replikeringsfördröjning. Du kan minska de potentiella effekterna genom att göra dessa typer av DDL-åtgärder under låg belastnings tider, till exempel under natten.

#### <a name="downgraded-replica-server"></a>Degraderad replik Server

I Azure Database for MySQL använder Läs repliker samma server konfiguration som käll servern. Du kan ändra replik Server konfigurationen när den har skapats. 

Om replik servern är nedgraderad kan arbets belastningen förbruka fler resurser, vilket i sin tur kan leda till replikeringsfördröjning. Identifiera det här problemet genom att använda Azure Monitor för att kontrol lera replik serverns processor-och minnes användning. 

I det här scenariot rekommenderar vi att du behåller replik serverns konfiguration på värden som är lika med eller större än käll serverns värden. Med den här konfigurationen kan repliken hållas på käll servern.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Förbättra replikeringsfördröjning genom att justera käll serverns parametrar

I Azure Database for MySQL optimeras replikeringen som standard för att köras med parallella trådar på repliker. När hög concurrency-arbetsbelastningar på käll servern gör att replik servern hamnar bakom, kan du förbättra replikeringens fördröjning genom att konfigurera parametern binlog_group_commit_sync_delay på käll servern. 

Parametern binlog_group_commit_sync_delay styr hur många mikrosekunder den binära loggen ska vänta innan den binära logg filen synkroniseras. Fördelen med den här parametern är att i stället för att omedelbart tillämpa varje genomförd transaktion skickar käll servern de binära logg uppdateringarna i bulk. Den här fördröjningen minskar IO på repliken och förbättrar prestandan. 

Det kan vara praktiskt att ange binlog_group_commit_sync_delay parametern till 1000 eller så. Övervaka sedan replikeringsfördröjning. Ange den här parametern försiktigt och Använd den endast för arbets belastningar med hög samtidighet. 

> [!IMPORTANT] 
> I replik servern rekommenderas binlog_group_commit_sync_delay parameter vara 0. Detta rekommenderas eftersom till skillnad från käll servern, men replik servern inte har hög samtidighet och ökning av värdet för binlog_group_commit_sync_delay på replik servern kan oavsiktligt orsaka att fördröjningen för replikering ökar.

För arbets belastningar med låg concurrency som innehåller många singleton-transaktioner kan binlog_group_commit_sync_delays inställningen öka svars tiden. Svars tiden kan öka eftersom IO-tråden väntar på Mass binära logg uppdateringar, även om bara några få transaktioner har genomförts. 

## <a name="next-steps"></a>Nästa steg
Kolla in [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
