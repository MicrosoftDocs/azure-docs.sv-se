---
title: Köra Apache Hadoop MapReduce-exempel i HDInsight – Azure
description: Kom igång med MapReduce-exempel i JAR-filer som ingår i HDInsight. Använd SSH för att ansluta till klustret och Använd sedan Hadoop-kommandot för att köra exempel jobb.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943213"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Kör MapReduce-exemplen som ingår i HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Lär dig hur du kör MapReduce-exemplen som ingår i Apache Hadoop på HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce-exemplen

Exemplen finns i HDInsight-klustret på `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . Käll koden för de här exemplen ingår i HDInsight-klustret på `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

Följande exempel finns i det här arkivet:

|Exempel |Beskrivning |
|---|---|
|aggregatewordcount|Räknar orden i indatafilerna.|
|aggregatewordhist|Beräknar histogrammet för orden i indatafilerna.|
|bbp|Använder Bailey-Borwein-Plouffe för att beräkna exakta siffror från PI.|
|dbcount|Räknar de sid visningar-loggar som lagras i en databas.|
|distbbp|Använder en BBP-formel för att beräkna exakta bitar av PI.|
|grep|Räknar antalet matchningar av ett regex i indatamängden.|
|join|Utför en koppling över sorterade, jämnt partitionerade data uppsättningar.|
|multifilewc|Räknar ord från flera filer.|
|pentomino|Panel som placerar program för att hitta lösningar på Pentomino problem.|
|PI|Uppskattar PI med en Carlo-metod från en-Monte.|
|randomtextwriter|Skriver 10 GB slumpmässiga text data per nod.|
|randomwriter|Skriver 10 GB slumpmässiga data per nod.|
|secondarysort|Definierar en sekundär sortering för minsknings fasen.|
|sortera|Sorterar data som skrivits av den slumpmässiga skrivaren.|
|sudoku|En Sudoku-problemlösare.|
|teragen|Generera data för terasort.|
|terasort|Kör terasort.|
|teravalidate|Kontrollerar resultatet av terasort.|
|WORDCOUNT|Räknar orden i indatafilerna.|
|wordmean|Räknar den genomsnittliga längden för orden i indatafilerna.|
|wordmedian|Räknar längden på median längden för orden i indatafilerna.|
|wordstandarddeviation|Räknar standard avvikelsen för längden på orden i indatafilerna.|

## <a name="run-the-wordcount-example"></a>Kör WORDCOUNT-exemplet

1. Anslut till HDInsight med SSH. Ersätt `CLUSTER` med namnet på klustret och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Från SSH-sessionen använder du följande kommando för att lista exemplen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Det här kommandot genererar en lista över exempel från föregående avsnitt i det här dokumentet.

3. Använd följande kommando för att få hjälp med ett bestämt exempel. I det här fallet **WORDCOUNT** -exemplet:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Följande meddelande visas:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Det här meddelandet anger att du kan tillhandahålla flera sökvägar för käll dokumenten. Den sista sökvägen är den plats där utdata (antal ord i käll dokumenten) lagras.

4. Använd följande för att räkna alla ord i antecknings böckerna i Leonardo da Vinci, som tillhandahålls som exempel data med klustret:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    InInformationen för det här jobbet läses från `/example/data/gutenberg/davinci.txt` . Utdata för det här exemplet lagras i `/example/data/davinciwordcount` . Båda Sök vägarna finns i standard lagrings utrymmet för klustret, inte det lokala fil systemet.

   > [!NOTE]  
   > Som anges i hjälpen för WORDCOUNT-exemplet kan du också ange flera indatafiler. Till exempel `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` räknas ord i både davinci.txt och ulysses.txt.

5. När jobbet har slutförts använder du följande kommando för att visa utdata:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Det här kommandot sammanfogar alla utdatafiler som skapas av jobbet. Den visar utdata till-konsolen. De utdata som genereras liknar följande text:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Varje rad representerar ett ord och hur många gånger det inträffade i indata.

## <a name="the-sudoku-example"></a>Sudoku-exemplet

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) är ett logiskt pussel som består av nio 3x3-rutnät. Vissa celler i rutnätet har siffror, medan andra är tomma och målet är att lösa för de tomma cellerna. Föregående länk har mer information om pusslet, men syftet med det här exemplet är att lösa de tomma cellerna. Vårt indata bör vara en fil med följande format:

* Nio rader med nio kolumner
* Varje kolumn kan innehålla antingen ett tal eller `?` (som indikerar en tom cell)
* Cellerna avgränsas med ett blank steg

Det finns ett visst sätt att konstruera Sudoku puzzles; Det går inte att upprepa ett tal i en kolumn eller rad. Det finns ett exempel på det HDInsight-kluster som är korrekt konstruerat. Den finns i `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` och innehåller följande text:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Om du vill köra det här exempel problemet genom Sudoku-exemplet använder du följande kommando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Resultatet ser ut ungefär som i följande text:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>PI-exempel (π)

I PI-exemplet används en statistisk (kvasibolag Carlo)-metod för att beräkna värdet för PI. Punkter placeras slumpmässigt i en enhets ruta. Fyr kanten innehåller även en cirkel. Sannolikheten för att punkterna faller inom cirkeln är lika med cirkelns yta, PI/4. Värdet för PI kan beräknas från värdet för 4R. R är förhållandet mellan antalet punkter i cirkeln och det totala antalet punkter som ligger inom fyr kanten. Ju större exemplet på de använda poängen, desto bättre blir beräkningen.

Använd följande kommando för att köra det här exemplet. Det här kommandot använder 16 Maps med 10 000 000 exempel för att uppskatta värdet för Pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Värdet som returneras av det här kommandot liknar **3.14159155000000000000**. För referenser är de första 10 decimalerna av PI 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort-exempel

GraySort är en benchmark-sortering. Måttet är den sorterings frekvens (TB/minut) som uppnås när stora mängder data sorteras, vanligt vis en 100 TB-lägsta.

I det här exemplet används 10 GB data så att det kan köras relativt snabbt. Den använder MapReduce-program som utvecklats av Owen O'Malley och Arun Murthy. Dessa program vann den årliga generella sorteringen ("Daytona") terabyte-sortering i 2009, med en hastighet på 0,578 TB/min (100 TB i 173 minuter). Mer information om detta och andra sorterings kriterier finns på webbplatsen för att [Sortera benchmark](https://sortbenchmark.org/) .

I det här exemplet används tre uppsättningar MapReduce-program:

* **TeraGen**: ett MapReduce-program som genererar rader med data att sortera

* **TeraSort**: exempel på indata och använder MapReduce för att sortera data i en total ordning

    TeraSort är en standard sortering med MapReduce, förutom en anpassad partitioner. Partitioneraren använder en sorterad lista över N-1 exempel nycklar som definierar nyckel intervallet för varje minskning. I synnerhet skickas alla nycklar, till exempel [i-1] <= nyckel < exemplet [i], för att minska i. Den här partitionerings tjänsten garanterar att utdata från minska i är mindre än resultatet av minska i + 1.

* **TeraValidate**: ett MapReduce-program som kontrollerar att utdata är globalt sorterade

    Den skapar en karta per fil i utdatakatalogen, och varje karta ser till att varje nyckel är mindre än eller lika med den föregående. Funktionen Map genererar poster för de första och sista nycklarna i varje fil. Funktionen reducera ser till att den första nyckeln i filen i är större än den sista nyckeln i filen i-1. Eventuella problem rapporteras som utdata från minsknings fasen, med de nycklar som är i ordning.

Använd följande steg för att generera data, sortera och sedan verifiera utdata:

1. Generera 10 GB data som lagras i HDInsight-klustrets standard lagring på `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks`Talar om Hadoop hur många kart uppgifter som ska användas för det här jobbet. De två sista parametrarna instruerar jobbet att skapa 10 GB data och lagra det på `/example/data/10GB-sort-input` .

2. Använd följande kommando för att sortera data:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks`Talar om Hadoop hur många minska uppgifter som ska användas för jobbet. De två sista parametrarna är bara platser för indata och utdata för data.

3. Använd följande för att validera data som genereras av sorteringen:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kör exemplen som ingår i Linux-baserade HDInsight-kluster. Självstudier om hur du använder gris, Hive och MapReduce med HDInsight finns i följande avsnitt:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
