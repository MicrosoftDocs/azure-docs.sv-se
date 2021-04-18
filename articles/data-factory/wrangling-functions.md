---
title: Dataförändringsfunktioner i Azure Data Factory
description: En översikt över tillgängliga data wrangling-funktioner i Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600948"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Transformeringsfunktioner i Power Query för dataomvandling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med dataförberedelse i Azure Data Factory kan du förbereda och omvandla data kodfritt i molnskala genom att översätta Power Query till ```M``` Dataflöde skript. ADF integreras med [Power Query Online](/powerquery-m/power-query-m-reference) och gör Power Query funktioner tillgängliga för data wrangling via Spark-körning med hjälp av Spark-infrastrukturen för ```M``` dataflödet. 

> [!NOTE]
> Power Query i ADF är för närvarande tillgänglig i offentlig förhandsversion

För närvarande stöds inte Power Query M-funktioner för data wrangling trots att de är tillgängliga under redigering. När du skapar dina kombinationsfunktioner visas följande felmeddelande om en funktion inte stöds:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Nedan visas en lista över stödda Power Query M-funktioner.

## <a name="column-management"></a>Kolumnhantering

* Urval: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Borttagning: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Byta namn: [Table.RenameColumns](/powerquery-m/table-renamecolumns), [Table.PrefixColumns](/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Ändra ordning: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Radfiltrering

Använd [M-funktionen Table.SelectRows](/powerquery-m/table-selectrows) för att filtrera på följande villkor:

* Likhet och olikhet
* Numeriska jämförelser, textjämförelser och datumjämförelser (men inte DateTime)
* Numerisk information som [Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Text inneslutning med [Text.Contains,](/powerquery-m/text-contains) [Text.StartsWith](/powerquery-m/text-startswith)eller [Text.EndsWith](/powerquery-m/text-endswith)
* Datumintervall inklusive alla [IsIn-datumfunktioner](/powerquery-m/date-functions)) 
* Kombinationer av dessa med hjälp av villkor för och, eller eller inte

## <a name="adding-and-transforming-columns"></a>Lägga till och transformera kolumner

Följande M-funktioner lägger till eller transformerar kolumner: [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns](/powerquery-m/table-transformcolumns), [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Nedan visas de transformeringsfunktioner som stöds.

* Numerisk aritmetik
* Sammanfogning av text
* Datum- och tidsaritmetik (aritmetiska operatorer, [Date.AddDays](/powerquery-m/date-adddays), [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters](/powerquery-m/date-addquarters), [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* Varaktigheter kan användas för datum- och tidsaritmetik, men måste omvandlas till en annan typ innan de skrivs till en mottagare (aritmetiska operatorer, [#duration](/powerquery-m/sharpduration), [Duration.Days](/powerquery-m/duration-days), [Duration.Hours](/powerquery-m/duration-hours), [Duration.Minutes](/powerquery-m/duration-minutes), [Duration.Seconds](/powerquery-m/duration-seconds), [Duration.TotalDays](/powerquery-m/duration-totaldays), [Duration.TotalHours](/powerquery-m/duration-totalhours), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds))    
* De flesta standardfunktioner, vetenskapliga och trigonometriska numeriska funktioner (Alla funktioner under [Åtgärder,](/powerquery-m/number-functions#operations) [Avrundning](/powerquery-m/number-functions#rounding)och [Trigonometri](/powerquery-m/number-functions#trigonometry) förutom  Number.Factorial, Number.Permutations och Number.Combinations)
* Replacement ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Extrahering av positionell text ([Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* Grundläggande textformatering ([Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Date/Time Functions ([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* Om uttryck (men grenar måste ha matchande typer)
* Radfilter som en logisk kolumn
* Konstanter för tal, text, logiskt värde, datum och datetime

<a name="mergingjoining-tables"></a>Sammanfoga/koppla tabeller
----------------------
* Power Query skapar en kapslad koppling (Table.NestedJoin; användare kan också skriva [Table.AddJoinColumn manuellt).](/powerquery-m/table-addjoincolumn)
    Användarna måste sedan expandera den kapslade kopplingskolumnen till en icke-kapslad koppling (Table.ExpandTableColumn, stöds inte i någon annan kontext).
* M-funktionen   [Table.Join](/powerquery-m/table-join) kan skrivas direkt för att undvika ytterligare ett expansionssteg, men användaren måste se till att det inte finns några dubblettkolumnnamn bland de kopplade tabellerna
* Kopplings typer som stöds:   [Inre,](/powerquery-m/joinkind-inner)   [LeftOuter,](/powerquery-m/joinkind-leftouter)   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* Både   [Value.Equals](/powerquery-m/value-equals)   [och Value.NullableEquals](/powerquery-m/value-nullableequals) stöds som jämförelse för nyckeljämförare

## <a name="group-by"></a>Gruppera efter

Använd [Table.Group för](/powerquery-m/table-group) att aggregera värden.
* Måste användas med en sammansättningsfunktion
* Sammansättningsfunktioner som stöds:   [List.Sum](/powerquery-m/list-sum),   [List.Count](/powerquery-m/list-count),   [List.Average](/powerquery-m/list-average),   [List.Min](/powerquery-m/list-min),   [List.Max](/powerquery-m/list-max),   [List.StandardDeviation](/powerquery-m/list-standarddeviation),   [List.First](/powerquery-m/list-first),   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sortering

Använd [Table.Sort för](/powerquery-m/table-sort) att sortera värden.

## <a name="reducing-rows"></a>Minska rader

Behåll och ta bort översta, Behåll intervall (motsvarande M-funktioner, stöder endast antal, inte villkor: [Table.FirstN](/powerquery-m/table-firstn), [Table.Skip](/powerquery-m/table-skip), [Table.RemoveFirstN](/powerquery-m/table-removefirstn), [Table.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Kända funktioner som inte stöds

| Funktion | Status |
| -- | -- |
| Table.PromoteHeaders | Stöds inte. Samma resultat kan uppnås genom att ange "Första raden som rubrik" i datauppsättningen. |
| Table.CombineColumns | Det här är ett vanligt scenario som inte stöds direkt, men som kan uppnås genom att lägga till en ny kolumn som sammanfogar två angivna kolumner.  Till exempel Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Detta stöds i de flesta fall. Följande scenarier stöds inte: transformering av sträng till valutatyp, transformering av sträng till tidstyp, transformering av sträng till procenttyp. |
| Table.NestedJoin | Om du bara gör en koppling resulterar det i ett verifieringsfel. Kolumnerna måste expanderas för att det ska fungera. |
| Table.Distinct | Ta bort dubblettrader stöds inte. |
| Table.RemoveLastN | Ta bort de nedersta raderna stöds inte. |
| Table.RowCount | Stöds inte, men kan uppnås genom att lägga till en anpassad kolumn som innehåller värdet 1 och sedan aggregera kolumnen med List.Sum. Table.Group stöds. | 
| Felhantering på radnivå | Felhantering på radnivå stöds inte för närvarande. Om du till exempel vill filtrera bort icke-numeriska värden från en kolumn är en metod att transformera textkolumnen till ett tal. Varje cell som inte kan transformeras är i ett feltillstånd och måste filtreras. Det här scenariot är inte möjligt i utskalade M. |
| Table.Transpose | Stöds inte |
| Table.Pivot | Stöds inte |
| Table.SplitColumn | Stöds delvis |

## <a name="m-script-workarounds"></a>Lösningar för M-skript

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>För ```SplitColumn``` finns det ett alternativ för delning efter längd och efter position

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Inserted first characters", "Text range", each Text.Middle([Email], 4, 9), type text)

Det här alternativet är tillgängligt från alternativet Extrahera i menyfliksområdet

![Power Query Lägg till kolumn](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>För ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName])


## <a name="next-steps"></a>Nästa steg

Lär dig hur [du skapar en data wrangling Power Query i ADF](wrangling-tutorial.md).
