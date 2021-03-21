---
title: Schema avvikelse i mappnings data flödet
description: Bygg elastiska data flöden i Azure Data Factory med schema avvikelse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027490"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Schema avvikelse i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Schema avvikelse är det fall där dina källor ofta ändrar metadata. Fält, kolumner och, typer kan läggas till, tas bort eller ändras i farten. Utan hantering av schema avvikelser blir ditt data flöde sårbart för överordnade data käll ändringar. Vanliga ETL-mönster fungerar inte när inkommande kolumner och fält ändras eftersom de tenderar att vara knutna till dessa käll namn.

För att skydda mot schema drift är det viktigt att du har funktioner i ett data flödes verktyg som gör det möjligt för dig, som en data tekniker, att:

* Definiera källor som har föränderligt fält namn, data typer, värden och storlekar
* Definiera omvandlings parametrar som kan användas med data mönster i stället för hårdkodade fält och värden
* Definiera uttryck som förstår mönster för att matcha inkommande fält, i stället för att använda namngivna fält

Azure Data Factory inbyggt stöder flexibla scheman som ändras från körning till körning så att du kan bygga Generic data Transformation Logic utan att behöva kompilera om dina data flöden.

Du måste fatta ett arkitektur beslut i ditt data flöde för att acceptera schema avvikelser i flödet. När du gör detta kan du skydda dig mot schema ändringar från källorna. Du förlorar dock tidigt bindningen för dina kolumner och typer i ditt data flöde. Azure Data Factory behandlar schema avvikelse flöden som sena bindnings flöden, så när du skapar dina omvandlingar är de nedstaplade kolumn namnen inte tillgängliga i schema vyerna i hela flödet.

Den här videon ger en introduktion till några av de komplexa lösningar som du enkelt kan bygga i ADF med data flödets schema funktion. I det här exemplet skapar vi återanvändbara mönster baserade på flexibla databas scheman:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Schema avvikelse i källa

Kolumner som kommer till ditt data flöde från din käll definition definieras som "inställt" när de inte finns i din käll projektion. Du kan visa käll projektionen från fliken projektion i käll omvandlingen. När du väljer en data uppsättning för källan tar ADF automatiskt schemat från data uppsättningen och skapar en projektion från den data uppsättningens schema definition.

I en käll omvandling definieras schema avvikelsen som att läsa kolumner som inte har definierats i data uppsättnings schemat. Om du vill aktivera schema avvikelse kontrollerar du **Tillåt schema avvikelse** i din käll omvandling.

![Schema avvikelse källa](media/data-flow/schemadrift001.png "Schema avvikelse källa")

När schema avvikelsen är aktive rad läses alla inkommande fält från din källa under körningen och skickas genom hela flödet till mottagaren. Som standard tas alla nyligen identifierade kolumner, som kallas för inkommande *kolumner*, emot som en sträng data typ. Om du vill att data flödet automatiskt ska härleda data typer av inaktuella kolumner kontrollerar du **härledda kolumn typer** i dina käll inställningar.

## <a name="schema-drift-in-sink"></a>Schema avvikelse i mottagare

I en Sink-omvandling är schema avvikelse när du skriver ytterligare kolumner ovanpå vad som definieras i data inmatnings schema. Om du vill aktivera schema avvikelse kontrollerar du **Tillåt schema avvikelse** i din Sink-omvandling.

![Schema avvikelse mottagare](media/data-flow/schemadrift002.png "Schema avvikelse mottagare")

Om schema avvikelse är aktiverat kontrollerar du att skjutreglaget för **automatisk mappning** på fliken mappning är aktiverat. Med det här skjutreglaget på, skrivs alla inkommande kolumner till ditt mål. Annars måste du använda regelbaserade mappningar för att skriva förbrukade kolumner.

![Automatisk mappning av mottagare](media/data-flow/automap.png "Automatisk mappning av mottagare")

## <a name="transforming-drifted-columns"></a>Omvandla informerade kolumner

När ditt data flöde har förfallna kolumner, kan du komma åt dem i dina omvandlingar med följande metoder:

* Använd `byPosition` uttryck och `byName` för att explicit referera till en kolumn efter namn eller positions nummer.
* Lägg till ett kolumn mönster i en härledd kolumn eller aggregerad omvandling så att den matchar valfri kombination av namn, ström, position, ursprung eller typ
* Lägg till regelbaserade mappningar i en urvals-eller Sink-omvandling för att matcha nedsänkta kolumner till kolumnalias i kolumner via ett mönster

Mer information om hur du implementerar kolumn mönster finns [i kolumn mönster i mappa data flöde](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Snabb åtgärd för att mappa förstaplade kolumner

Om du vill referera till påpekade kolumner kan du snabbt skapa mappningar för dessa kolumner via snabb åtgärden för förhands granskning av data. När [fel söknings läget](concepts-data-flow-debug-mode.md) är på går du till fliken Data förhands granskning och klickar på **Uppdatera** för att hämta en data för hands version. Om Data Factory upptäcker att det finns inaktuella kolumner kan du klicka på **Mappa** och generera en härledd kolumn som gör att du kan referera till alla nedstaplade kolumner i schema vyerna.

![Skärm bild som visar fliken Data förhands granskning med inaktuellt inringat.](media/data-flow/mapdrifted1.png "Kartning")

I den genererade härledda kolumn-omvandlingen mappas varje nedstaplad kolumn till dess identifierade namn och datatyp. I data förhands granskningen ovan identifieras kolumnen ' movieId ' som ett heltal. När du har klickat på **kartan** definieras movieId i den härledda kolumnen som `toInteger(byName('movieId'))` och tas med i schema vyerna i efterföljande transformeringar.

![Skärm bild som visar fliken Inställningar för den härledda kolumnen.](media/data-flow/mapdrifted2.png "Kartning")

## <a name="next-steps"></a>Nästa steg
I [data flödets uttrycks språk](data-flow-expression-functions.md)hittar du ytterligare funktioner för kolumn mönster och schema avvikelser, inklusive "byName" och "byPosition".
