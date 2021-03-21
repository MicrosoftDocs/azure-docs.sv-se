---
title: Lösa data förvrängning – Azure Data Lake verktyg för Visual Studio
description: Felsöka potentiella lösningar för data sneda problem med hjälp av Azure Data Lake verktyg för Visual Studio.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219198"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Lösa problem med datasnedställning med hjälp av Azure Data Lake Tools för Visual Studio

## <a name="what-is-data-skew"></a>Vad är data skevning?

En kortfattad uppgift är ett övergivet värde. Tänk dig att du har tilldelat 50 skatte granskare för att granska moms returer, en granskare för varje AMERIKANSKt tillstånd. Wyoming-granskaren, eftersom populationen där är liten, har lite att göra. I Kalifornien förvaras dock granskaren mycket upptagen på grund av tillståndets stora befolkning.
    ![Exempel på data sneda problem](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

I vårt scenario fördelas data inte jämnt över alla skatte granskningar, vilket innebär att vissa granskningar måste arbeta mer än andra. I ditt eget jobb får du ofta situationer som till exempel för skatte granskning här. Med fler tekniska villkor får ett hörn mycket mer data än dess motparter, en situation som gör att form hörnet fungerar mer än de andra och som slutligen saktar ned ett helt jobb. Vad som är värre kan det hända att jobbet Miss söker, eftersom hörnen kan ha, till exempel en begränsning på 5 timmar och en minnes begränsning på 6 GB.

## <a name="resolving-data-skew-problems"></a>Lösa problem med data förvrängning

Azure Data Lake verktyg för Visual Studio kan hjälpa dig att identifiera om ditt jobb har ett problem med data skevningen. Om det finns ett problem kan du lösa det genom att testa lösningarna i det här avsnittet.

## <a name="solution-1-improve-table-partitioning"></a>Lösning 1: förbättra tabell partitionering

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Alternativ 1: filtrera värdet skevad nyckel i förväg

Om den inte påverkar affärs logiken kan du filtrera de högre frekvens värdena i förväg. Om det till exempel finns många 000-000-000 i kolumn-GUID kanske du inte vill sätta samman det värdet. Innan du sammanställer kan du skriva "där GUID! =" 000-000-000 "" för att filtrera värdet för hög frekvens.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Alternativ 2: Välj en annan partition eller distributions nyckel

I föregående exempel kan du, om du vill att du bara vill kontrol lera arbets belastningen för skatte granskning alla över landet/regionen, förbättra data distributionen genom att välja ID-numret som nyckel. Om du väljer en annan partition eller distributions nyckel kan du ibland distribuera data jämnt, men du måste se till att det här alternativet inte påverkar din affärs logik. Om du till exempel vill beräkna skatte summan för varje tillstånd kan du välja att ange ett _tillstånd_ som partitionsnyckel. Om du fortsätter att uppleva det här problemet kan du prova att använda alternativ 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Alternativ 3: Lägg till fler partitioner eller distributions nycklar

I stället för att endast använda _tillstånd_ som partitionsnyckel kan du använda mer än en nyckel för partitionering. Överväg till exempel att lägga till _zip-kod_ som en ytterligare partitionsnyckel för att minska storleken på datapartitioner och distribuera data jämnt.

### <a name="option-4-use-round-robin-distribution"></a>Alternativ 4: Använd Round-Robin-distribution

Om du inte hittar en lämplig nyckel för partition och distribution kan du försöka använda resursallokering (Round-Robin). Resursallokeringen för resursallokering behandlar alla rader jämnt och placerar dem slumpmässigt i motsvarande buckets. Data blir jämnt distribuerade, men det förlorar information om den lokala informationen, en nackdel som också kan minska jobbets prestanda för vissa åtgärder. Om du gör agg regering för den skevade nyckeln ändå behålls problemet med data skevningen. Mer information om resursallokering finns i avsnittet om distributioner i U-SQL-tabellen i [CREATE TABLE (U-SQL): skapa en tabell med schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Lösning 2: förbättra frågeplan

### <a name="option-1-use-the-create-statistics-statement"></a>Alternativ 1: Använd instruktionen skapa statistik

U-SQL tillhandahåller instruktionen skapa statistik för tabeller. Den här instruktionen ger mer information om frågans optimering om data egenskaper, till exempel värde distribution, som lagras i en tabell. För de flesta frågor genererar fråge optimering redan nödvändig statistik för en plan med hög kvalitet. Ibland kan du behöva förbättra frågans prestanda genom att skapa ytterligare statistik med skapa statistik eller genom att ändra frågans design. Mer information finns på sidan [skapa statistik (U-SQL)](/u-sql/ddl/statistics/create-statistics) .

Kod exempel:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>Statistik information uppdateras inte automatiskt. Om du uppdaterar data i en tabell utan att skapa statistiken igen kan frågans prestanda nekas.

### <a name="option-2-use-skewfactor"></a>Alternativ 2: Använd SKEWFACTOR

Om du vill summera skatten för varje tillstånd måste du använda GROUP BY State, en metod som inte undviker problemet med data skevningen. Du kan dock ange ett data tips i din fråga för att identifiera data skevning i nycklar så att optimeringen kan förbereda en körnings plan åt dig.

Vanligt vis kan du ange parametern som 0,5 och 1, med 0,5 vilket innebär inte mycket snedhet och 1 betyder tung skevning. Eftersom tipset påverkar optimeringen av körnings planer för den aktuella instruktionen och alla underordnade instruktioner, måste du lägga till tipset innan du kan använda den nedrullningsbara nyckel-och sammansättnings metoden.

```usql
SKEWFACTOR (columns) = x
```

Ger ett tips om att de aktuella kolumnerna har en sned faktor x från 0 (ingen skevning) till 1 (mycket tung skev).

Kod exempel:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>Alternativ 3: Använd ROWCOUNT
Förutom SKEWFACTOR, för vissa sneda nyckel kopplingar, om du vet att den andra sammanfogade rad uppsättningen är liten, kan du se optimeringen genom att lägga till ett radantal-tips i U-SQL-instruktionen före koppling. På så sätt kan optimeringen välja en sändnings strategi för att få bättre prestanda. Tänk på att ROWCOUNT inte löser problemet med data snedheten, men det kan ge ytterligare hjälp.

```usql
OPTION(ROWCOUNT = n)
```

Identifiera en liten rad uppsättning före koppling genom att tillhandahålla ett uppskattat heltals antal rader.

Kod exempel:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Lösning 3: förbättra den användardefinierade reduceraren och kombineraren

Ibland kan du skriva en användardefinierad operator för att hantera komplex process logik och en välskriven minsknings process och kombinerare kan minimera ett data skevs problem i vissa fall.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Alternativ 1: Använd en rekursiv minskning, om möjligt

Som standard körs en användardefinierad minskning i icke-rekursivt läge, vilket innebär att det minskar arbetet för en nyckel distribueras till ett enda hörn. Men om dina data är sneda kan de enorma data uppsättningarna bearbetas i ett enda hörn och köras under en längre tid.

Du kan förbättra prestanda genom att lägga till ett attribut i koden för att definiera att dereduceraren ska köras i rekursivt läge. Sedan kan de enorma data uppsättningarna distribueras till flera hörn och köras parallellt, vilket påskyndar ditt jobb.

Om du vill ändra en icke-rekursiv minskning till rekursivt måste du kontrol lera att algoritmen är associativ. Summan är till exempel associativ och median värdet. Du måste också kontrol lera att indata och utdata för reduceraren behåller samma schema.

Attribut för rekursiv minskning:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Kod exempel:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Alternativ 2: Använd kombinations läget på radnivå, om möjligt

På samma sätt som för rad räknings tips för vissa sneda nyckel kopplings fall försöker kombinerat läge distribuera enorma skevade nyckel värden till flera hörn så att arbetet kan köras samtidigt. Det går inte att lösa problem med data skevning i kombinations läge, men det kan ge ytterligare hjälp med enorma skevade nyckel värden.

Som standard är sammanslagnings läget full, vilket innebär att den vänstra rad uppsättningen och den högra rad uppsättningen inte kan skiljas. Att ställa in läget som vänster/höger/inre aktiverar rad nivå koppling. Systemet separerar motsvarande rad uppsättningar och distribuerar dem till flera hörn som körs parallellt. Men innan du konfigurerar kombinations läget måste du vara noga med att se till att motsvarande rad uppsättningar kan separeras.

Exemplet nedan visar en separerad vänster rad uppsättning. Varje utgående rad är beroende av en enda inmatnings rad från vänster, och den kan vara beroende av alla rader från höger med samma nyckel värde. Om du ställer in sammanslagnings läget som Left separerar systemet den enorma vänstra rad uppsättningen till små och tilldelar dem till flera hörn.

![Bild av kombinations läge](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Om du ställer in fel kombinations läge är kombinationen mindre effektiv och resultatet kan vara fel.

Attribut för kombinations läge:

- SqlUserDefinedCombiner (mode = CombinerMode. full): varje utgående rad kan vara beroende av alla inmatnings rader från vänster och höger med samma nyckel värde.

- SqlUserDefinedCombiner (läge = CombinerMode. Left): varje utmatnings rad är beroende av en enda inmatnings rad från vänster (och eventuellt alla rader från höger med samma nyckel värde).

- qlUserDefinedCombiner (läge = CombinerMode. Right): varje utgående rad är beroende av en enda inmatnings rad från höger (och alla rader från vänster med samma nyckel värde).

- SqlUserDefinedCombiner (läge = CombinerMode. Inner): varje utgående rad är beroende av en enda inmatnings rad från vänster och höger med samma värde.

Kod exempel:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
