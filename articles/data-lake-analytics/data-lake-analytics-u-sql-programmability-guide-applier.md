---
title: U-SQL-användardefinierad applier programmerings guide för Azure Data Lake
description: Läs mer om U-SQL-UDO programmerings guide – användardefinierad applier.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512644"
---
# <a name="use-user-defined-applier"></a>Använda användardefinierad tillämpare 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL-UDO: användardefinierad applier
Med ett U-SQL-användardefinierat applier kan du anropa en anpassad C#-funktion för varje rad som returneras av det yttre tabell uttrycket i en fråga. Den högra inmatningen utvärderas för varje rad från den vänstra inmatningen och de rader som skapas kombineras för slutliga utdata. Listan över kolumner som skapas av operatorn APPLY är en kombination av kolumn uppsättningen i vänster och höger Indatatyp.


## <a name="how-to-define-and-use-user-defined-applier"></a>Definiera och använda användardefinierade applier
En användardefinierad applier anropas som en del av USQL SELECT-uttrycket.

Det vanligaste anropet till den användardefinierade applier ser ut så här:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Mer information om hur du använder appliers i ett SELECT-uttryck finns i [U-SQL väljer du väljer från kors Apply och Outer Apply](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Den användardefinierade applier Bask Class-definitionen är följande:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

För att definiera en användardefinierad applier, måste vi skapa `IApplier` gränssnittet med `SqlUserDefinedApplier` attributet [], vilket är valfritt för en användardefinierad applier-definition.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply anropas för varje rad i den yttre tabellen. Den returnerar `IUpdatableRow` utmatnings rad uppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade applier.

**SqlUserDefinedApplier** anger att typen ska registreras som en användardefinierad applier. Det går inte att ärva den här klassen.

**SqlUserDefinedApplier** är **valfritt** för en användardefinierad applier-definition.


Huvud programmerings objekt är följande:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Uppsättningar med indatamängdar skickas som inloggade `IRow` . De utgående raderna genereras som `IUpdatableRow` utdata-gränssnitt.

Du kan bestämma enskilda kolumn namn genom att anropa `IRow` schema metoden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

För att hämta de faktiska datavärdena från det inkommande `IRow` använder vi metoden Get () för `IRow` gränssnittet.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Eller så använder vi schema kolumn namnet:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Värdena för utdata måste anges med `IUpdatableRow` output:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Det är viktigt att förstå att anpassade appliers endast innehåller utdatakolumner och värden som definieras med `output.Set` metod anrop.

Faktiska utdata utlöses genom att anropa `yield return output.AsReadOnly();` .

De användardefinierade applier-parametrarna kan skickas till konstruktorn. Applier kan returnera ett variabel antal kolumner som måste definieras under applier-anropet i bas U-SQL-skriptet.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Här är det användardefinierade applier-exemplet:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Följande är det grundläggande U-SQL-skriptet för den här användardefinierade applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

I det här scenariot för användnings fall fungerar användardefinierade applier som en kommaavgränsad värde tolkare för egenskaperna för Car-flottan. Raderna i indatafilen ser ut ungefär så här:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Det är en typisk tabbavgränsad TSV-fil med en egenskaps kolumn som innehåller bil egenskaper som märke och modell. Dessa egenskaper måste parsas till tabell kolumnerna. Med applier som tillhandahålls kan du också generera ett dynamiskt antal egenskaper i resultat rad uppsättningen, baserat på den parameter som skickades. Du kan endast generera alla egenskaper eller en speciell uppsättning egenskaper.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Användardefinierade applier kan anropas som en ny instans av applier-objektet:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Eller med anropet till en omslutnings fabriks metod:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)