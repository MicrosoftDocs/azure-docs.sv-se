---
title: Transparent datakryptering (portal) för dedikerad SQL-pool (tidigare SQL DW)
description: Transparent datakryptering (TDE) för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: da4be6f4bc8335e0976a0a4a87c4d232b2a2285f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676314"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Kom igång med transparent datakryptering (TDE) för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill aktivera transparent datakryptering (TDE) måste du vara administratör eller medlem i DBManager-rollen.

## <a name="enabling-encryption"></a>Aktivera kryptering

Om du vill aktivera TDE följer du stegen nedan:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj Portal inställningar för **transparent data krypterings** alternativ ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Välj **på** att ställa in ![ Portal inställningar på](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Välj **Spara** 
    ![ Portal inställningar Spara](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Inaktiverar kryptering

Om du vill inaktivera TDE följer du stegen nedan:

1. Öppna databasen i [Azure Portal](https://portal.azure.com)
2. Klicka på knappen **Inställningar** i bladet databas
3. Välj Portal inställningar för **transparent data krypterings** alternativ ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Välj **inaktivera** inställnings ![ Portal inställningar av](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Välj **Spara** 
    ![ Portal inställning Spara 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Kryptering DMV: er

Kryptering kan bekräftas med följande DMV: er:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
