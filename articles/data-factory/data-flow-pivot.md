---
title: Pivot-transformering i data flöde för mappning
description: Pivotera data från rader till kolumner med Azure Data Factory mappa data flödets Pivot-transformering
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086684"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Pivot-transformering i data flöde för mappning


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd Pivot-transformeringen för att skapa flera kolumner från de unika rad värdena för en enskild kolumn. Pivot är en agg regerings omvandling där du väljer gruppera efter kolumner och genererar Pivot-kolumner med [mängd funktioner](data-flow-expression-functions.md#aggregate-functions).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Konfiguration

Pivot-transformeringen kräver tre olika indata: Gruppera efter kolumner, pivot-tangenten och hur du genererar de pivoterade kolumnerna

### <a name="group-by"></a>Gruppera efter

![Gruppera efter alternativ](media/data-flow/pivot2.png "Gruppera efter alternativ")

Välj vilka kolumner som ska aggregera de pivoterade kolumnerna. Utdata kommer att gruppera alla rader med samma grupp efter värden till en rad. Agg regeringen som görs i den pivoterade kolumnen görs över varje grupp.

Det här avsnittet är valfritt. Om ingen grupp efter kolumner har marker ATS aggregeras hela data strömmen och bara en rad returneras.

### <a name="pivot-key"></a>Pivot-nyckel

![Pivot-nyckel](media/data-flow/pivot3.png "Pivot-nyckel")

Pivot-tangenten är den kolumn vars rad värden får pivoteras i nya kolumner. Som standard skapas en ny kolumn för varje unikt rad värde i Pivot-transformeringen.

I avsnittet med namnet **värde** kan du ange vissa rad värden som ska pivoteras. Endast de rad värden som anges i det här avsnittet kommer att pivoteras. Om **null-värdet** aktive ras skapas en pivotad kolumn för null-värden i kolumnen.

### <a name="pivoted-columns"></a>Pivoterade kolumner

![Pivoterade kolumner](media/data-flow/pivot4.png "Pivoterade kolumner")

Generera ett sammanställt rad värde för varje grupp för varje unikt värde för Pivot-nyckel som blir en kolumn. Du kan skapa flera kolumner per Pivot-nyckel. Varje Pivot-kolumn måste innehålla minst en [mängd funktion](data-flow-expression-functions.md#aggregate-functions).

**Kolumn namns mönster:** Välj hur du vill formatera kolumn namnet för varje Pivot-kolumn. Namnet på den inkluderade kolumnen är en kombination av värdet för Pivot-tangenten, kolumn prefixet och valfritt prefix, tillräckligt, mellan tecken. 

**Kolumn ordning:** Om du genererar fler än en pivot-kolumn per Pivot-nyckel väljer du hur du vill att kolumnerna ska sorteras. 

**Kolumn-prefix:** Om du genererar fler än en pivot-kolumn per Pivot-nyckel anger du ett kolumn-prefix för varje kolumn. Den här inställningen är valfri om du bara har en pivoterad kolumn.

## <a name="help-graphic"></a>Hjälp bild

I hjälp bilden nedan visas hur olika Pivot-komponenter interagerar med varandra

![Pivotera hjälp grafik](media/data-flow/pivot5.png "Hjälp bild för Pivot")

## <a name="pivot-metadata"></a>Pivotera metadata

Om inga värden anges i konfiguration av Pivot-nyckel kommer de pivoterade kolumnerna att genereras dynamiskt vid körning. Antalet pivoterade kolumner motsvarar antalet unika värden för Pivot-nycklar multiplicerat med antalet pivottabeller. Eftersom detta kan vara ett ändrat nummer, visar UX inte kolumnens metadata på fliken **Granska** och det kommer inte att finnas någon kolumn spridning. Om du vill omvandla dessa kolumner använder du [kolumn mönster](concepts-data-flow-column-pattern.md) funktionerna i mappa data flöde. 

Om de angivna värdena för en piltangent anges visas de pivoterade kolumnerna i metadata. Kolumn namnen är tillgängliga för dig i inspektions-och mottagar mappningen.

### <a name="generate-metadata-from-drifted-columns"></a>Generera metadata från inaktiverade kolumner

Pivot genererar nya kolumn namn dynamiskt baserat på rad värden. Du kan lägga till dessa nya kolumner i metadata som kan refereras till senare i ditt data flöde. Om du vill göra det använder du åtgärden [Mappa](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) åtgärdad snabb åtgärd i data förhands granskning. 

![Pivotkolumner](media/data-flow/newpivot1.png "Mappa uppstaplade Pivot-kolumner")

### <a name="sinking-pivoted-columns"></a>Handfat med pivoterade kolumner

Även om pivoterade kolumner är dynamiska kan de fortfarande skrivas till mål data lagret. Aktivera **Tillåt schema avvikelse** i mottagar inställningarna. På så sätt kan du skriva kolumner som inte ingår i metadata. Du kommer inte att se de nya dynamiska namnen i metadata för kolumnen, men du kan använda alternativet schema drifts alternativ för att landa data.

### <a name="rejoin-original-fields"></a>Koppla från ursprungliga fält

Pivot-transformeringen kommer bara att projicera kolumnerna Group by och pivoted. Använd ett [själv kopplings](data-flow-join.md#self-join) mönster om du vill att dina utdata ska innehålla andra inmatnings kolumner.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Exempel

Skärmarna som visas i konfigurations avsnittet har följande data flödes skript:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Nästa steg

Prova [unpivot-transformeringen](data-flow-unpivot.md) för att omvandla kolumn värden till rad värden. 
