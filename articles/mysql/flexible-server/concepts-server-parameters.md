---
title: Server parametrar – Azure Database for MySQL-flexibel Server
description: Det här avsnittet innehåller rikt linjer för hur du konfigurerar Server parametrar i Azure Database for MySQL-flexibel Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: f83f743b692ae5a625a4c881b12cbad999f1f606
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106776"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Server parametrar i Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln innehåller överväganden och rikt linjer för att konfigurera server parametrar i Azure Database for MySQL flexibel Server.

## <a name="what-are-server-variables"></a>Vad är servervariabler? 

MySQL-motorn innehåller många olika [servervariabler/parametrar](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) som kan användas för att konfigurera och finjustera motor beteendet. Vissa parametrar kan ställas in dynamiskt under körning medan andra är "statiska", vilket kräver en server omstart för att kunna användas.

Azure Database for MySQL flexibla servern visar möjligheten att ändra värdet för olika MySQL server-parametrar med hjälp av [Azure Portal](./how-to-configure-server-parameters-portal.md) och [Azure CLI](./how-to-configure-server-parameters-cli.md) för att matcha din arbets belastnings behov.

## <a name="configurable-server-parameters"></a>Konfigurerbara Server parametrar

Du kan hantera Azure Database for MySQL flexibel Server konfiguration med hjälp av Server parametrar. Server parametrarna konfigureras med standard och Rekommenderat värde när du skapar servern. Server parameter bladet på Azure Portal visar både de ändrings bara och icke-modifierade Server parametrarna. De Server parametrar som inte kan ändras är nedtonade.

Listan över Server parametrar som stöds växer ständigt. Använd fliken Server parametrar i Azure Portal om du vill visa en fullständig lista och konfigurera server parametrar värden.

Se följande avsnitt för att lära dig mer om gränserna för de många ofta uppdaterade Server parametrarna. Gränserna bestäms av serverns beräknings nivå och storlek (virtuella kärnor).

> [!NOTE]
> Om du vill ändra en server parameter som inte kan ändras, men du vill se som en ändrings bar för din miljö, öppnar du ett [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) -objekt eller en röst om det redan finns en som kan hjälpa oss att prioritera.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

I Azure Database for MySQL flexibel Server är binära loggar alltid aktiverade (det vill säga `log_bin` är inställt på på). Om du vill använda utlösare får du ett fel som liknar att *du inte har behörigheten Super och binär loggning är aktive rad (du kanske vill använda mindre säker `log_bin_trust_function_creators` variabel)*. 

Formatet för binär loggning är alltid **rad** och alla anslutningar till servern använder **alltid** rad-baserad binär loggning. Med en diskbaserad binär loggning finns inte säkerhets problem och binär loggning kan inte brytas, så du kan säkert ange [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **värdet sant**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) .

|**Prisnivå**|**vCore (s)**|**Minnes storlek (GiB)**|**Standardvärde (byte)**|**Minsta värde (byte)**|**Max värde (byte)**|
|---|---|---|---|---|---|
|Burstable (B1S)|1|1|134217728|33554432|134217728|
|Burstable (B1ms)|1|2|536870912|134217728|536870912|
|Burstable|2|4|2147483648|134217728|2147483648|
|Generell användning|2|8|6442450944|134217728|6442450944|
|Generell användning|4|16|12884901888|134217728|12884901888|
|Generell användning|8|32|25769803776|134217728|25769803776|
|Generell användning|16|64|51539607552|134217728|51539607552|
|Generell användning|32|128|103079215104|134217728|103079215104|
|Generell användning|48|192|154618822656|134217728|154618822656|
|Generell användning|64|256|206158430208|134217728|206158430208|
|Minnesoptimerad|2|16|12884901888|134217728|12884901888|
|Minnesoptimerad|4|32|25769803776|134217728|25769803776|
|Minnesoptimerad|8|64|51539607552|134217728|51539607552|
|Minnesoptimerad|16|128|103079215104|134217728|103079215104|
|Minnesoptimerad|32|256|206158430208|134217728|206158430208|
|Minnesoptimerad|48|384|309237645312|134217728|309237645312|
|Minnesoptimerad|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL lagrar tabellen InnoDB i olika tabell utrymmen baserat på den konfiguration du angav när tabellen skapades. [Systemets tabell utrymme](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) är lagrings utrymmet för data ord listan InnoDB. Ett tabell namn för en [fil per tabell](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) innehåller data och index för en enskild InnoDB-tabell och lagras i fil systemet i en egen datafil. Detta beteende styrs av `innodb_file_per_table` Server parametern. Inställningen `innodb_file_per_table` `OFF` gör att InnoDB skapar tabeller i System register utrymmet. Annars skapar InnoDB tabeller i tabell utrymmen per tabell.

Azure Database for MySQL flexibel Server stöder högst **4 TB** i en enda datafil. Om databas storleken är större än 4 TB bör du skapa tabellen i [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabell utrymme. Om du har en enskild tabell storlek som är större än 4 TB bör du använda partitionstabellen.

### <a name="max_connections"></a>max_connections

Värdet för max_connection bestäms av serverns minnes storlek. 

|**Prisnivå**|**vCore (s)**|**Minnes storlek (GiB)**|**Standardvärde**|**Minvärde**|**Maxvärde**|
|---|---|---|---|---|---|
|Burstable (B1S)|1|1|85|10|171|
|Burstable (B1ms)|1|2|171|10|341|
|Burstable|2|4|341|10|683|
|Generell användning|2|8|683|10|1365|
|Generell användning|4|16|1365|10|2731|
|Generell användning|8|32|2731|10|5461|
|Generell användning|16|64|5461|10|10923|
|Generell användning|32|128|10923|10|21845|
|Generell användning|48|192|16384|10|32768|
|Generell användning|64|256|21845|10|43691|
|Minnesoptimerad|2|16|1365|10|2731|
|Minnesoptimerad|4|32|2731|10|5461|
|Minnesoptimerad|8|64|5461|10|10923|
|Minnesoptimerad|16|128|10923|10|21845|
|Minnesoptimerad|32|256|21845|10|43691|
|Minnesoptimerad|48|384|32768|10|65536|
|Minnesoptimerad|64|504|43008|10|86016|

När anslutningar överskrider gränsen kan följande fel meddelande visas:
> FEL 1040 (08004): för många anslutningar

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som ProxySQL för att effektivt hantera anslutningar.

Att skapa nya klient anslutningar till MySQL tar tid och när de har upprättats, använder dessa anslutningar databas resurser, även när de är inaktiva. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information om hur du konfigurerar ProxySQL finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL är ett community-verktyg med öppen källkod. Den stöds av Microsoft på bästa möjliga sikt. För att få produktions support med auktoritativ vägledning kan du utvärdera och nå ut till [ProxySQL produkt support](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Om du får ett fel som liknar "rad storleken är för stor (> 8126)" kanske du vill inaktivera parametern **innodb_strict_mode**. Det går inte att ändra server parametern **innodb_strict_mode** globalt på server nivå på grund av att data storleken på raden är större än 8k. data kommer att trunkeras utan ett fel, vilket kan leda till potentiell data förlust. Vi rekommenderar att du ändrar schemat så att det passar sid storleks gränsen. 

Den här parametern kan ställas in på en sessionsnyckel med `init_connect` . Om du vill ange **innodb_strict_mode** på sessionsstatus, se [inställnings parameter som inte visas](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Om du har en Läs replik Server avbryts replikeringen om du anger **innodb_strict_mode** till av på sessions nivå på en käll Server. Vi rekommenderar att du håller parametern inställt på av om du har Läs repliker.

### <a name="time_zone"></a>time_zone

Vid den första distributionen innehåller en Azure för MySQL-flexibel server system tabeller för tids zons information, men tabellerna fylls inte i. Du kan fylla i tids zons tabellerna genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som MySQL kommando rad eller MySQL Workbench. Mer information om hur du anropar den lagrade proceduren finns i [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) -eller [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -artiklarna och ställer in globala eller sessionsbaserade tids zoner.

## <a name="non-modifiable-server-parameters"></a>Icke-ändrings bara server parametrar

Bladet Server parameter i Azure Portal visar både de ändrings bara och icke-modifierade Server parametrarna. De Server parametrar som inte kan ändras är nedtonade. Om du vill konfigurera en server parameter som inte kan ändras på sessionsläge, se artikeln [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) eller [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) för att ange parametern på anslutnings nivån med `init_connect` .

## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](./how-to-configure-server-parameters-portal.md)
- Så här konfigurerar du [Server parametrar i Azure CLI](./how-to-configure-server-parameters-cli.md)
