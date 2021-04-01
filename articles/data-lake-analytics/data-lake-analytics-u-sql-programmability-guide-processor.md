---
title: Programmerings guide för U-SQL-användardefinierad processor för Azure Data Lake
description: Läs om U-SQL-UDO programmerings guide – användardefinierad processor.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512692"
---
# <a name="use-user-defined-processor"></a>Använda användardefinierad processor

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL-UDO: användardefinierad processor
Användardefinierad processor, eller UDP, är en typ av U-SQL-UDO som gör att du kan bearbeta inkommande rader genom att tillämpa programmerings funktioner. Med UDP kan du kombinera kolumner, ändra värden och lägga till nya kolumner om det behövs. I princip hjälper det att bearbeta en rad uppsättning för att producera nödvändiga data element.

## <a name="how-to-define-and-use-user-defined-processor"></a>Definiera och använda användardefinierad processor
För att definiera en UDP måste vi skapa ett `IProcessor` gränssnitt med `SqlUserDefinedProcessor` attributet, vilket är valfritt för UDP.

Det här gränssnittet ska innehålla definitionen för `IRow` åsidosättningen av gränssnitts rad uppsättningen, som visas i följande exempel:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** anger att typen ska registreras som en användardefinierad processor. Det går inte att ärva den här klassen.

Attributet SqlUserDefinedProcessor är **valfritt** för UDP-definition.

Huvud programmerings objekt är **indata** och **utdata**. Indata-objektet används för att räkna upp inmatnings kolumner och utdata och för att ange utdata som ett resultat av processor aktiviteten.

För uppräkning av indatamängds kolumner använder vi `input.Get` metoden.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametern för `input.Get` metoden är en kolumn som har skickats som en del av `PRODUCE` satsen i `PROCESS` instruktionen för U-SQL Base-skriptet. Vi måste använda rätt datatyp här.

För utdata använder du- `output.Set` metoden.

Det är viktigt att notera att den anpassade producenten endast matar ut kolumner och värden som definieras med `output.Set` metod anropet.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Faktisk processor utmatning utlöses genom att anropa `return output.AsReadOnly();` .

Följande är ett processor exempel:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

I det här användnings fallet genererar processorn en ny kolumn med namnet "full_description" genom att kombinera befintliga kolumner, i det här fallet "användare" i versaler och "des". Den återskapar också ett GUID och returnerar de ursprungliga och nya GUID-värdena.

Som du kan se i föregående exempel kan du anropa C#-metoder under `output.Set` metod anrop.

Följande är ett exempel på ett grundläggande U-SQL-skript som använder en anpassad processor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)