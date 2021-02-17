---
title: Skript för mappning av data flöde
description: Översikt över Data Factory data flödes skript kod – bakom språk
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534829"
---
# <a name="data-flow-script-dfs"></a>Data flödes skript (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data flödes skriptet (DFS) är underliggande metadata, ungefär som ett kodnings språk, som används för att köra omvandlingar som ingår i ett data flöde för mappning. Varje omvandling representeras av en serie egenskaper som ger den information som krävs för att köra jobbet på rätt sätt. Skriptet är synligt och redigerbart från ADF genom att klicka på knappen "skript" i det övre menyfliksområdet i webb läsar gränssnittet.

![Skript knapp](media/data-flow/scriptbutton.png "Skript knapp")

`allowSchemaDrift: true,`I en käll omvandling kan du till exempel instruera tjänsten att inkludera alla kolumner från käll data uppsättningen i data flödet även om de inte ingår i schema projektionen.

## <a name="use-cases"></a>Användningsfall
DFS skapas automatiskt av användar gränssnittet. Du kan klicka på skript knappen för att visa och anpassa skriptet. Du kan också generera skript utanför ADF-ANVÄNDARGRÄNSSNITTET och sedan skicka det till PowerShell-cmdleten. När du felsöker komplexa data flöden, kan det vara lättare att söka igenom skript koden – bakom i stället för att skanna in UI-diagrammets representation av dina flöden.

Här är några exempel på användnings fall:
- Program mässigt skapar många data flöden som är ganska identiska, d.v.s. "stämpla ut"-data flöden.
- Komplexa uttryck som är svåra att hantera i användar gränssnittet eller som resulterar i verifierings problem.
- Fel sökning och bättre förståelse för olika fel som returneras under körningen.

När du skapar ett data flödes skript som ska användas med PowerShell eller ett API måste du komprimera den formaterade texten till en enda rad. Du kan behålla TABB-och newlines som escape-tecken. Men texten måste vara formaterad för att få plats i en JSON-egenskap. Det finns en knapp i gränssnittet för skript redigeraren längst ned som formaterar skriptet som en enskild rad åt dig.

![Knappen Kopiera](media/data-flow/copybutton.png "Knappen Kopiera")

## <a name="how-to-add-transforms"></a>Lägga till transformeringar
Det krävs tre grundläggande steg för att lägga till transformeringar: lägga till kärn omvandlings data, dirigera om indataströmmen och sedan dirigera om utdataströmmen. Detta kan ses enklast i ett exempel.
Låt oss säga att vi börjar med en enkel källa för att ta med data flödet som följande:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Om vi bestämmer dig för att lägga till en härledd omvandling måste du först skapa en grundläggande omvandlings text, som har ett enkelt uttryck för att lägga till en ny versal kolumn med namnet `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Sedan tar vi befintliga DFS och lägger till omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Och nu dirigerar vi om den inkommande strömmen genom att identifiera vilken omvandling vi vill att den nya omvandlingen ska komma efter (i det här fallet `source1` ) och kopiera namnet på data strömmen till den nya omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Slutligen identifierar vi den omvandling som vi vill komma efter den här nya omvandlingen och ersätter dess indataströmmen (i det här fallet `sink1` ) med namnet på den nya omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Grundläggande om DFS
DFS består av en serie anslutna omvandlingar, inklusive källor, mottagare och andra som kan lägga till nya kolumner, filtrera data, koppla data och mycket mer. Vanligt vis är skriptet med start med en eller flera källor följt av många omvandlingar och slutar med en eller flera handfat.

Alla källor har samma grundläggande konstruktion:
```
source(
  source properties
) ~> source_name
```

En enkel källa med tre kolumner (movieId, title, genrer) skulle till exempel vara:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alla transformeringar än källor har samma grundläggande konstruktion:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Till exempel är en enkel härledd omvandling som tar en kolumn (rubrik) och skriver över den med en versal version så här:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Och en mottagare utan schema skulle bara vara:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Skript avsnitt

Skript kodfragment är en delnings bara kod för det data flödes skript som du kan använda för att dela över data flöden. Den här videon nedan pratar om hur du använder skript-kodfragment och använder data flödes skript för att kopiera och klistra in delar av skriptet bakom dina data flödes diagram:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Sammanställd sammanfattnings statistik
Lägg till en sammanställd omvandling i data flödet med namnet "SummaryStats" och klistra sedan in den här koden nedan för mängd funktionen i skriptet och ersätt den befintliga SummaryStats. Detta ger ett allmänt mönster för sammanfattnings statistik för data profiler.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Du kan också använda exemplet nedan för att räkna antalet unika rader och antalet distinkta rader i dina data. Exemplet nedan kan klistras in i ett data flöde med en sammanställd omvandling som kallas ValueDistAgg. I det här exemplet används en kolumn med namnet "title". Se till att ersätta "title" med kolumnen string i dina data som du vill använda för att hämta värde antal.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Ta med alla kolumner i en mängd
Detta är ett allmänt sammanslaget mönster som visar hur du kan behålla de återstående kolumnerna i metadata för utdata när du skapar agg regeringar. I det här fallet använder vi ```first()``` funktionen för att välja det första värdet i varje kolumn vars namn inte är "film". Om du vill använda detta skapar du en sammanställd omvandling med namnet DistinctRows och klistrar in det i skriptet ovanpå det befintliga DistinctRows-aggregerade skriptet.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Skapa finger avtryck för rad-hash 
Använd den här koden i ditt data flödes skript för att skapa en ny härledd kolumn ```DWhash``` som ger en ```sha1``` hash av tre kolumner.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Du kan också använda det här skriptet nedan för att generera en rad-hash med alla kolumner som finns i data strömmen, utan att behöva namnge varje kolumn:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg motsvarande
Den här koden fungerar som T-SQL- ```string_agg()``` funktionen och kommer att aggregera sträng värden till en matris. Du kan sedan omvandla matrisen till en sträng som ska användas med SQL-mål.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Antal uppdateringar, upsertar, infogningar, borttagningar
När du använder en Alter Row-omvandling kanske du vill räkna antalet uppdateringar, upsertar, infogningar, och ta bort resultatet från dina Alter Row-principer. Lägg till en sammanställd omvandling efter din Alter-rad och klistra in det här data flödes skriptet i den sammanställda inventeringen.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Distinkt rad med alla kolumner
Det här kodfragmentet lägger till en ny aggregerad omvandling till ditt data flöde som tar alla inkommande kolumner, genererar en hash som används för gruppering för att eliminera dubbletter, och sedan ange den första förekomsten av varje dubblett som utdata. Du behöver inte uttryckligen namnge kolumnerna, de skapas automatiskt från din inkommande data ström.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Sök efter NULL-värden i alla kolumner
Det här är ett kodfragment som du kan klistra in i ditt data flöde för att allmänt kontrol lera alla kolumner för NULL-värden. Den här tekniken utnyttjar schema avvikelser för att titta igenom alla kolumner i alla rader och använder en villkorlig delning för att separera raderna med NULL-värden från raderna utan NULLVÄRDEN. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Använda AutoMap-scheman med ett SELECT
När du behöver läsa in ett befintligt databas schema från en okänd eller dynamisk uppsättning inkommande kolumner måste du mappa de högra kolumnerna i omvandlingen av mottagare. Detta behövs bara när du läser in en befintlig tabell. Lägg till det här kodfragmentet innan du skapar en Välj som automatiskt mappar dina kolumner. Lämna din Sink-mappning till Auto-Map.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Bevara kolumn data typer
Lägg till det här skriptet inuti en härledd kolumn definition för att lagra kolumn namn och data typer från ditt data flöde till ett beständigt lager med hjälp av en mottagare.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Fyll nedåt
Så här implementerar du det vanliga problemet med "Autofyll" med data uppsättningar när du vill ersätta NULL-värden med värdet från föregående värde som inte är NULL i sekvensen. Observera att den här åtgärden kan ha negativa prestanda konsekvenser eftersom du måste skapa ett syntetiskt fönster för hela data uppsättningen med kategori värde "dummy". Dessutom måste du sortera efter ett värde för att skapa rätt datasekvens för att hitta föregående värde som inte är NULL. Det här kodfragmentet nedan skapar den syntetiska kategorin som "provdocka" och sorterar efter en surrogat nyckel. Du kan ta bort surrogat nyckeln och använda din egen dataspecifika sorterings nyckel. Det här kodfragmentet förutsätter att du redan har lagt till en käll omvandling som heter ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Glidande medelvärde
Glidande medelvärde kan implementeras mycket enkelt i data flöden med hjälp av en Windows-omvandling. I det här exemplet nedan skapas ett glidande medelvärde på 15 dagar av aktie kurser för Microsoft.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Nästa steg

Utforska data flöden genom att starta med [översikts artikeln för data flöden](concepts-data-flow-overview.md)
