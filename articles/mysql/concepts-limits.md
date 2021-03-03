---
title: Begränsningar – Azure Database for MySQL
description: I den här artikeln beskrivs begränsningar i Azure Database for MySQL, till exempel antalet anslutnings-och lagrings motor alternativ.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721028"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure Database for MySQL
I följande avsnitt beskrivs kapacitet, stöd för lagrings motor, stöd för stöd för data manipulation och funktionella gränser i databas tjänsten. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) som gäller för databas motorn MySQL.

## <a name="server-parameters"></a>Serverparametrar

> [!NOTE]
> Om du letar efter minsta/högsta-värden för Server parametrar som `max_connections` och `innodb_buffer_pool_size` har den här informationen flyttats till **[Server parametrar](./concepts-server-parameters.md)** -artikeln.

Azure Database for MySQL stöder justering av värdet för Server parametrar. Det minsta och högsta värdet för vissa parametrar (t. ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) bestäms av pris nivån och virtuella kärnor för servern. Se [Server parametrar](./concepts-server-parameters.md) för mer information om dessa gränser.

Vid den första distributionen innehåller en Azure för MySQL-server system tabeller för tids zons information, men tabellerna fylls inte i. Du kan fylla i tids zons tabellerna genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som MySQL kommando rad eller MySQL Workbench. Mer information om hur du anropar den lagrade proceduren finns i [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -eller [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -artiklarna och ställer in globala eller sessionsbaserade tids zoner.

Lösen ords-plugin-program som "validate_password" och "caching_sha2_password" stöds inte av tjänsten.

## <a name="storage-engines"></a>Lagrings motorer

MySQL stöder många lagrings motorer. På Azure Database for MySQL stöds följande lagrings motorer och stöds inte:

### <a name="supported"></a>Stöds
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MINNESOPTIMERADE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöd saknas
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARKIVATTRIBUTET](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [EXTERNT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Privilegier & stöd för data manipulation

Många Server parametrar och inställningar kan oavsiktligt försämra serverns prestanda eller negera sur egenskaper för MySQL-servern. För att upprätthålla tjänstens integritet och service avtal på en produkt nivå visar inte tjänsten flera roller. 

MySQL-tjänsten tillåter inte direkt åtkomst till det underliggande fil systemet. Vissa kommandon för att ändra data stöds inte. 

### <a name="unsupported"></a>Stöd saknas

Följande stöds inte:
- DBA-roll: begränsad. Du kan också använda administratörs användaren (skapas när du skapar en ny server), så att du kan utföra de flesta DDL-och DML-instruktioner. 
- Superprivilegium: samma behörighet som [Super](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är begränsad.
- Avrundning: kräver Super-behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhets kopia tar du bort `CREATE DEFINER` kommandona manuellt eller genom att använda `--skip-definer` kommandot när du utför en mysqldump.
- System databaser: [MySQL system-databasen](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) är skrivskyddad och används för att stödja olika PaaS-funktioner. Du kan inte göra ändringar i `mysql` system databasen.
- `SELECT ... INTO OUTFILE`: Stöds inte i tjänsten.
- `LOAD_FILE(file_name)`: Stöds inte i tjänsten.

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure Storage monteras via SMB).

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalnings åtgärder
- Dynamisk skalning till och från de grundläggande pris nivåerna stöds inte för närvarande.
- Det finns inte stöd för att minska lagrings storleken för servern.

### <a name="server-version-upgrades"></a>Uppgraderingar av Server version
- Automatisk migrering mellan huvud versioner av databas motorn stöds inte för närvarande. Om du vill uppgradera till nästa högre version kan du göra en [dumpning och återställa](./concepts-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR skapas den nya servern med samma konfigurationer som den server den baseras på.
- Det finns inte stöd för att återställa en borttagen Server.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL Server-instansen visar fel Server version när anslutningen har upprättats. Använd kommandot för att få rätt version av Server instans motorn `select version();` .

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MySQL-databas versioner som stöds](concepts-supported-versions.md)
