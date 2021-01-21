---
title: Data datatransformering Functions i Azure Data Factory
description: En översikt över tillgängliga data Datatransformering-funktioner i Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: a88f9fab2b10271aa7856a6d0b5ee114f46cfb49
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634331"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Omvandlings funktioner i Power Query för data datatransformering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med data Datatransformering i Azure Data Factory kan du göra kod fria smidiga data förberedelser och datatransformering i moln skala genom att översätta Power Query- ```M``` skript till ett data flödes skript. ADF integreras med [Power Query online](/powerquery-m/power-query-m-reference) och gör Power Query ```M``` funktioner som är tillgängliga för data datatransformering via Spark-körning med data flödet Spark-infrastruktur. 

> [!NOTE]
> Power Query i ADF är för närvarande tillgängliga i offentlig för hands version

För närvarande stöds inte alla Power Query M-funktioner för data datatransformering trots att de är tillgängliga under redigeringen. När du skapar din kombinera, uppmanas du att ange följande fel meddelande om en funktion inte stöds:

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

Nedan visas en lista över Power Query M-funktioner som stöds.

## <a name="column-management"></a>Kolumn hantering

* Markering: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Borttagning: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Byter namn på: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Ordnar om: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rad filtrering

Använd M Function [Table. SelectRows](/powerquery-m/table-selectrows) för att filtrera efter följande villkor:

* Likhet och olikhet
* Numeriska, text-och datum jämförelser (men inte DateTime)
* Numerisk information som [number. IsEven](/powerquery-m/number-iseven) / [udda](/powerquery-m/number-iseven)
* Text inne slutning med [text. contains](/powerquery-m/text-contains), [text. StartsWith](/powerquery-m/text-startswith)eller [text. EndsWith](/powerquery-m/text-endswith)
* Datum intervall, inklusive alla "IsIn' [datum funktioner](/powerquery-m/date-functions)" 
* Kombinationer av dessa med hjälp av och, eller, eller inte villkor

## <a name="adding-and-transforming-columns"></a>Lägga till och transformera kolumner

Följande M-funktioner lägger till eller transformerar kolumner: [Table. AddColumn](/powerquery-m/table-addcolumn), [Table. TransformColumns](/powerquery-m/table-transformcolumns), [Table. ReplaceValue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Nedan visas de omvandlings funktioner som stöds.

* Numeriskt aritmetiskt
* Text sammanfogning
* Date andTime aritmetiskt (aritmetiska operatorer, [date. AddDays](/powerquery-m/date-adddays), [date. AddMonths](/powerquery-m/date-addmonths), [date. AddQuarters](/powerquery-m/date-addquarters), [date. AddWeeks](/powerquery-m/date-addweeks), [date. AddYears](/powerquery-m/date-addyears))
* Varaktigheter kan användas för datum-och tids beräkningar, men måste omvandlas till en annan typ innan de skrivs till en mottagare (aritmetiska operatorer, [#duration](/powerquery-m/sharpduration), [duration. dagar](/powerquery-m/duration-days), [varaktighet. timmar](/powerquery-m/duration-hours), [varaktighet. minuter](/powerquery-m/duration-minutes), [varaktighet. sekunder](/powerquery-m/duration-seconds), [varaktighet. TotalDays](/powerquery-m/duration-totaldays), [varaktighet. TotalHours](/powerquery-m/duration-totalhours), [duration. TotalMinutes](/powerquery-m/duration-totalminutes), [duration. TotalSeconds](/powerquery-m/duration-totalseconds))    
* De flesta vanliga, vetenskapliga och trigonometriska numeriska funktioner (alla funktioner under [åtgärder](/powerquery-m/number-functions#operations), [avrundning](/powerquery-m/number-functions#rounding)och [trigonometriska](/powerquery-m/number-functions#trigonometry) tecken *utom* Number. fakultet, Number. permutationer och Number. kombination)
* Ersättning ([ReplaceText](/powerquery-m/replacer-replacetext), [replacer. ReplaceValue](/powerquery-m/replacer-replacevalue), [text. Replace](/powerquery-m/text-replace), [text. Remove](/powerquery-m/text-remove))
* Extrahering av positions text[(text. PositionOf](/powerquery-m/text-positionof), [text. length](/powerquery-m/text-length), [text. start](/powerquery-m/text-start), [text. End](/powerquery-m/text-end), [text. mitten](/powerquery-m/text-middle), [text. ReplaceRange](/powerquery-m/text-replacerange), [text. RemoveRange](/powerquery-m/text-removerange))
* Grundläggande textformatering ([text. gemener](/powerquery-m/text-lower), [text. versaler](/powerquery-m/text-upper), [text. trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [text. PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [text. reversed](/powerquery-m/text-reverse))
* Datum-/tids funktioner ([datum. dag](/powerquery-m/date-day), [datum. månad](/powerquery-m/date-month), [datum. Year](/powerquery-m/date-year) [Time. Hour](/powerquery-m/time-hour), [Time. Minute](/powerquery-m/time-minute), [Time. Second](/powerquery-m/time-second), [date. DAYOFWEEK](/powerquery-m/date-dayofweek), [date. DayOfYear](/powerquery-m/date-dayofyear), [date. DaysInMonth](/powerquery-m/date-daysinmonth))
* If-uttryck (men förgreningar måste ha matchande typer)
* Rad filter som en logisk kolumn
* Tal-, text-, logiska-, datum-och datetime-konstanter

<a name="mergingjoining-tables"></a>Sammanfoga/koppla tabeller
----------------------
* Power Query skapar en kapslad koppling (Table. NestedJoin; användare kan också manuellt skriva [tabellen. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Användarna måste sedan expandera den kapslade kopplings kolumnen till en icke-kapslad koppling (Table. ExpandTableColumn, stöds inte i någon annan kontext).
* M-funktions   [tabellen. koppling](/powerquery-m/table-join) kan skrivas direkt för att undvika behovet av ytterligare ett expansions steg, men användaren måste se till att det inte finns några dubbletter av kolumn namn bland de kopplade tabellerna
* Kopplings typer som stöds:   [Inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* Både   [Value. equals](/powerquery-m/value-equals) och   [Value. NullableEquals](/powerquery-m/value-nullableequals) stöds som jämförelser av nyckel likheter

## <a name="group-by"></a>Gruppera efter

Använd [Table. Group](/powerquery-m/table-group) för att aggregera värden.
* Måste användas med en agg regerings funktion
* Sammansättnings funktioner som stöds:   [list. sum](/powerquery-m/list-sum),   [list. Count](/powerquery-m/list-count),   [list. genomsnitt](/powerquery-m/list-average),   [list. min](/powerquery-m/list-min),   [list. Max](/powerquery-m/list-max),   [list. StandardDeviation](/powerquery-m/list-standarddeviation),   [list. First](/powerquery-m/list-first), list.   [Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sortering

Använd [Table. sort](/powerquery-m/table-sort) för att sortera värden.

## <a name="reducing-rows"></a>Minska rader

Behåll och ta bort överkant, Behåll intervallet (motsvarande M-funktioner, endast stödda antal, inte villkor: [Table. firstd](/powerquery-m/table-firstn), [Table. Skip](/powerquery-m/table-skip), [Table. RemoveFirstN](/powerquery-m/table-removefirstn), [Table. Range](/powerquery-m/table-range), [Table. MinN](/powerquery-m/table-minn), [Table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Kända funktioner som inte stöds

| Funktion | Status |
| -- | -- |
| Table.PromoteHeaders | Stöds inte. Du kan åstadkomma samma resultat genom att ange "första raden som rubrik" i data uppsättningen. |
| Table.CombineColumns | Det här är ett vanligt scenario som inte stöds direkt, men som kan uppnås genom att lägga till en ny kolumn som sammanfogar två kolumner.  Till exempel Table. AddColumn (RemoveEmailColumn, "namn", varje [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Detta stöds i de flesta fall. Följande scenarier stöds inte: omvandling av sträng till valuta typ, transformering av sträng till tids typ, omvandling av sträng till procent typ. |
| Table.NestedJoin | Att bara göra en koppling leder till ett verifierings fel. Kolumnerna måste utökas för att den ska fungera. |
| Table.Distinct | Det finns inte stöd för att ta bort dubbla rader. |
| Table.RemoveLastN | Ta bort de nedersta raderna stöds inte. |
| Table.RowCount | Stöds inte, men kan uppnås genom att lägga till en anpassad kolumn som innehåller värdet 1, och sedan aggregera den kolumnen med list. sum. Table. Group stöds. | 
| Fel hantering på radnivå | Fel hantering på radnivå stöds inte för närvarande. Om du till exempel vill filtrera ut icke-numeriska värden från en kolumn, skulle en metod vara att omvandla text kolumnen till ett tal. Varje cell som inte kan transformera är i fel tillstånd och måste filtreras. Det här scenariot är inte möjligt i datatransformering Data Flow. |
| Table.Transpose | Stöds inte |
| Table.Pivot | Stöds inte |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en data datatransformering Power Query i ADF](wrangling-tutorial.md).
