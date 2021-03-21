---
title: Tilldela variabler
description: I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler för dedikerade SQL-pooler i Azure Synapse Analytics.
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
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459224"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Tilldela variabler för dedikerade SQL-pooler i Azure Synapse Analytics

I den här artikeln hittar du viktiga tips för att tilldela T-SQL-variabler i en dedikerad SQL-pool.

## <a name="set-variables-with-declare"></a>Ange variabler med deklarera

Variabler i dedikerad SQL-pool anges med `DECLARE` instruktionen eller `SET` instruktionen. Att initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabel värde i SQL-poolen.

```sql
DECLARE @v  int = 0
;
```

Du kan också använda deklarera för att ange mer än en variabel i taget. Du kan inte använda Välj eller uppdatera för att göra följande:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Det går inte att initiera och använda en variabel i samma DECLARE-instruktion. Följande exempel tillåts **inte** eftersom @p1 är både initierad och används i samma declare-instruktion för att illustrera punkten. Därför ger följande exempel ett fel:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Ange värden med SET

SET är en gemensam metod för att ställa in en enskild variabel.

Följande instruktioner är giltiga sätt att ange en variabel med SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan bara ange en variabel i taget med SET. Sammansatta operatörer är dock tillåtna.

## <a name="limitations"></a>Begränsningar

Du kan inte använda uppdatering för variabel tilldelning.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
