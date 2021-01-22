---
title: Server parametrar – Azure Database for MariaDB
description: Det här avsnittet innehåller rikt linjer för hur du konfigurerar Server parametrar i Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: ca1acfd6891c9389363fceb7eccec6f460537399
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664154"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Server parametrar i Azure Database for MariaDB

Den här artikeln innehåller överväganden och rikt linjer för att konfigurera server parametrar i Azure Database for MariaDB.

## <a name="what-are-server-parameters"></a>Vad är Server parametrar? 

MariaDB-motorn innehåller många olika servervariabler/parametrar som kan användas för att konfigurera och finjustera motor beteendet. Vissa parametrar kan ställas in dynamiskt under körning medan andra är "statiska", vilket kräver en server omstart för att kunna användas.

Azure Database for MariaDB visar möjligheten att ändra värdet för olika parametrar för MariaDB-servern med hjälp av [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-cli.md)och [PowerShell](./howto-configure-server-parameters-using-powershell.md) för att matcha din arbets belastnings behov.

## <a name="configurable-server-parameters"></a>Konfigurerbara Server parametrar

Listan över Server parametrar som stöds växer ständigt. Använd fliken Server parametrar i Azure Portal om du vill visa en fullständig lista och konfigurera server parametrar värden.

Se följande avsnitt för att lära dig mer om gränserna för de många ofta uppdaterade Server parametrarna. Gränserna bestäms av pris nivån och virtuella kärnor för servern.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

I Azure Database for MariaDB är binära loggar alltid aktiverade (dvs. `log_bin` är inställt på på). Om du vill använda utlösare får du ett fel som liknar att *du inte har behörigheten Super och binär loggning är aktive rad (du kanske vill använda mindre säker `log_bin_trust_function_creators` variabel)*.

Formatet för binär loggning är alltid **rad** och alla anslutningar till servern använder **alltid** rad-baserad binär loggning. Med en diskbaserad binär loggning finns inte säkerhets problem och binär loggning kan inte brytas, så du kan säkert ange [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) **värdet sant**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size) .

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servrar som har stöd för upp till 4 TB lagring

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|872415232|134217728|872415232|
|Grundläggande|2|2684354560|134217728|2684354560|
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
|Grundläggande|1|872415232|134217728|872415232|
|Grundläggande|2|2684354560|134217728|2684354560|
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

MariaDB lagrar InnoDB-tabellen i olika tabell utrymmen baserat på den konfiguration du angav när tabellen skapades. [Systemets tabell utrymme](https://mariadb.com/kb/en/innodb-system-tablespaces/) är lagrings utrymmet för data ord listan InnoDB. Ett tabell namn för en [fil per tabell](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) innehåller data och index för en enskild InnoDB-tabell och lagras i fil systemet i en egen datafil. Detta beteende styrs av `innodb_file_per_table` Server parametern. Inställningen `innodb_file_per_table` `OFF` gör att InnoDB skapar tabeller i System register utrymmet. Annars skapar InnoDB tabeller i tabell utrymmen per tabell.

Azure Database for MariaDB stöder högst **1 TB** i en enskild datafil. Om databas storleken är större än 1 TB bör du skapa tabellen i [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) tabell utrymme. Om du har en enskild tabell storlek som är större än 1 TB bör du använda partitionstabellen.

### <a name="join_buffer_size"></a>join_buffer_size

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Grundläggande|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
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
|Grundläggande|1|50|10|50|
|Grundläggande|2|100|10|100|
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

Att skapa nya klient anslutningar till MariaDB tar tid och när de har upprättats, tar dessa anslutningar med databas resurser, även när de är inaktiva. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information om hur du konfigurerar ProxySQL finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL är ett community-verktyg med öppen källkod. Den stöds av Microsoft på bästa möjliga sikt. För att få produktions support med auktoritativ vägledning kan du utvärdera och nå ut till [ProxySQL produkt support](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Grundläggande|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
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

Frågespråket är aktiverat som standard i MariaDB med `have_query_cache` parametern. 

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Grundläggande|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Grundläggande|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
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

Läs mer om den här parametern i [MariaDB-dokumentationen](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|
|Grundläggande|1|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
|Grundläggande|2|Kan inte konfigureras på Basic-nivå|Saknas|Saknas|
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

Vid den första distributionen innehåller en Azure for MariaDB-server system tabeller för tids zons information, men tabellerna fylls inte i. Du kan fylla i tids zons tabellerna genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som MySQL kommando rad eller MySQL Workbench. Mer information om hur du anropar den lagrade proceduren finns i [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -eller [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -artiklarna och ställer in globala eller sessionsbaserade tids zoner.

## <a name="non-configurable-server-parameters"></a>Server parametrar som inte går att konfigurera

Följande Server parametrar kan inte konfigureras i tjänsten:

|**Parameter**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256|
|innodb_log_files_in_group|2|

Andra server parametrar som inte listas här anges som standardvärden för MariaDB för [MariaDB](https://mariadb.com/kb/en/server-system-variables/).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar egenskaper för servern med hjälp av Azure Portal](./howto-server-parameters.md)
- Lär dig hur du [konfigurerar egenskaper för servrar med Azure CLI](./howto-configure-server-parameters-cli.md)
- Lär dig hur du [konfigurerar egenskaper för servern med hjälp av PowerShell](./howto-configure-server-parameters-using-powershell.md)
