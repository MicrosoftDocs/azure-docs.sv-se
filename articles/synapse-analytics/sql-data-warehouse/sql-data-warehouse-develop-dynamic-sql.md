---
title: Använda dynamisk SQL
description: Tips för utvecklings lösningar med dynamisk SQL för dedikerade SQL-pooler i Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679609"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Dynamisk SQL för dedikerade SQL-pooler i Azure Synapse Analytics

I den här artikeln finns tips för utvecklings lösningar som använder dynamiska SQL i dedikerade SQL-pooler.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar program kod för dedikerade SQL-pooler kan du behöva använda dynamisk SQL för att leverera flexibla, generiska och modulära lösningar. Dedikerade SQL-pooler stöder för närvarande inte BLOB-datatyper.

Om du inte stöder BLOB-datatyper kan du begränsa storleken på dina strängar eftersom BLOB-datatyper inkluderar båda typerna varchar (max) och nvarchar (max).

Om du har använt dessa typer i program koden för att bygga stora strängar måste du dela upp koden i segment och använda EXEC-instruktionen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) som normalt.

> [!NOTE]
> Instruktioner som körs i dynamisk SQL kommer fortfarande att omfattas av alla verifierings regler för T-SQL.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
