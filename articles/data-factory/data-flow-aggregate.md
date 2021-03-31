---
title: Sammanställd transformering i mappnings data flödet
description: Lär dig hur du sammanställer data i skala i Azure Data Factory med den sammanställda omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531974"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Sammanställd transformering i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den sammanställda omvandlingen definierar agg regeringar för kolumner i dina data strömmar. Med uttrycks verktyget kan du definiera olika typer av agg regeringar som SUM, MIN, MAX och COUNT grupperade efter befintliga eller beräknade kolumner.

## <a name="group-by"></a>Gruppera efter

Välj en befintlig kolumn eller skapa en ny beräknad kolumn som ska användas som en Group by-sats för din AGG regering. Om du vill använda en befintlig kolumn väljer du den i list rutan. För att skapa en ny beräknad kolumn, Hovra över satsen och klicka på **beräknad kolumn**. Detta öppnar [uttrycks verktyget för data flöde](concepts-data-flow-expression-builder.md). När du har skapat en beräknad kolumn anger du namnet på kolumnen utdata i fältet **namn som** . Om du vill lägga till ytterligare en Group by-sats, Hovra över en befintlig sats och klicka på plus-ikonen.

![Sammanställd omvandlings grupp efter inställningar](media/data-flow/agg.png "Sammanställd omvandlings grupp efter inställningar")

En Group by-sats är valfri i en mängd omvandling.

## <a name="aggregate-columns"></a>Sammanställda kolumner

Gå till fliken **Aggregator** för att bygga agg regerings uttryck. Du kan antingen skriva över en befintlig kolumn med en agg regering eller skapa ett nytt fält med ett nytt namn. Agg regerings uttrycket anges i den högra rutan bredvid kolumn namns väljaren. Redigera uttrycket genom att klicka på text rutan och öppna uttrycks verktyget. Om du vill lägga till fler mängd kolumner klickar du på **Lägg till** ovanför kolumn listan eller plus ikonen bredvid en befintlig sammanslagen kolumn. Välj antingen **Lägg till kolumn** eller **Lägg till kolumn mönster**. Varje agg regerings uttryck måste innehålla minst en mängd funktion.

![Samlings inställningar](media/data-flow/aggregate-columns.png "Samlings inställningar")

> [!NOTE]
> I fel söknings läge kan uttrycks verktyget inte producera data för hands versionerna med mängd funktioner. Om du vill visa data förhands granskningar för sammanställda transformeringar stänger du uttrycks verktyget och visar data via fliken Data förhands granskning.

### <a name="column-patterns"></a>Kolumnmönster

Använd [kolumn mönster](concepts-data-flow-column-pattern.md) för att använda samma agg regering för en uppsättning kolumner. Detta är användbart om du vill behålla många kolumner från schemat när de ignoreras som standard. Använd en tumregel, till exempel `first()` för att bevara inmatade kolumner genom agg regeringen.

## <a name="reconnect-rows-and-columns"></a>Återanslut rader och kolumner

Sammanställda transformeringar liknar SQL-aggregerade urvals frågor. Kolumner som inte ingår i din Group by-sats eller mängd funktioner flödar inte genom utdata från din mängd omvandling. Om du vill inkludera andra kolumner i dina aggregerade utdata gör du något av följande:

* Använd en mängd funktion, till exempel `last()` eller `first()` för att inkludera den ytterligare kolumnen.
* Återanslut kolumnerna till utdataström med hjälp av [själv kopplings mönstret](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Tar bort duplicerade rader

En vanlig användning av den sammanställda omvandlingen tar bort eller identifierar dubbla poster i källdata. Den här processen kallas deduplicering. Baserat på en uppsättning Group by-nycklar, använder du en tumregel som du väljer för att avgöra vilken duplicerad rad som ska behållas. Vanliga heuristik är `first()` ,, `last()` `max()` och `min()` . Använd [kolumn mönster](concepts-data-flow-column-pattern.md) för att tillämpa regeln på alla kolumner utom gruppera efter-kolumner.

![Deduplicering](media/data-flow/agg-dedupe.png "Deduplicering")

I exemplet ovan används kolumner `ProductID` och används `Name` för gruppering. Om två rader har samma värden för dessa två kolumner betraktas de som dubbletter. I den här sammanställda omvandlingen kommer värdena för den första raden som matchas att behållas och alla andra kommer att tas bort. Med hjälp av syntaxen för kolumn mönster, alla kolumner vars namn inte är `ProductID` och `Name` mappas till deras befintliga kolumn namn och tilldelas värdet för de första matchade raderna. Schemat för utdata är detsamma som i schemat för indata.

För data verifierings scenarier `count()` kan funktionen användas för att räkna upp hur många dubbletter som finns.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan tar en inkommande data ström `MoviesYear` och grupper rader efter kolumn `year` . Omvandlingen skapar en agg regerings kolumn `avgrating` som utvärderas till medelvärdet för kolumnen `Rating` . Den sammanställda omvandlingen kallas `AvgComedyRatingsByYear` .

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Gruppera efter exempel](media/data-flow/agg-script1.png "Gruppera efter exempel")

![Samlings exempel](media/data-flow/agg-script2.png "Samlings exempel")

Data flödes skriptet för den här omvandlingen finns i kodfragmentet nedan.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Skript för sammanställd data flöde](media/data-flow/aggdfs1.png "Skript för sammanställd data flöde")

```MoviesYear```: Härledd kolumn som definierar år och rubrik kolumner ```AvgComedyRatingByYear``` : aggregerad omvandling för genomsnittlig bedömning av Comedies grupperat per år ```avgrating``` : namnet på den nya kolumnen som skapas för att innehålla det sammanlagda värdet

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Nästa steg

* Definiera Window-baserad agg regering med [fönster omvandlingen](data-flow-window.md)
