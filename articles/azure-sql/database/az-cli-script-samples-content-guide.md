---
title: Exempel på Azure CLI-skript
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exempel på Azure CLI-skript för att skapa och hantera Azure SQL Database och Azure SQL-hanterad instans
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563366"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure CLI-exempel för Azure SQL Database-och SQL-hanterad instans 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Du kan konfigurera Azure SQL Database-och SQL-hanterad instans med hjälp av <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Följande tabell innehåller länkar till exempel på Azure CLI-skript för att hantera enskilda databaser och databaser i Azure SQL Database. 

|Område|Beskrivning|
|---|---|
|**Skapa databaser i Azure SQL Database**||
| [Skapa en databas och konfigurera en brandväggsregel](scripts/create-and-configure-database-cli.md) | Skapar en SQL Database och konfigurerar en brand Väggs regel på server nivå. |
| [Skapa elastiska pooler och flytta pooldatabaser](scripts/move-database-between-elastic-pools-cli.md) | Skapar elastiska pooler, flyttar databaser i pooler och ändrar beräknings storlekarna. |
|**Skala databaser i Azure SQL Database**||
| [Skala en databas](scripts/monitor-and-scale-database-cli.md) | Skalar en databas i SQL Database till en annan beräknings storlek efter att ha frågat efter storleks informationen för databasen. |
| [Skala en elastisk pool](scripts/scale-pool-cli.md) | Skalar en elastisk SQL-pool till en annan beräknings storlek. |
|**Konfigurera geo-replikering och redundans**||
| [Lägga till en enskild databas i en failover-grupp](scripts/add-database-to-failover-group-cli.md)| Skapar en databas och en failover-grupp, lägger till databasen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera en failover-grupp för en elastisk pool](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Skapar en databas, lägger till den i en elastisk pool, lägger till den elastiska poolen i gruppen redundans och testar sedan redundansväxlingen till den sekundära servern. |
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfigurerar aktiv geo-replikering för en databas i Azure SQL Database och växlar den till den sekundära repliken. |
| [Konfigurera och redundansväxla en databas i pooler med hjälp av aktiv geo-replikering](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfigurerar aktiv geo-replikering för en databas i en elastisk pool och växlar sedan till den sekundära repliken. |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfigurerar principer för granskning och hot identifiering för en databas i Azure SQL Database. |
| **Säkerhetskopiera, återställa, kopiera och importera en databas**||
| [Säkerhetskopiera en databas](../../sql-database/scripts/sql-database-backup-database-cli.md)| Säkerhetskopierar en databas i SQL Database till en Azure Storage-säkerhetskopiering. |
| [Återställ en databas](../../sql-database/scripts/sql-database-restore-database-cli.md)| Återställer en databas i SQL Database från en Geo-redundant säkerhets kopia och återställer en borttagen databas till den senaste säkerhets kopian. |
| [Kopiera en databas till en ny server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Skapar en kopia av en befintlig databas i SQL Database på en ny server. |
| [Importera en databas från en BACPAC-fil](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importerar en databas till SQL Database från en BACPAC-fil. |
|||

Läs mer om [Azure CLI-API: et för en databas](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

Följande tabell innehåller länkar till exempel på Azure CLI-skript för Azure SQL-hanterad instans.

|Område|Beskrivning|
|---|---|
| **Skapa en SQL-hanterad instans**||
| [Skapa en SQL-hanterad instans](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Skapar en SQL-hanterad instans. |
| **Konfigurera transparent datakryptering (TDE)**||
| [Hantera transparent datakryptering i en SQL-hanterad instans med hjälp av Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfigurerar transparent datakryptering (TDE) i SQL-hanterad instans med hjälp av Azure Key Vault med olika nyckel scenarier. |
|**Konfigurera en failover-grupp**||
| [Konfigurera en failover-grupp för SQL-hanterad instans](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Skapar två instanser av SQL-hanterad instans, lägger till dem i en grupp för redundans och testar sedan redundans från den primära SQL-hanterade instansen till den sekundära SQL-hanterade instansen. |
|||

Fler exempel på SQL-hanterade instanser finns i [skapa](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [Uppdatera](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [flytta en databas](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)och [arbeta med](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skript.

Läs mer om [Azure CLI API för SQL-hanterad instans](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---