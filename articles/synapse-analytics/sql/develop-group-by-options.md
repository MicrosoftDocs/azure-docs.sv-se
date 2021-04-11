---
title: Använd GROUP BY-alternativ i Synapse SQL
description: Med Synapse SQL kan du utveckla lösningar genom att implementera olika grupper med alternativ.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: c2e1ddbfb87df40a0e3683e7bca7539c26191a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671303"
---
# <a name="group-by-options-in-synapse-sql"></a>Gruppera efter alternativ i Synapse SQL

Med Synapse SQL kan du utveckla lösningar genom att implementera olika grupper med alternativ. 

## <a name="what-group-by-does"></a>Vad GROUP BY gör

[Group by](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest&preserve-view=true) T-SQL-satsen sammanställer data till en sammanfattande uppsättning rader.

SQL-poolen utan Server stöder hela intervallet av GROUP BY-alternativ. Dedikerad SQL-pool stöder ett begränsat antal grupper med alternativ.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Gruppera efter alternativ som stöds i dedikerad SQL-pool

GROUP BY har vissa alternativ som dedikerade SQL-poolen inte stöder. De här alternativen innehåller lösningar, som är följande:

* Gruppera efter med sammanslagning
* GRUPPERADE UPPSÄTTNINGAR
* Gruppera efter med kub

### <a name="rollup-and-grouping-sets-options"></a>Alternativ för sammanfattning och gruppering av uppsättningar

Det enklaste alternativet är att använda UNION ALL för att köra sammanslagningen i stället för att förlita dig på den explicita syntaxen. Resultatet är exakt samma

I följande exempel används GROUP BY-instruktionen med alternativet samla in:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Genom att använda ROLLUP begär föregående exempel följande agg regeringar:

* Land och region
* Land
* Totalsumma

Om du vill ersätta ROLLUP och returnera samma resultat kan du använda UNION alla och uttryckligen ange de nödvändiga agg regeringar:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Exempel principen gäller för att ersätta grupp uppsättningar. Du behöver bara skapa UNION alla avsnitt för de agg regerings nivåer som du vill se.

### <a name="cube-options"></a>Kubalternativ

Det är möjligt att skapa en grupp med hjälp av kub med hjälp av UNION ALL-metoden. Problemet är att koden snabbt kan bli besvärlig och svårhanterligt. Du kan använda den här mer avancerade metoden för att undvika det här problemet.

Det första steget är att definiera ' Cube ' som definierar alla nivåer av agg regering som vi vill skapa. Anteckna kors kopplingen för de två härledda tabellerna när alla nivåer skapas. Resten av koden finns där för formatering.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Följande bild visar resultatet av [CREATE TABLE som Välj](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true):

![Gruppera efter kub](./media/develop-group-by-options/develop-group-by-cube.png)

Det andra steget är att ange en mål tabell för att lagra interimistiska resultat:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Det tredje steget är att loopa över kuben med kolumner som utför aggregation. Frågan körs en gång för varje rad i #Cube temporär tabell. Resultaten lagras i tabellen #Results temporära tabell:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Slutligen kan du returnera resultaten genom att läsa från #Results temporär tabell:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Genom att dela upp koden i avsnitt och generera en loop-konstruktion blir koden mer hanterbar och hanterbar.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](develop-overview.md).
