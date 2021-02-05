---
title: Uttrycks verktyg i data flöde för mappning
description: Bygg uttryck genom att använda uttrycks verktyget i att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584931"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Bygg uttryck i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I mappnings data flödet anges många omvandlings egenskaper som uttryck. Dessa uttryck består av kolumn värden, parametrar, funktioner, operatorer och litteraler som utvärderar till data typen Spark vid körning. Kart data flöden har en särskild erfarenhet som syftar till att hjälpa dig att skapa dessa uttryck som kallas **uttrycks verktyg**. Att använda  [IntelliSense](/visualstudio/ide/using-intellisense) -kod komplettering för markering, syntaxkontroll och autokomplettering är att uttrycks verktyget är utformat för att göra det enkelt att skapa data flöden. Den här artikeln förklarar hur du använder uttrycks verktyget för att effektivt bygga affärs logiken.

![Uttrycks verktyg](media/data-flow/expresion-builder.png "Uttrycksverktyget")

## <a name="open-expression-builder"></a>Öppna uttrycks verktyget

Det finns flera start punkter för att öppna uttrycks verktyget. Dessa är alla beroende av den speciella kontexten för omvandlingen av data flöde. Det vanligaste användnings fallet är i transformeringar som [härledd kolumn](data-flow-derived-column.md) och [mängd](data-flow-aggregate.md) där användarna skapar eller uppdaterar kolumner med data flödets uttrycks språk. Uttrycks verktyget kan öppnas genom att välja **Öppna uttrycks verktyg** ovanför listan med kolumner. Du kan också klicka på en kolumn kontext och öppna uttrycks verktyget direkt till uttrycket.

![Öppna uttrycks verktyget Härled](media/data-flow/open-expression-builder-derive.png "Öppna uttrycks verktyget Härled")

I vissa transformeringar som [filter](data-flow-filter.md), öppnar uttrycks verktyget genom att klicka på ett blått uttryck text ruta. 

![Rutan blå uttryck](media/data-flow/expressionbox.png "Rutan blå uttryck")

När du refererar till kolumner i en matchande eller Group by-villkor kan ett uttryck extrahera värden från kolumner. Om du vill skapa ett uttryck väljer du **beräknad kolumn**.

![Alternativ för beräknad kolumn](media/data-flow/computedcolumn.png "Alternativ för beräknad kolumn")

I de fall där ett uttryck eller ett tecken värde är giltiga indata, väljer du **Lägg till dynamiskt innehåll** för att skapa ett uttryck som utvärderas till ett exakt värde.

![Alternativet Lägg till dynamiskt innehåll](media/data-flow/add-dynamic-content.png "Alternativet Lägg till dynamiskt innehåll")

## <a name="expression-elements"></a>Uttrycks element

I mappnings data flöden kan uttryck bestå av kolumn värden, parametrar, funktioner, lokala variabler, operatorer och litteraler. Dessa uttryck måste utvärderas till en spark-datatyp som String, Boolean eller Integer.

![Uttrycks element](media/data-flow/expression-elements.png "Uttrycks element")

### <a name="functions"></a>Functions

Mappning av data flöden har inbyggda funktioner och operatorer som kan användas i uttryck. En lista över tillgängliga funktioner finns i [språk referens för mappnings data flöde](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Adress mat ris index

Använd hakparenteser ([]) för att komma åt ett speciellt element vid hantering av kolumner eller funktioner som returnerar mat ris typer. Om indexet inte finns utvärderas uttrycket i NULL.

![Expression Builder-matris](media/data-flow/expression-array.png "För hands version av uttrycks data")

> [!IMPORTANT]
> I mappnings data flöden är matriser en enda, vilket innebär att det första elementet refereras till av index ett. Till exempel kan mat ris [1] komma åt det första elementet i en matris med namnet "mat".

### <a name="input-schema"></a>Schema för indatamängd

Om data flödet använder ett definierat schema i någon av dess källor, kan du referera till en kolumn efter namn i många uttryck. Om du använder schema avvikelser kan du referera till kolumner som uttryckligen använder `byName()` eller- `byNames()` funktioner eller matcha med hjälp av kolumn mönster.

#### <a name="column-names-with-special-characters"></a>Kolumn namn med specialtecken

Om du har kolumn namn som innehåller specialtecken eller mellanslag, omger du namnet med klammerparenteser för att referera dem i ett uttryck.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parametrar

Parametrar är värden som skickas till ett data flöde vid körning från en pipeline. Om du vill referera till en parameter klickar du antingen på parametern från vyn **uttrycks element** eller hänvisar till den med ett dollar tecken framför dess namn. En parameter som kallas Parameter1 skulle till exempel refereras till av `$parameter1` . Mer information finns i [Parameters-mappning av data flöden](parameters-data-flow.md).

### <a name="cached-lookup"></a>Cachelagrad sökning

Med en cachelagrad sökning kan du göra en infogad sökning efter utdata från en cachelagrad mottagare. Det finns två funktioner som kan användas på varje mottagare `lookup()` och `outputs()` . Syntaxen som används för att referera till dessa funktioner är `cacheSinkName#functionName()` . Mer information finns i [cache-mottagare](data-flow-sink.md#cache-sink).

`lookup()` tar i de matchande kolumnerna i den aktuella omvandlingen som parametrar och returnerar en komplex kolumn som motsvarar den rad som matchar nyckel kolumnerna i cache-sinken. Den komplexa kolumnen som returnerades innehåller en under kolumn för varje kolumn som har mappats i cache-sinken. Om du till exempel hade en felkod för cacheuppdatering `errorCodeCache` som hade en nyckel kolumns matchning i koden och en kolumn med namnet `Message` . Anrop `errorCodeCache#lookup(errorCode).Message` returnerar meddelandet som motsvarar koden som skickades. 

`outputs()` tar inga parametrar och returnerar hela cache-sinken som en matris med komplexa kolumner. Detta kan inte anropas om nyckel kolumner anges i sinken och endast ska användas om det finns ett litet antal rader i cache-sinken. Ett vanligt användnings fall lägger till max värdet för en stegvis ökande nyckel. Om en enskild aggregerad rad `CacheMaxKey` innehåller en kolumn `MaxKey` kan du referera till det första värdet genom att anropa `CacheMaxKey#outputs()[1].MaxKey` .

![Cachelagrad sökning](media/data-flow/cached-lookup-example.png "Cachelagrad sökning")

### <a name="locals"></a>Lokala variabler

Om du delar logik över flera kolumner eller vill separera din logik kan du skapa en lokal i en härledd column\. Om du vill referera till en lokal, klickar du antingen på den lokala från vyn **uttrycks element** eller refererar till den med ett kolon framför dess namn. Till exempel skulle en lokal som heter Local1 refereras till av `:local1` . Läs mer om lokala i den [härledda kolumn dokumentationen](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Resultat för för hands versions uttryck

Om [fel söknings läge](concepts-data-flow-debug-mode.md) är aktiverat kan du interaktivt använda fel söknings kluster för att förhandsgranska vad ditt uttryck utvärderas till. Uppdatera resultatet av data förhands granskningen genom att välja **Uppdatera** bredvid data förhands granskning. Du kan se utdata för varje rad med kolumnerna för indata.

![Pågående för hands version](media/data-flow/preview-expression.png "För hands version av uttrycks data")

## <a name="string-interpolation"></a>String-interpolation

När du skapar långa strängar som använder uttrycks element, använder du String-interpolation för att enkelt skapa komplex sträng logik. Med hjälp av String-interpolation undviks en omfattande sträng sammanfogning när parametrar tas med i frågesträngar. Använd dubbla citat tecken för att omsluta textuella sträng text med uttryck. Du kan inkludera uttrycks funktioner, kolumner och parametrar. Om du vill använda uttrycks syntax, omger du det med klammerparenteser,

Några exempel på String-interpolation:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> När du använder syntaxen för String-interpolation i SQL-källfiler måste frågesträngen finnas på en enda rad, utan "/n".

## <a name="commenting-expressions"></a>Kommentera uttryck

Lägg till kommentarer till dina uttryck med hjälp av enradig syntax med en rad och flera rader.

Följande exempel är giltiga kommentarer:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Om du lägger till en kommentar överst i uttrycket, visas den i text rutan omvandling för att dokumentera dina omvandlings uttryck.

![Kommentar i text rutan omvandling](media/data-flow/comment-expression.png "Kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Många uttrycks språk funktioner använder syntaxen för reguljära uttryck. När du använder reguljära uttrycks funktioner försöker uttrycks verktyget tolka ett omvänt snedstreck ( \\ ) som en Escape-teckensekvens. När du använder omvända snedstreck i det reguljära uttrycket, omger du hela regexen i baktick ( \` ) eller använder ett dubbelt omvänt snedstreck.

Ett exempel som använder baktick:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Ett exempel som använder dubbla snedstreck:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Kortkommandon

Nedan visas en lista med tillgängliga genvägar i uttrycks verktyget. De flesta IntelliSense-genvägar är tillgängliga när du skapar uttryck.

* CTRL + K CTRL + C: kommentera hela raden.
* CTRL + K CTRL + U: ta bort kommentar.
* F1: ge redigerings hjälp kommandon.
* Alt + nedåtpil: Flytta ned den aktuella raden.
* ALT + UPPIL tangent: flytta upp aktuell rad.
* CTRL + blank steg: Visa Sammanhangs hjälp.

## <a name="commonly-used-expressions"></a>Ofta använda uttryck

### <a name="convert-to-dates-or-timestamps"></a>Konvertera till datum eller tidsstämplar

Om du vill inkludera sträng litteraler i dina Tidsstämpelns utdata kan du figursätta konverteringen i ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Använd för att konvertera millisekunder från epok till datum eller tidsstämpel `toTimestamp(<number of milliseconds>)` . Om tiden kommer i sekunder multiplicerar du med 1 000.

```toTimestamp(1574127407*1000l)```

Efterföljande "l" i slutet av föregående uttryck avser omvandling till en lång typ som infogad syntax.

### <a name="find-time-from-epoch-or-unix-time"></a>Hitta tiden från epok eller UNIX-tid

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' ÅÅÅÅ-MM-dd HH: mm: SS. SSS ')) * 1000

## <a name="next-steps"></a>Nästa steg

[Börja bygga data omvandlings uttryck](data-flow-expression-functions.md)
