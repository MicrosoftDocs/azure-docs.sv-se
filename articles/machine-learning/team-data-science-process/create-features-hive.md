---
title: Skapa funktioner för data i ett Azure HDInsight Hadoop kluster – team data science process
description: Exempel på Hive-frågor som genererar funktioner i data som lagras i ett Azure HDInsight Hadoop-kluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020593"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor
Det här dokumentet visar hur du skapar funktioner för data som lagras i ett Azure HDInsight Hadoop kluster med Hive-frågor. Dessa Hive-frågor använder Embedded Hive User-Defined functions (UDF: er), de skript som anges.

De åtgärder som krävs för att skapa funktioner kan vara minnes krävande. Prestanda för Hive-frågor blir mer kritiska i sådana fall och kan förbättras genom justering av vissa parametrar. Justeringen av dessa parametrar beskrivs i det sista avsnittet.

Exempel på frågor som presenteras är särskilt för [NYC taxi-data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenarierna finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Dessa frågor har redan angivet data schema och är redo att skickas till att köras. I det sista avsnittet beskrivs de parametrar som användarna kan justera så att prestandan för Hive-frågor kan förbättras också.

Den här uppgiften är ett steg i [TDSP (Team data science process)](./index.yml).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapat ett Azure Storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Etablerade ett anpassat Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner, se [anpassa Azure HDInsight Hadoop kluster för avancerad analys](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Data har laddats upp till Hive-tabeller i Azure HDInsight Hadoop kluster. Om den inte har det följer du först [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) för att ladda upp data till Hive-tabeller.
* Fjärråtkomst till klustret har Aktiver ATS. Om du behöver instruktioner, se [åtkomst till Head-noden i Hadoop-klustret](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generering av funktioner
I det här avsnittet beskrivs flera exempel på hur funktioner kan genereras med Hive-frågor. När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, som sedan kan kopplas till den ursprungliga tabellen. Följande exempel visas:

1. [Frekvens-baserad funktions generering](#hive-frequencyfeature)
2. [Risker med kategoriska-variabler i binära klassificering](#hive-riskfeature)
3. [Extrahera funktioner från datetime-fältet](#hive-datefeatures)
4. [Extrahera funktioner från textfält](#hive-textfeatures)
5. [Beräkna avståndet mellan GPS-koordinater](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Frekvens-baserad funktions generering
Det är ofta användbart att beräkna frekvensen för nivåerna för en kategoriska-variabel eller frekvensen för vissa kombinationer av nivåer från flera kategoriska-variabler. Användare kan använda följande skript för att beräkna dessa frekvenser:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Risker med kategoriska-variabler i binära klassificering
I binära klassificering måste icke-numeriska kategoriska-variabler konverteras till numeriska funktioner när modeller som används bara tar numeriska funktioner. Den här omvandlingen görs genom att varje icke-numerisk nivå ersätts med en numerisk risk. I det här avsnittet visas några generiska Hive-frågor som beräknar risk värden (log strider) för en kategoriska-variabel.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

I det här exemplet `smooth_param1` är variabler och `smooth_param2` inställda på att utjämna de risk värden som beräknas utifrån data. Riskerna har ett intervall mellan-inf och inf. En risk > 0 anger att målet är lika med 1 är större än 0,5.

När risk tabellen har beräknats kan användarna tilldela risk värden till en tabell genom att koppla den till risk tabellen. Strukturen som ansluter till frågan fanns i föregående avsnitt.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Extrahera funktioner från DATETIME-fält
Hive innehåller en uppsättning UDF: er för bearbetning av DATETIME-fält. I Hive är standard datum-datetime-formatet ' ÅÅÅÅ-MM-DD 00:00:00 ' (' 1970-01-01 12:21:32 ' till exempel). Det här avsnittet innehåller exempel som extraherar dagen i månaden, månaden från ett datum/tid-fält och andra exempel som konverterar en datetime-sträng i ett annat format än standardformat till en datetime-sträng i standardformat.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Den här Hive-frågan förutsätter att *\<datetime field>* är i standard-datetime-formatet.

Om ett datetime-fält inte är i standardformat måste du först konvertera datetime-fältet till UNIX-tidsstämpeln och sedan konvertera UNIX-tidsstämpeln till en datetime-sträng som är i standardformat. När datetime är i standardformat kan användare använda det inbäddade datetime-UDF: er för att extrahera funktioner.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

I den här frågan, om *\<datetime field>* har mönstret som *03/26/2015 12:04:39*, ska vara *\<pattern of the datetime field>* `'MM/dd/yyyy HH:mm:ss'` . Användarna kan testa det genom att köra

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

*Hivesampletable* i den här frågan är förinstallerat på alla Azure HDInsight Hadoop kluster som standard när klustren är etablerade.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Extrahera funktioner från textfält
När Hive-tabellen har ett textfält som innehåller en sträng med ord som är avgränsade med blank steg, extraherar följande fråga längden på strängen och antalet ord i strängen.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Beräkna avstånd mellan uppsättningar av GPS-koordinater
Frågan som anges i det här avsnittet kan tillämpas direkt på NYC taxi-rese data. Syftet med den här frågan är att visa hur du använder en inbäddad matematisk funktion i Hive för att generera funktioner.

De fält som används i den här frågan är GPS-koordinaterna för hämtnings-och DropOff-platser, med namnet *pickup \_ longitud*, *pickup \_ Latitude*, *DropOff \_ longitud* och *DropOff \_ Latitude*. Frågorna som beräknar det direkta avståndet mellan upphämtnings-och DropOff-koordinaterna är:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

De matematiska ekvationer som beräknar avståndet mellan två GPS-koordinater finns på den <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">flyttbara typ skript</a> platsen som skapats av Peter Lapisu. I det här Java Script `toRad()` är funktionen bara *lat_or_lon* PI/180, vilket konverterar grader till radianer. Här *lat_or_lon* är latitud eller longitud. Eftersom Hive inte tillhandahåller funktionen `atan2` , men tillhandahåller funktionen `atan` , `atan2` implementeras funktionen av `atan` funktion i Hive-frågan ovan med hjälp av den definition som anges i <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Skapa arbetsyta](./media/create-features-hive/atan2new.png)

En fullständig lista över inbäddade Hive-UDF: er finns i avsnittet **inbyggda funktioner** på <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Avancerade ämnen: finjustera Hive-parametrar för att förbättra frågans hastighet
Standard parameter inställningarna för Hive-kluster kanske inte lämpar sig för Hive-frågor och data som frågorna behandlar. I det här avsnittet beskrivs vissa parametrar som användarna kan justera för att förbättra prestandan för Hive-frågor. Användare måste lägga till parameter justerings frågorna innan frågor om bearbetning av data.

1. **Java heap-utrymme**: för frågor som rör anslutning av stora data uppsättningar, eller bearbetning av långa poster, är det ett av de vanliga felen att **köra utanför heap-utrymmet** . Det här felet kan undvikas genom att ange parametrarna *MapReduce. map. java.* Välj och *MapReduce. Task. io. sort. MB* till önskade värden. Här är ett exempel:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Med den här parametern allokeras 4 GB minne till ett Java-heap-utrymme och du kan också sortera mer effektivt genom att allokera mer minne för den. Det är en bra idé att spela med de här allokeringarna om det uppstår fel som rör heap-utrymmet.

1. **Storlek på DFS-block**: den här parametern anger den minsta data enheten som fil systemet lagrar. Om till exempel storleken på DFS-blocket är 128 MB lagras alla data som är mindre än och upp till 128 MB i ett enda block. Data som är större än 128 MB tilldelas extra block. 
2. Om du väljer en liten block storlek får du stora omkostnader i Hadoop eftersom noden namn måste bearbeta många fler förfrågningar för att hitta det relevanta block som hör till filen. En rekommenderad inställning vid hantering av gigabyte (eller större) data:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Optimering av kopplings åtgärd i Hive**: vid kopplings åtgärder i kart-och minsknings ramverket sker vanligt vis i minsknings fasen, men enorma vinster kan uppnås genom att schemalägga kopplingar i kart fasen (kallas även "mapjoins"). Ange det här alternativet:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Ange antalet mappningar till Hive**: medan Hadoop tillåter att användaren anger antalet avreducerare, anges inte antalet mappningar av användaren. Ett stick som tillåter viss kontroll av det här antalet är att välja Hadoop-variablerna *mapred. min. Split. size* och *mapred. max. Split. storlek* eftersom storleken på varje kart uppgift bestäms av:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Normalt är standardvärdet för:
    
   - *mapred. min. Split. storlek* är 0, det vill säga
   - *mapred. max. Split. storlek* är **långt. Max** och för 
   - *DFS. block. storlek* är 64 MB.

     Som vi kan se, med tanke på data storleken, kan du justera de här parametrarna genom att "ställa in" dem så att vi kan justera antalet mappningar som används.

4. Här följer några fler **Avancerade alternativ** för att optimera Hive-prestanda. Med de här alternativen kan du ange hur mycket minne som allokeras för att mappa och minska aktiviteter, och det kan vara användbart när du vill ändra prestanda. Tänk på att *MapReduce. reduce. Memory. MB* får inte vara större än storleken på det fysiska minnet för varje arbetsnod i Hadoop-klustret.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```