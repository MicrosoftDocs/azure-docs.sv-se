---
title: Begränsningar – Azure Database for MySQL-flexibel Server
description: I den här artikeln beskrivs begränsningar i Azure Database for MySQL-flexibel Server, till exempel antalet anslutnings-och lagrings motor alternativ.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a3abde9092519be057dcd73ec63318f970fd5c74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543093"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Begränsningar i Azure Database for MySQL-flexibel Server (för hands version)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln beskriver begränsningar i Azure Database for MySQL flexibla Server-tjänsten. [Allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) i MySQL Database Engine är också tillämpliga. Om du vill lära dig mer om resurs (beräknings-, minnes-, lagrings-) nivåer, se artikeln [beräkning och lagring](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Serverparametrar

> [!NOTE]
> Om du letar efter minsta/högsta-värden för Server parametrar som `max_connections` och `innodb_buffer_pool_size` , har den här informationen flyttats till Server parametrarna Concept [Server Parameters](./concepts-server-parameters.md) .

Azure Database for MySQL stöder justering av värdet för Server parametrar. Det minsta och högsta värdet för vissa parametrar (t. ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) bestäms av beräknings nivån och serverns beräknings storlek. Se [Server parametrar](./concepts-server-parameters.md) för mer information om dessa gränser.

Lösen ords-plugin-program som "validate_password" och "caching_sha2_password" stöds inte av tjänsten.

## <a name="storage-engines"></a>Lagrings motorer

MySQL stöder många lagrings motorer. På Azure Database for MySQL flexibel Server stöds följande lagrings motorer och stöds inte:

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

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure Storage monteras via SMB).

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="zone-redundant-ha"></a>Zonens redundanta HA
- Den här konfigurationen kan bara anges när servern skapas.
- Stöds inte i data behandlings nivån burst.

### <a name="networking"></a>Nätverk
- Anslutnings metoden kan inte ändras när servern har skapats. Om servern har skapats med *privat åtkomst (VNet-integrering)* går det inte att ändra till *offentlig åtkomst (tillåtna IP-adresser)* efter Create och vice versa
- TLS/SSL är aktiverat som standard och kan inte inaktive ras.
- Den lägsta TLS-version som stöds på servern är TLS 1.2. Läs mer i [ansluta med TLS/SSL](./how-to-connect-tls-ssl.md) .

### <a name="stopstart-operation"></a>Stoppa/starta åtgärd
- Stöds inte med Zone-redundanta konfigurationer (både primär och standby).
- Stöds inte med konfigurationer för Läs-replikering (både källa och repliker).

### <a name="scale-operations"></a>Skalnings åtgärder
- Det finns inte stöd för att minska Server lagrings utrymme.

### <a name="read-replicas"></a>Skrivskyddade repliker
- Stöds inte med Zone-redundanta konfigurationer (både primär och standby).

### <a name="server-version-upgrades"></a>Uppgraderingar av Server version
- Automatisk migrering mellan huvud versioner av databas motorn stöds inte. Om du vill uppgradera den högre versionen tar du en [dump och återställer](../concepts-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

### <a name="restoring-a-server"></a>Återställa en server
- Med en tidpunkts återställning skapas nya servrar med samma beräknings-och lagrings konfiguration som den käll server som den baseras på. Den nyligen återställda serverns beräkning kan skalas ned när servern har skapats.
- Det finns inte stöd för att återställa en borttagen Server.

## <a name="next-steps"></a>Nästa steg

- Förstå [vad som är tillgängligt för beräknings-och lagrings alternativ](concepts-compute-storage.md)
- Lär dig mer om [MySQL-versioner som stöds](concepts-supported-versions.md)
- Granska [hur du säkerhetskopierar och återställer en server med hjälp av Azure Portal](how-to-restore-server-portal.md)