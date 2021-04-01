---
title: U-SQL-användardefinierad kombinations programmerings guide för Azure Data Lake
description: Läs om U-SQL-UDO programmerings guide – användardefinierad kombinations bok.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512695"
---
# <a name="use-user-defined-combiner"></a>Använda användardefinierad kombinerare

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL-UDO: användardefinierad kombinations
Med användardefinierad kombinations-eller UDC-uppsättning kan du kombinera rader från vänster och höger rad uppsättningar, baserat på anpassad logik. Användardefinierad kombinerare används med kombinera-uttryck.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Definiera och använda användardefinierad kombinations samling

En kombinerare anropas med ett KOMBINATIONs uttryck som innehåller den information som krävs om båda uppsättningarna med indata, grupper, resultat schema och ytterligare information.

För att anropa en kombinerare i ett bas U-SQL-skript, använder vi följande syntax:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Mer information finns i [kombinera uttryck (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

För att definiera en användardefinierad kombinations samling måste vi skapa `ICombiner` gränssnittet med `SqlUserDefinedCombiner` attributet [], vilket är valfritt för en användardefinierad kombinations definition.

Definition av bas `ICombiner` klass:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Den anpassade implementeringen av ett `ICombiner` gränssnitt ska innehålla definitionen för en `IEnumerable<IRow>` kombinerad åsidosättning.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Attributet **SqlUserDefinedCombiner** anger att typen ska registreras som en användardefinierad kombinations samling. Det går inte att ärva den här klassen.

**SqlUserDefinedCombiner** används för att definiera egenskapen kombinations läge. Det är ett valfritt attribut för en användardefinierad kombinations definition.

CombinerMode-läge

CombinerMode Enum kan ha följande värden:

* Fullständig (0) varje utgående rad kan vara beroende av alla indata-rader från vänster och höger med samma nyckel värde.

* Left (1) varje utgående rad är beroende av en enda inmatnings rad från vänster (och eventuellt alla rader från höger med samma nyckel värde).

* Höger (2) varje utgående rad är beroende av en enda inmatnings rad från den högra (och eventuellt alla rader från vänster med samma nyckel värde).

* Inre (3) varje utgående rad är beroende av en enda inmatnings rad från vänster och höger med samma värde.

Exempel: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


De huvudsakliga programmerings objekt är:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Uppsättningar med indatakälla skickas som **vänster** och **höger** `IRowset` typ av gränssnitt. Båda rad uppsättningarna måste räknas upp för bearbetning. Du kan bara räkna upp varje gränssnitt en gång, så vi måste räkna upp och cachelagra det om det behövs.

För cachelagring kan vi skapa en lista \<T\> över minnes strukturer som ett resultat av en LINQ-frågekörningen, särskilt List<`IRow`>. Den anonyma data typen kan bara användas vid uppräkning.

Se [Introduktion till LINQ-frågor (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) för mer information om LINQ-frågor och [IEnumerable- \<T\> gränssnitt](/dotnet/api/system.collections.generic.ienumerable-1) för mer information om IEnumerable- \<T\> gränssnitt.

För att hämta de faktiska datavärdena från det inkommande `IRowset` använder vi metoden Get () för `IRow` gränssnittet.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Du kan bestämma enskilda kolumn namn genom att anropa `IRow` schema metoden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Eller med hjälp av namnet på schema kolumnen:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Den allmänna uppräkningen med LINQ ser ut så här:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

När du har uppräknat båda rad uppsättningarna kommer vi att gå igenom alla rader. För varje rad i den vänstra rad uppsättningen kommer vi att hitta alla rader som uppfyller villkoret för vår kombinations samling.

Värdena för utdata måste anges med `IUpdatableRow` output.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Faktiska utdata utlöses genom att anropa till `yield return output.AsReadOnly();` .

Följande är ett kombinations exempel:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

I det här scenariot för användnings fall skapar vi en analys rapport för åter försäljaren. Målet är att hitta alla produkter som kostar mer än $20 000 och som säljer via webbplatsen snabbare än via den vanliga åter försäljaren inom en viss tids period.

Här är det grundläggande U-SQL-skriptet. Du kan jämföra logiken mellan en vanlig koppling och en kombinerare:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

En användardefinierad kombinations rutan kan anropas som en ny instans av applier-objektet:

```csharp
USING new MyNameSpace.MyCombiner();
```


Eller med anropet till en omslutnings fabriks metod:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)