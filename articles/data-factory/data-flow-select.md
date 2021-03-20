---
title: Välj omvandling i data flöde för mappning
description: Azure Data Factory mappa data flöde Välj omvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040239"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Välj omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd Välj omvandling för att byta namn på, släppa eller ordna om kolumner. Den här omvandlingen ändrar inte raddata, men väljer vilka kolumner som ska spridas över underordnade. 

I en SELECT-omvandling kan användarna ange fasta mappningar, använda mönster för att utföra regelbaserade mappningar eller aktivera automatisk mappning. Fasta och regelbaserade mappningar kan båda användas inom samma Select-omvandling. Om en kolumn inte matchar någon av de definierade mappningarna kommer den att tas bort.

## <a name="fixed-mapping"></a>Fast mappning

Om det finns färre än 50 kolumner definierade i projektionen så har alla definierade kolumner en fast mappning som standard. En fast mappning tar en definierad, inkommande kolumn och mappar ett exakt namn.

![Fast mappning](media/data-flow/fixedmapping.png "Fast mappning")

> [!NOTE]
> Du kan inte mappa eller byta namn på en nedstaplad kolumn med en fast mappning

### <a name="mapping-hierarchical-columns"></a>Mappa hierarkiska kolumner

Fasta mappningar kan användas för att mappa en under kolumn i en hierarkisk kolumn till en kolumn på den översta nivån. Om du har en definierad hierarki använder du List rutan kolumn för att välja en under kolumn. I SELECT-omvandlingen skapas en ny kolumn med under kolumnens värde och data typ.

![hierarkisk mappning](media/data-flow/select-hierarchy.png "hierarkisk mappning")

## <a name="rule-based-mapping"></a>Regel baserad mappning


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Om du vill mappa många kolumner samtidigt eller skicka efterföljande kolumner, använder du regelbaserade mappningar för att definiera dina mappningar med hjälp av kolumn mönster. Matchning baserat på `name` `type` kolumnerna,, `stream` och `position` . Du kan ha en kombination av fasta och regelbaserade mappningar. Som standard är alla projektioner med fler än 50 kolumner som standard en regelbaserade mappning som matchar i varje kolumn och som utvärderar det angivna namnet. 

Om du vill lägga till en regelbaserade mappning klickar du på **Lägg till mappning** och väljer **regel baserad mappning**.

![Skärm bild som visar regel baserad mappning som valts från Lägg till mappning.](media/data-flow/rule2.png "Regel baserad mappning")

Varje regelbaserade mappning kräver två indata: det villkor som ska matchas med och vad som ska namnge varje mappad kolumn. Båda värdena anges via [uttrycks verktyget](concepts-data-flow-expression-builder.md). Ange ditt booleska matchnings villkor i rutan till vänster-uttryck. I rutan till höger uttryck anger du vad den matchade kolumnen ska mappas till.

![Skärm bild som visar en mappning.](media/data-flow/rule-based-mapping.png "Regel baserad mappning")

Använd `$$` syntax för att referera till Indataporten för en matchad kolumn. Använd bilden ovan som ett exempel, säg att en användare vill matcha i alla sträng kolumner vars namn är kortare än sex tecken. Om en inkommande kolumn har namngetts `test` , `$$ + '_short'` kommer uttrycket att byta namn på kolumnen `test_short` . Om det är den enda mappning som finns, kommer alla kolumner som inte uppfyller villkoret att tas bort från de data som returneras.

Mönster matchar både inkompatibla och definierade kolumner. Om du vill se vilka definierade kolumner som mappas av en regel klickar du på glasögon-ikonen bredvid regeln. Verifiera dina utdata med data förhands granskning.

### <a name="regex-mapping"></a>Regex-mappning

Om du klickar på ikonen för nedåtriktadt läge kan du ange ett regex-mappnings villkor. Ett regex-mappnings villkor matchar alla kolumn namn som matchar det angivna regex-villkoret. Detta kan användas tillsammans med standard regelbaserade mappningar.

![Skärm bild som visar regex-mappningens villkor med hierarkinivå och namn matchningar.](media/data-flow/regex-matching.png "Regel baserad mappning")

Ovanstående exempel matchar i regex-mönster `(r)` eller kolumn namn som innehåller gemener r. På samma sätt som med standard regelbaserade mappningar ändras alla matchade kolumner av villkoret till höger med `$$` syntax.

Om du har flera regex-matchningar i ditt kolumn namn kan du referera till de olika matchningar som använder `$n` WHERE ' n ' som matchar. Till exempel refererar "$2" till den andra matchningen inom ett kolumn namn.

### <a name="rule-based-hierarchies"></a>Regelbaserade hierarkier

Om din definierade projektion har en hierarki kan du använda regelbaserade mappningar för att mappa under kolumnerna hierarkier. Ange ett matchande villkor och den komplexa kolumn vars under kolumner du vill mappa. Varje matchad under kolumn kommer att returneras med regeln "name as".

![Skärm bild som visar en regel baserad mappning som använder för en hierarki.](media/data-flow/rule-based-hierarchy.png "Regel baserad mappning")

Ovanstående exempel matchar på alla under kolumner i komplex kolumn `a` . `a` innehåller två under kolumner `b` och `c` . Utdata-schemat kommer att innehålla två kolumner `b` och `c` som villkoret ' name as ' `$$` .

### <a name="parameterization"></a>Parameterisering

Du kan Parameterisera kolumn namn med hjälp av regelbaserade mappningar. Använd nyckelordet ```name``` för att matcha inkommande kolumn namn mot en parameter. Om du till exempel har en data flödes parameter ```mycolumn``` kan du skapa en regel som matchar alla kolumn namn som är lika med ```mycolumn``` . Du kan byta namn på den matchade kolumnen till en hårdkodad sträng, till exempel Business Key, och referera till den explicit. I det här exemplet är matchnings villkoret ```name == $mycolumn``` och namn villkoret ' Business Key '. 

## <a name="auto-mapping"></a>Automatisk mappning

När du lägger till en SELECT-omvandling kan **automatisk mappning** aktive ras genom att skjutreglaget för automatisk mappning växlar. Med automatisk mappning mappar Select-omvandlingen alla inkommande kolumner, exklusive dubbletter, med samma namn som indatatyperna. Detta inkluderar använda kolumner, vilket innebär att utdata kan innehålla kolumner som inte har definierats i schemat. Mer information om inaktiverade kolumner finns i [schema avvikelser](concepts-data-flow-schema-drift.md).

![Automatisk mappning](media/data-flow/automap.png "Automatisk mappning")

Med automatisk mappning aktive ras den valda omvandlingen hoppa över duplicerade inställningar och ange ett nytt alias för befintliga kolumner. Aliasning är användbart när du gör flera kopplingar eller uppslag i samma ström och i självkopplings scenarier. 

## <a name="duplicate-columns"></a>Duplicera kolumner

Som standard släpper den valda omvandlingen duplicerade kolumner i både indata och utdata-projektion. Dubbletter av inmatade kolumner kommer ofta från sammanfognings-och Sök omvandlingar där kolumn namn dupliceras på varje sida av kopplingen. Duplicerade utdatakolumner kan inträffa om du mappar två olika kolumner till samma namn. Välj om du vill släppa eller släppa dubbletter av kolumner genom att växla kryss rutan.

![Hoppa över dubbletter](media/data-flow/select-skip-dup.png "Hoppa över dubbletter")

## <a name="ordering-of-columns"></a>Sortering av kolumner

Ordningen på mappningar bestämmer ordningen för utdatakolumner. Om en inmatad kolumn har mappats flera gånger kommer endast den första mappningen att ske. För alla duplicerade kolumner behålls den första matchningen.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Exempel

Nedan visas ett exempel på en urvals mappning och dess data flödes skript:

![Välj skript exempel](media/data-flow/select-script-example.png "Välj skript exempel")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Nästa steg
* När du har använt Välj för att byta namn på, ändra ordning och alias använder du [omvandling av mottagare](data-flow-sink.md) för att använda data i ett data lager.
