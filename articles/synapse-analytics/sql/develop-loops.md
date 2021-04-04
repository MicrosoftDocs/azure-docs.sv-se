---
title: Använd T-SQL-slingor
description: Tips för att använda T-SQL-slingor, ersätta markörer och utveckla relaterade lösningar med Synapse SQL i Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120894"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Använd T-SQL-slingor med Synapse SQL i Azure Synapse Analytics

Den här artikeln innehåller viktiga tips för att använda T-SQL-slingor, ersätta markörer och utveckla relaterade lösningar med Synapse SQL.

## <a name="purpose-of-while-loops"></a>Syfte med WHILe-slingor

Synapse SQL stöder [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) -slingan för att köra instruktions block upprepade gånger. Den här WHILe-slingan fortsätter så länge som de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med hjälp av nyckelordet BREAK. 

Slingor i Synapse SQL är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligt vis är nästan alla markörer som skrivs i SQL-kod av den snabba, skrivskyddade sorten. Detta innebär att slingor är ett bra alternativ för att ersätta markörer.

## <a name="replace-cursors-in-synapse-sql"></a>Ersätt markörer i Synapse SQL

Innan simhopp i bör följande fråga beaktas: "kan den här markören skrivas om för att använda set-based Operations?" I många fall är svaret ja och det är ofta den bästa metoden. En fristående åtgärd körs ofta snabbare än en upprepad rad med rad-metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med en loop-konstruktion. Följande kod är ett enkelt exempel. I den här kod exemplet uppdateras statistiken för alla tabeller i databasen. Genom att iterera över tabellerna i slingan körs varje kommando i följd.

Skapa först en temporär tabell som innehåller ett unikt rad nummer som används för att identifiera de enskilda uttrycken:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Sedan initierar du de variabler som krävs för att köra slingan:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Loopar över instruktioner som kör dem en i taget:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Ta slutligen bort den tillfälliga tabellen som skapades i det första steget

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](develop-overview.md).