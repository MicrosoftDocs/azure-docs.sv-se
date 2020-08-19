---
title: Server parametrar – Azure Database for MySQL
description: Det här avsnittet innehåller rikt linjer för hur du konfigurerar Server parametrar i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: e7ca86d0146f05d5171d5eae18aac81d75122bcc
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258549"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Server parametrar i Azure Database for MySQL

Den här artikeln innehåller överväganden och rikt linjer för att konfigurera server parametrar i Azure Database for MySQL.

## <a name="what-are-server-parameters"></a>Vad är Server parametrar? 

MySQL-motorn innehåller många olika servervariabler/parametrar som kan användas för att konfigurera och finjustera motor beteendet. Vissa parametrar kan ställas in dynamiskt under körning medan andra är "statiska", vilket kräver en server omstart för att kunna användas.

Azure Database for MySQL visar möjligheten att ändra värdet för olika MySQL server-parametrar med hjälp av [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md)och [PowerShell](./howto-configure-server-parameters-using-powershell.md) för att matcha din arbets belastnings behov.

## <a name="configurable-server-parameters"></a>Konfigurerbara Server parametrar

Listan över Server parametrar som stöds växer ständigt. Använd fliken Server parametrar i Azure Portal om du vill visa en fullständig lista och konfigurera server parametrar värden.

Se följande avsnitt för att lära dig mer om gränserna för de många ofta uppdaterade Server parametrarna. Gränserna bestäms av pris nivån och virtuella kärnor för servern.

### <a name="thread-pools"></a>Trådbaserade pooler

MySQL tilldelar traditionellt en tråd för varje klient anslutning. När antalet samtidiga användare växer, finns det en motsvarande minskning av prestanda. Många aktiva trådar kan påverka prestandan avsevärt på grund av ökad kontext växling, tråd konkurrens och felaktig plats för CPU-cacheminnen.

Trådbaserade pooler som är en funktion på Server sidan och som är skilda från anslutningspoolen, maximerar prestanda genom att introducera en dynamisk pool av arbets trådar som kan användas för att begränsa antalet aktiva trådar som körs på servern och minimerar tråd omsättningen. På så sätt ser du till att en burst-anslutning inte medför att servern tar slut på resurser eller kraschar med ett slut på minnes fel. Trådbaserade pooler är mest effektiva för korta frågor och CPU-intensiva arbets belastningar, till exempel OLTP-arbetsbelastningar.

Mer information om Thread-pooler finns [i Introduktion till thread-pooler i Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> Funktionen Thread pool stöds inte för MySQL 5,6-versionen. 

### <a name="configuring-the-thread-pool"></a>Konfigurera trådpoolen
Om du vill aktivera trådpoolen uppdaterar du `thread_handling` Server parametern till "pool-of-threads". Den här parametern är som standard inställd på `one-thread-per-connection` , vilket innebär att MySQL skapar en ny tråd för varje ny anslutning. Observera att detta är en statisk parameter och kräver att servern startas om.

Du kan också konfigurera det högsta och lägsta antalet trådar i poolen genom att ange följande Server parametrar: 
- `thread_pool_max_threads`: Det här värdet säkerställer att det inte finns fler än det här antalet trådar i poolen.
- `thread_pool_min_threads`: Det här värdet anger antalet trådar som reserveras även efter att anslutningarna har avslut ATS.

Om du vill förbättra prestanda problem med korta frågor i trådpoolen kan du med Azure Database for MySQL aktivera batch-körning, där i stället för att återgå till trådpoolen omedelbart efter att en fråga har körts, så fortsätter trådarna vara aktiva under en kort tid för att vänta på nästa fråga via den här anslutningen. Tråden kör sedan frågan snabbt och när den är klar, väntar på nästa, tills den totala tids förbrukningen för den här processen överskrider ett tröskelvärde. Beteendet för batch-körning fastställs med följande Server parametrar:  

-  `thread_pool_batch_wait_timeout`: Det här värdet anger hur lång tid en tråd väntar på att en annan fråga ska bearbetas.
- `thread_pool_batch_max_time`: Det här värdet anger den längsta tid som en tråd kommer att upprepa cykeln för frågekörningen och väntar på nästa fråga.

> [!IMPORTANT]
> Testa trådpoolen innan du aktiverar den i produktion. 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servrar som har stöd för upp till 4 TB lagring

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Generell användning|2|3758096384|134217728|3758096384|
|Generell användning|4|8053063680|134217728|8053063680|
|Generell användning|8|16106127360|134217728|16106127360|
|Generell användning|16|32749125632|134217728|32749125632|
|Generell användning|32|66035122176|134217728|66035122176|
|Generell användning|64|132070244352|134217728|132070244352|
|Minnesoptimerad|2|7516192768|134217728|7516192768|
|Minnesoptimerad|4|16106127360|134217728|16106127360|
|Minnesoptimerad|8|32212254720|134217728|32212254720|
|Minnesoptimerad|16|65498251264|134217728|65498251264|
|Minnesoptimerad|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Servrar stöder upp till 16 TB lagring

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Generell användning|2|7516192768|134217728|7516192768|
|Generell användning|4|16106127360|134217728|16106127360|
|Generell användning|8|32212254720|134217728|32212254720|
|Generell användning|16|65498251264|134217728|65498251264|
|Generell användning|32|132070244352|134217728|132070244352|
|Generell användning|64|264140488704|134217728|264140488704|
|Minnesoptimerad|2|15032385536|134217728|15032385536|
|Minnesoptimerad|4|32212254720|134217728|32212254720|
|Minnesoptimerad|8|64424509440|134217728|64424509440|
|Minnesoptimerad|16|130996502528|134217728|130996502528|
|Minnesoptimerad|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` kan bara uppdateras i Generell användning och minnesoptimerade pris nivåer.

MySQL lagrar tabellen InnoDB i olika tabell utrymmen baserat på den konfiguration du angav när tabellen skapades. [Systemets tabell utrymme](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) är lagrings utrymmet för data ord listan InnoDB. Ett tabell namn för en [fil per tabell](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) innehåller data och index för en enskild InnoDB-tabell och lagras i fil systemet i en egen datafil. Detta beteende styrs av `innodb_file_per_table` Server parametern. Inställningen `innodb_file_per_table` `OFF` gör att InnoDB skapar tabeller i System register utrymmet. Annars skapar InnoDB tabeller i tabell utrymmen per tabell.

Azure Database for MySQL stöder högst **1 TB**i en enskild datafil. Om databas storleken är större än 1 TB bör du skapa tabellen i [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabell utrymme. Om du har en enskild tabell storlek som är större än 1 TB bör du använda partitionstabellen.

### <a name="join_buffer_size"></a>join_buffer_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Basic|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Generell användning|2|262144|128|268435455|
|Generell användning|4|262144|128|536870912|
|Generell användning|8|262144|128|1073741824|
|Generell användning|16|262144|128|2147483648|
|Generell användning|32|262144|128|4294967295|
|Generell användning|64|262144|128|4294967295|
|Minnesoptimerad|2|262144|128|536870912|
|Minnesoptimerad|4|262144|128|1073741824|
|Minnesoptimerad|8|262144|128|2147483648|
|Minnesoptimerad|16|262144|128|4294967295|
|Minnesoptimerad|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minvärde**|**Maxvärde**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Generell användning|2|300|10|600|
|Generell användning|4|625|10|1250|
|Generell användning|8|1250|10|2500|
|Generell användning|16|2500|10|5000|
|Generell användning|32|5000|10|10000|
|Generell användning|64|10000|10|20000|
|Minnesoptimerad|2|625|10|1250|
|Minnesoptimerad|4|1250|10|2500|
|Minnesoptimerad|8|2500|10|5000|
|Minnesoptimerad|16|5000|10|10000|
|Minnesoptimerad|32|10000|10|20000|

När anslutningar överskrider gränsen kan följande fel meddelande visas:
> FEL 1040 (08004): för många anslutningar

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som ProxySQL för att effektivt hantera anslutningar.

Att skapa nya klient anslutningar till MySQL tar tid och när de har upprättats, använder dessa anslutningar databas resurser, även när de är inaktiva. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information om hur du konfigurerar ProxySQL finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL är ett community-verktyg med öppen källkod. Den stöds av Microsoft på bästa möjliga sikt. För att få produktions support med auktoritativ vägledning kan du utvärdera och nå ut till [ProxySQL produkt support](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Basic|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Generell användning|2|16777216|16384|268435455|
|Generell användning|4|16777216|16384|536870912|
|Generell användning|8|16777216|16384|1073741824|
|Generell användning|16|16777216|16384|2147483648|
|Generell användning|32|16777216|16384|4294967295|
|Generell användning|64|16777216|16384|4294967295|
|Minnesoptimerad|2|16777216|16384|536870912|
|Minnesoptimerad|4|16777216|16384|1073741824|
|Minnesoptimerad|8|16777216|16384|2147483648|
|Minnesoptimerad|16|16777216|16384|4294967295|
|Minnesoptimerad|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

Frågesyntaxen är inaktive rad som standard. Konfigurera-parametern om du vill aktivera Query Cache `query_cache_type` . 

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> Frågesyntaxen är föråldrad från MySQL-5.7.20 och har tagits bort i MySQL 8,0

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|* * Max värde * *|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Basic|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Generell användning|2|0|0|16777216|
|Generell användning|4|0|0|33554432|
|Generell användning|8|0|0|67108864|
|Generell användning|16|0|0|134217728|
|Generell användning|32|0|0|134217728|
|Generell användning|64|0|0|134217728|
|Minnesoptimerad|2|0|0|33554432|
|Minnesoptimerad|4|0|0|67108864|
|Minnesoptimerad|8|0|0|134217728|
|Minnesoptimerad|16|0|0|134217728|
|Minnesoptimerad|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Lower_case_table_name har angetts till 1 som standard och du kan uppdatera den här parametern i MySQL 5,6 och MySQL 5,7

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) .

> [!NOTE]
> I MySQL 8,0 är lower_case_table_name inställd på 1 som standard och du kan inte ändra den.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Om du får ett fel som liknar "rad storleken är för stor (> 8126)" kanske du vill inaktivera parametern **innodb_strict_mode**. Det går inte att ändra server parametern **innodb_strict_mode** globalt på server nivå eftersom data storleken på raden är större än 8k, men data trunkeras utan ett fel som leder till potentiell data förlust. Vi rekommenderar att du ändrar schemat så att det passar sid storleks gränsen. 

Den här parametern kan ställas in på en sessionsnyckel med `init_connect` . Om du vill ange **innodb_strict_mode** på sessionsstatus, se [inställnings parameter som inte visas](https://docs.microsoft.com/azure/mysql/howto-server-parameters#setting-parameters-not-listed).

> [!NOTE]
> Om du har en Läs replik Server avbryts replikeringen om du anger **innodb_strict_mode** till av på sessions nivå på en huvud server. Vi rekommenderar att du håller parametern inställt på av om du har Läs repliker.

### <a name="sort_buffer_size"></a>sort_buffer_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Basic|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Generell användning|2|524288|32768|4194304|
|Generell användning|4|524288|32768|8388608|
|Generell användning|8|524288|32768|16777216|
|Generell användning|16|524288|32768|33554432|
|Generell användning|32|524288|32768|33554432|
|Generell användning|64|524288|32768|33554432|
|Minnesoptimerad|2|524288|32768|8388608|
|Minnesoptimerad|4|524288|32768|16777216|
|Minnesoptimerad|8|524288|32768|33554432|
|Minnesoptimerad|16|524288|32768|33554432|
|Minnesoptimerad|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Basic|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Generell användning|2|16777216|1024|67108864|
|Generell användning|4|16777216|1024|134217728|
|Generell användning|8|16777216|1024|268435456|
|Generell användning|16|16777216|1024|536870912|
|Generell användning|32|16777216|1024|1073741824|
|Generell användning|64|16777216|1024|1073741824|
|Minnesoptimerad|2|16777216|1024|134217728|
|Minnesoptimerad|4|16777216|1024|268435456|
|Minnesoptimerad|8|16777216|1024|536870912|
|Minnesoptimerad|16|16777216|1024|1073741824|
|Minnesoptimerad|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Vid den första distributionen innehåller en Azure för MySQL-server system tabeller för tids zons information, men tabellerna fylls inte i. Du kan fylla i tids zons tabellerna genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som MySQL kommando rad eller MySQL Workbench. Mer information om hur du anropar den lagrade proceduren finns i [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -eller [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -artiklarna och ställer in globala eller sessionsbaserade tids zoner.

## <a name="non-configurable-server-parameters"></a>Server parametrar som inte går att konfigurera

Följande Server parametrar kan inte konfigureras i tjänsten:

|**Parameter**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256|
|innodb_log_files_in_group|2|

Andra variabler som inte anges här anges som standardvärden för MySQL-standardvärden. Se MySQL-dokumenten för versionerna [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)och [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) för standardvärdena. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar egenskaper för servern med hjälp av Azure Portal](./howto-server-parameters.md)
- Lär dig hur du [konfigurerar egenskaper för servrar med Azure CLI](./howto-configure-server-parameters-using-cli.md)
- Lär dig hur du [konfigurerar egenskaper för servern med hjälp av PowerShell](./howto-configure-server-parameters-using-powershell.md)
