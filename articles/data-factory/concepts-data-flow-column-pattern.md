---
title: Kolumn mönster i Azure Data Factory mappa data flöde
description: Skapa generaliserade data omvandlings mönster med kolumn mönster i Azure Data Factory mappa data flöden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94845014"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Använda kolumn mönster i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med flera mappnings data flödes omvandlingar kan du referera till mall-kolumner baserat på mönster i stället för hårdkodade kolumn namn. Den här matchningen kallas för *kolumn mönster*. Du kan definiera mönster för att matcha kolumner baserat på namn, datatyp, ström, ursprung eller position i stället för att kräva exakta fält namn. Det finns två scenarier där kolumn mönster är användbara:

* Om inkommande käll fält ändras ofta, till exempel vid ändring av kolumner i textfiler eller NoSQL-databaser. Det här scenariot kallas [schema avvikelse](concepts-data-flow-schema-drift.md).
* Om du vill göra en gemensam åtgärd i en stor grupp kolumner. Om du till exempel vill omvandla varje kolumn som har "total" i dess kolumn namn till ett dubbelt värde.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolumn mönster i härledd kolumn och mängd

Om du vill lägga till ett kolumn mönster i en härledd kolumn-, samlings-eller fönster omvandling klickar du på **Lägg till** ovanför kolumn listan eller plus ikonen bredvid en befintlig härledd kolumn. Välj **Lägg till kolumn mönster**.

![Skärm bild som visar plus ikonen för att lägga till ett kolumn mönster.](media/data-flow/add-column-pattern.png "Kolumnmönster")

Använd [uttrycks verktyget](concepts-data-flow-expression-builder.md) för att ange matchnings villkor. Skapa ett booleskt uttryck som matchar kolumner baserat på `name` kolumnens,,, `type` `stream` `origin` och `position` . Mönstret påverkar alla kolumner, som anges eller definieras, där villkoret returnerar true.

De två uttrycks rutorna under matchnings villkoret anger de nya namnen och värdena för de berörda kolumnerna. Används `$$` för att referera till det befintliga värdet för det matchade fältet. Rutan till vänster-uttryck definierar det namn och den högra resultat rutan som definierar värdet.

![Skärm bild som visar fliken Inställningar för den härledda kolumnen.](media/data-flow/edit-column-pattern.png "Kolumnmönster")

Mönstret ovan matchar alla kolumner av typen Double och skapar en härledd kolumn per matchning. Genom att ange `$$` kolumn namn fältet uppdateras varje matchad kolumn med samma namn. Värdet för varje kolumn är det befintliga värdet avrundat till två decimaler.

För att kontrol lera att ditt matchnings villkor är korrekt kan du validera schemat för definierade kolumner på fliken **Granska** eller hämta en ögonblicks bild av data på fliken **data förhands granskning** . 

![Skärm bild som visar fliken utdata schema.](media/data-flow/columnpattern3.png "Kolumnmönster")

### <a name="hierarchical-pattern-matching"></a>Hierarkiskt mönster matchning

Du kan också bygga mönster matchning i komplexa hierarkiska strukturer. Expandera avsnittet `Each MoviesStruct that matches` där du kommer att uppmanas att ange varje hierarki i data strömmen. Du kan sedan bygga matchande mönster för egenskaper inom den valda hierarkin.

![Skärm bild som visar hierarkiskt kolumn mönster.](media/data-flow/patterns-hierarchy.png "Kolumn mönster i hierarkier")

## <a name="rule-based-mapping-in-select-and-sink"></a>Regel baserad mappning i SELECT och Sink

När du mappar kolumner i källa och väljer omvandlingar kan du lägga till antingen fast mappning eller regelbaserade mappningar. Matchning baserat på `name` `type` kolumnerna,, `stream` , `origin` och `position` . Du kan ha en kombination av fasta och regelbaserade mappningar. Som standard är alla projektioner med fler än 50 kolumner som standard en regelbaserade mappning som matchar i varje kolumn och som utvärderar det angivna namnet. 

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

### <a name="rule-based-hierarchies"></a>Regelbaserade hierarkier

Om din definierade projektion har en hierarki kan du använda regelbaserade mappningar för att mappa under kolumnerna hierarkier. Ange ett matchande villkor och den komplexa kolumn vars under kolumner du vill mappa. Varje matchad under kolumn kommer att returneras med regeln "name as".

![Skärm bild som visar en regel baserad mappning som använder för en hierarki.](media/data-flow/rule-based-hierarchy.png "Regel baserad mappning")

Ovanstående exempel matchar på alla under kolumner i komplex kolumn `a` . `a` innehåller två under kolumner `b` och `c` . Utdata-schemat kommer att innehålla två kolumner `b` och `c` som villkoret ' name as ' `$$` .

## <a name="pattern-matching-expression-values"></a>Mönster matchnings uttrycks värden.

* `$$` översätts till namnet eller värdet för varje matchning vid körning. Tänk på `$$` som likvärdigt med `this` .
* `name` representerar namnet på varje inkommande kolumn
* `type` representerar data typen för varje inkommande kolumn
* `stream` representerar namnet som är kopplat till varje data ström eller omvandling i ditt flöde
* `position` är ordnings punkten för kolumner i ditt data flöde
* `origin` är omvandlingen där en kolumn kommer från eller senast uppdaterades

## <a name="next-steps"></a>Nästa steg
* Läs mer om data flödes [uttrycks språket](data-flow-expression-functions.md) för data omvandlingar
* Använd kolumn mönster i [omvandling av mottagare](data-flow-sink.md) och [Välj omvandling](data-flow-select.md) med regelbaserade mappning
