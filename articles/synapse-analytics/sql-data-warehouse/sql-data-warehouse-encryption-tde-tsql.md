---
title: Transparent data kryptering (T-SQL)
description: Transparent data kryptering (TDE) i Azure Synapse Analytics (T-SQL)
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
ms.openlocfilehash: 80e5da6bb281806afe6bc980e35d70732bcd609c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676280"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Kom igång med transparent datakryptering (TDE)

> [!div class="op_single_selector"]
>
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill aktivera transparent datakryptering (TDE) måste du vara administratör eller medlem i DBManager-rollen.

## <a name="enabling-encryption"></a>Aktivera kryptering

Följ de här stegen för att aktivera TDE:

1. Anslut till *huvud* databasen på den server som är värd för databasen med en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktiverar kryptering

Följ dessa steg om du vill inaktivera TDE:

1. Anslut till *huvud* databasen med en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> En pausad dedikerad SQL-pool måste återupptas innan du gör ändringar i TDE-inställningarna.

## <a name="verifying-encryption"></a>Verifiera kryptering

Följ stegen nedan för att kontrol lera krypterings status:

1. Anslut till *huvud* -eller instans databasen med hjälp av en inloggning som är administratör eller medlem i **DBManager** -rollen i huvud databasen
2. Kör följande instruktion för att kryptera-databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Resultatet av en ```1``` krypterad databas ```0``` indikerar en icke-krypterad databas.

## <a name="encryption-dmvs"></a>Kryptering DMV: er

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
