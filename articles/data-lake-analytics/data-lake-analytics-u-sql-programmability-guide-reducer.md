---
title: U-SQL-användardefinierad programmerings handbok för minskning av Användar handbok för Azure Data Lake
description: Läs om U-SQL-UDO programmerings guide – användardefinierad dereducerare.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512632"
---
# <a name="use-user-defined-reducer"></a>Använda användardefinierad reducerare

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL-UDO: användardefinierad minskning

Med U-SQL kan du skriva anpassade rad uppsättnings Defiler i C# med hjälp av det användardefinierade ramverket för operatörs utökning och implementera ett IReducer-gränssnitt.

Användardefinierad minsknings punkt eller UDR kan användas för att eliminera onödiga rader under data extrahering (import). Den kan också användas för att manipulera och utvärdera rader och kolumner. Baserat på programmerings logik kan det också definiera vilka rader som ska extraheras.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Definiera och använda användardefinierad minskning
För att kunna definiera en UDR-klass måste vi skapa ett `IReducer` gränssnitt med ett valfritt `SqlUserDefinedReducer` attribut.

Det här klass gränssnittet ska innehålla en definition för `IEnumerable` åsidosättningen av gränssnitts rad uppsättningen.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Attributet **SqlUserDefinedReducer** anger att typen ska vara registrerad som användardefinierad minskning. Det går inte att ärva den här klassen.
**SqlUserDefinedReducer** är ett valfritt attribut för en användardefinierad minsknings definition. Den används för att definiera IsRecursive-egenskapen.

* bool IsRecursive    
* **True**  = anger om denna minskning är associativ och commutative

Huvud programmerings objekt är **indata** och **utdata**. Det inmatade objektet används för att räkna upp ingående rader. Utdata används för att ange utmatnings rader som ett resultat av en minskning av aktiviteten.

Vi använder-metoden för uppräkning av inmatade rader `Row.Get` .

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametern för `Row.Get` metoden är en kolumn som har skickats som en del av `PRODUCE` `REDUCE` satsen i instruktionen U-SQL Base. Vi behöver använda rätt datatyp här även.

För utdata använder du- `output.Set` metoden.

Det är viktigt att förstå att anpassad minskning bara matar ut värden som definieras med `output.Set` metod anropet.

```csharp
output.Set<string>("mycolumn", guid);
```

Den faktiska minskningens utdata utlöses genom att anropa `yield return output.AsReadOnly();` .

Följande är ett exempel på en minsknings exempel:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

I det här scenariot för användnings fall hoppar avminskningen av rader med ett tomt användar namn. För varje rad i rad uppsättningen läser den varje obligatorisk kolumn och utvärderar sedan användar namnets längd. Den returnerar den faktiska raden endast om värdets längd för användar namn är större än 0.

Följande är grundläggande U-SQL-skript som använder en anpassad minskning:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)