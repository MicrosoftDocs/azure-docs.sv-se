---
title: Datareplikering-Azure Database for MySQL
description: Lär dig mer om att använda Datareplikering för att synkronisera från en extern server till tjänsten Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 83ad2d4f392afb6bb33c99a5449b9bc8ceeaa058
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312815"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikera data till Azure Database for MySQL

Med Datareplikering kan du synkronisera data från en extern MySQL-server till tjänsten Azure Database for MySQL. Den externa servern kan vara lokalt, i virtuella datorer eller i en databas tjänst som är värd för andra moln leverantörer. Datareplikering baseras på den binära loggen (BinLog) fil positions-eller GTID-baserad replikering som är inbyggd i MySQL. Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

## <a name="when-to-use-data-in-replication"></a>När du ska använda Datareplikering

Huvud scenarierna att tänka på när du använder Datareplikering är:

- **Synkronisering av hybrid data:** Med Datareplikering kan du synkronisera data mellan dina lokala servrar och Azure Database for MySQL. Den här synkroniseringen är användbar för att skapa hybrid program. Den här metoden kan överklaga när du har en befintlig lokal databas server men vill flytta data till en region närmare slutanvändarna.
- **Synkronisering med flera moln:** För komplexa moln lösningar använder du Datareplikering för att synkronisera data mellan Azure Database for MySQL och olika moln leverantörer, inklusive virtuella datorer och databas tjänster som finns i molnet.

Använd [Azure Database migration service](https://azure.microsoft.com/services/database-migration/)(DMS) för migrerings scenarier.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data har inte repliker ATS

[*MySQL system-databasen*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) på käll servern replikeras inte. Dessutom replikeras inte ändringar i konton och behörigheter på käll servern. Om du skapar ett konto på käll servern och det här kontot behöver åtkomst till replik servern, skapar du samma konto manuellt på replik servern. Information om vilka tabeller som finns i system-databasen finns i [hand boken för MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtrering

För att hoppa över replikering av tabeller från käll servern (som finns lokalt, på virtuella datorer eller i en databas tjänst som hanteras av andra moln leverantörer) `replicate_wild_ignore_table` stöds parametern. Du kan också uppdatera den här parametern på replik servern som finns i Azure med hjälp av [Azure Portal](howto-server-parameters.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

Läs mer om den här parametern i MySQL- [dokumentationen](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>Stöds endast i Generell användning eller Minnesoptimerade nivån

Datareplikering stöds endast i Generell användning och minnesoptimerade pris nivåer.

### <a name="requirements"></a>Krav

- Käll Server versionen måste vara minst MySQL version 5,6.
- Käll-och replik Server versionerna måste vara desamma. Till exempel måste båda vara MySQL version 5,6 eller båda måste vara MySQL version 5,7.
- Varje tabell måste ha en primär nyckel.
- Käll servern bör använda MySQL InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på käll servern.
- Om käll servern har SSL aktiverat kontrollerar du att det SSL-CA-certifikat som har angetts för domänen har inkluderats i den `mysql.az_replication_change_master` eller `mysql.az_replication_change_master_with_gtid` lagrade proceduren. Se följande [exempel](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) och `master_ssl_ca` parametern.
- Se till att käll serverns IP-adress har lagts till Azure Database for MySQL replik serverns brand Väggs regler. Uppdatera brandväggsregler med hjälp av [Azure-portalen](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md).
- Kontrol lera att den dator som är värd för käll servern tillåter både inkommande och utgående trafik på port 3306.
- Kontrol lera att käll servern har en **offentlig IP-adress**, att DNS är offentligt tillgängligt eller att käll servern har ett fullständigt kvalificerat domän namn (FQDN).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar replikering av data](howto-data-in-replication.md)
- Lär dig mer om att [Replikera i Azure med Läs repliker](concepts-read-replicas.md)
- Lär dig mer om hur du [migrerar data med minimal stillestånds tid med DMS](howto-migrate-online.md)
