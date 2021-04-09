---
title: Azure PowerShell skript exempel
description: Använd Azure PowerShell-skript exempel som hjälper dig att skapa och hantera Azure SQL Database och Azure SQL-hanterade instans resurser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690982"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure PowerShell exempel för Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure SQL Database och Azure SQL-hanterad instans kan du konfigurera databaser, instanser och pooler med hjälp av Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure SQL Database.

|Länk|Beskrivning|
|---|---|
|**Skapa och konfigurera enskilda databaser och elastiska pooler**||
| [Skapa en enskild databas och konfigurera en brand Väggs regel på server nivå](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet skapar en enskild databas och konfigurerar en IP-brandväggsregel på server nivå. |
| [Skapa elastiska pooler och flytta pooldatabaser](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet skapar elastiska pooler, flyttar databaser i pooler och ändrar beräknings storlekarna.|
|**Konfigurera geo-replikering och redundans**||
| [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar aktiv geo-replikering för en enskild databas och växlar den till den sekundära repliken. |
| [Konfigurera och redundansväxla en poolad databas med aktiv geo-replikering](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar aktiv geo-replikering för en databas i en elastisk pool och växlar den till den sekundära repliken. |
|**Konfigurera en failover-grupp**||
| [Konfigurera en failover-grupp för en enskild databas](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet skapar en databas och en failover-grupp, lägger till databasen i gruppen redundans och testar redundans till den sekundära servern. |
| [Konfigurera en failover-grupp för en elastisk pool](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet skapar en databas, lägger till den i en elastisk pool, lägger till den elastiska poolen i gruppen redundans och testar redundans till den sekundära servern. |
|**Skala en databas och en elastisk pool**||
| [Skala en databas](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestanda måtten för en enskild databas, skalar den till en högre beräknings storlek och skapar en varnings regel för ett av prestanda måtten. |
| [Skala en elastisk pool](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet övervakar prestanda måtten för en elastisk pool, skalar den till en högre beräknings storlek och skapar en varnings regel för ett av prestanda måtten. |
| **Granskning och hotidentifiering** |
| [Konfigurera granskning och identifiering av hot](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar principer för granskning och hot identifiering för en databas. |
| **Återställa, kopiera och importera en databas**||
| [Återställ en databas](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet återställer en databas från en Geo-redundant säkerhets kopia och återställer en borttagen databas till den senaste säkerhets kopian. |
| [Kopiera en databas till en ny server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet skapar en kopia av en befintlig databas på en ny server. |
| [Importera en databas från en BACPAC-fil](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet importerar en databas till Azure SQL Database från en BACPAC-fil. |
| **Synkronisera data mellan databaser**||
| [Synkronisera data mellan databaser](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar datasynkronisering för synkronisering mellan flera databaser i Azure SQL Database. |
| [Synkronisera data mellan SQL Database och SQL Server lokalt](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet konfigurerar datasynkronisering för synkronisering mellan en databas i Azure SQL Database och en SQL Server lokal databas. |
| [Uppdatera synkroniseringsschemat för SQL Data Sync](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet lägger till eller tar bort objekt från synkroniseringsschemat för Data Sync. |
|||

Läs mer om [Azure PowerShell-API: et för en databas](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

Följande tabell innehåller länkar till exempel Azure PowerShell skript för Azure SQL-hanterad instans.

|Länk|Beskrivning|
|---|---|
|**Skapa och konfigurera hanterade instanser**||
| [Skapa och hantera en hanterad instans](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Det här PowerShell-skriptet visar hur du skapar och hanterar en hanterad instans med hjälp av Azure PowerShell. |
| [Skapa och hantera en hanterad instans med hjälp av Azure Resource Manager mall](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet visar hur du skapar och hanterar en hanterad instans med hjälp av Azure PowerShell och Azure Resource Manager-mallen.|
| [Återställa databasen till en hanterad instans i en annan geo-region](../managed-instance/scripts/restore-geo-backup.md) | Det här PowerShell-skriptet tar en säkerhets kopia av en databas och återställer den till en annan region. Detta kallas för ett haveri återställnings scenario med geo-återställning. |
| **Konfigurera transparent data kryptering**||
| [Hantera transparent data kryptering i en hanterad instans med hjälp av din egen nyckel från Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet konfigurerar transparent data kryptering i ett Bring Your Own Key scenario för Azure SQL-hanterad instans med hjälp av en nyckel från Azure Key Vault.|
|**Konfigurera en failover-grupp**||
| [Konfigurera en failover-grupp för en hanterad instans](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Det här PowerShell-skriptet skapar två hanterade instanser, lägger till dem i en grupp för redundans och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen. |
|||

Läs mer om [PowerShell-cmdlets för Azure SQL-hanterad instans](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Ytterligare resurser

I exemplen som visas på den här sidan används [PowerShell-cmdletar](/powershell/module/az.sql/) för att skapa och hantera Azure SQL-resurser. Ytterligare cmdletar för att köra frågor och utföra många databas uppgifter finns i [SQLServer](/powershell/module/sqlserver/) -modulen. Mer information finns i [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).