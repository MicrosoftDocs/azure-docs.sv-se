---
title: Härledd kolumn omvandling i data flöde för mappning
description: Lär dig hur du omvandlar data i skala i Azure Data Factory med den härledda kolumn omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532021"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Härledd kolumn omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd omvandlingen för härledd kolumn för att skapa nya kolumner i ditt data flöde eller ändra befintliga fält.

## <a name="create-and-update-columns"></a>Skapa och uppdatera kolumner

När du skapar en härledd kolumn kan du antingen skapa en ny kolumn eller uppdatera en befintlig. I text rutan **kolumn** anger du i kolumnen som du skapar. Om du vill åsidosätta en befintlig kolumn i schemat kan du använda List rutan kolumn. Om du vill skapa den härledda kolumnens uttryck klickar du på text rutan **Ange uttryck** . Du kan antingen börja skriva uttrycket eller öppna uttrycks verktyget för att skapa din logik.

![Härledda kolumn inställningar](media/data-flow/create-derive-column.png "Härledda kolumn inställningar")

Om du vill lägga till fler härledda kolumner klickar du på **Lägg till** ovanför kolumn listan eller plus ikonen bredvid en befintlig härledd kolumn. Välj antingen **Lägg till kolumn** eller **Lägg till kolumn mönster**.

![Ny härledd kolumn markering](media/data-flow/add-derived-column.png "Ny härledd kolumn markering")

### <a name="column-patterns"></a>Kolumnmönster

I de fall där ditt schema inte uttryckligen definieras eller om du vill uppdatera en uppsättning kolumner i bulk, ska du skapa kolumnen patters. Med kolumn mönster kan du matcha kolumner med regler baserat på kolumnens metadata och skapa härledda kolumner för varje matchad kolumn. För mer information, lär [du dig att bygga kolumn mönster](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) i den härledda kolumn omvandlingen.

![Kolumnmönster](media/data-flow/column-pattern-derive.png "Kolumnmönster")

## <a name="building-schemas-using-the-expression-builder"></a>Skapa scheman med uttrycks verktyget

När du använder [uttrycks verktyget](concepts-data-flow-expression-builder.md)för mappning av data Flow kan du skapa, redigera och hantera dina härledda kolumner i avsnittet **härledda kolumner** . Alla kolumner som skapas eller ändras i omvandlingen visas i listan. Välj den kolumn eller det mönster som du redigerar interaktivt genom att klicka på kolumnens namn. Om du vill lägga till ytterligare en kolumn väljer du **Skapa ny** och väljer om du vill lägga till en enskild kolumn eller ett mönster.

![Skapa ny kolumn](media/data-flow/derive-add-column.png "Skapa ny kolumn")

När du arbetar med komplexa kolumner kan du skapa under kolumner. Det gör du genom att klicka på plus ikonen bredvid valfri kolumn och välja **Lägg till under kolumn**. Mer information om hur du hanterar komplexa typer i data flöde finns i [JSON-hantering i mappa data flöde](format-json.md#mapping-data-flow-properties).

![Lägg till under kolumn](media/data-flow/derive-add-subcolumn.png "Lägg till under kolumn")

Mer information om hur du hanterar komplexa typer i data flöde finns i [JSON-hantering i mappa data flöde](format-json.md#mapping-data-flow-properties).

![Lägg till komplex kolumn](media/data-flow/derive-complex-column.png "Lägg till kolumner")

### <a name="locals"></a>Lokala variabler

Om du delar logik över flera kolumner eller vill separera din logik kan du skapa en lokal i en härledd kolumn-omvandling. En lokal är en uppsättning logik som inte sprids över till följande omvandling. Du kan skapa lokala objekt i uttrycks verktyget genom att gå till **uttrycks element** och välja **språk**. Skapa en ny genom att välja **Skapa ny**.

![Skapa lokal](media/data-flow/create-local.png "Skapa lokal")

Lokala objekt kan referera till ett uttrycks element som en härledd kolumn, inklusive funktioner, indataparametrar, parametrar och andra språk. När du refererar till andra språk kan det bero på att den lokala referensen måste vara "över" den aktuella.

![Skapa lokal 2](media/data-flow/create-local-2.png "Skapa lokal 2")

Om du vill referera till en lokal i en härledd kolumn klickar du antingen på den lokala från vyn **uttrycks element** eller refererar till den med ett kolon framför dess namn. Till exempel skulle en lokal som heter Local1 refereras till av `:local1` . Om du vill redigera en lokal definition, Hovra över den i vyn uttrycks element och klicka på Penn ikonen.

![Använda lokala](media/data-flow/using-locals.png "Använda lokala")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en härledd kolumn med namnet `CleanData` som tar en inkommande data ström `MoviesYear` och skapar två härledda kolumner. Den första härledda kolumnen ersätter kolumnen `Rating` med gradering-värdet som en heltals typ. Den andra härledda kolumnen är ett mönster som matchar varje kolumn vars namn börjar med "filmer". För varje matchad kolumn skapar den en kolumn `movie` som är lika med värdet för den matchade kolumnen som föregås av movie_. 

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Härled exempel](media/data-flow/derive-script.png "Härled exempel")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [språket för mappning av data Flow-uttryck](data-flow-expression-functions.md).
