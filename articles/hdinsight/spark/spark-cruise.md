---
title: Använd SparkCruise på Azure HDInsight för att påskynda Apache Spark-frågor
description: Lär dig hur du använder SparkCruise optimerings plattform för att förbättra effektiviteten i Apache Spark frågor.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f26addda79d57a055f7b431968319138d499ef18
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272945"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise på Azure HDInsight

I det här dokumentet beskrivs funktionen *SparkCruise*i Azure HDInsight, som automatiskt återanvänder Apache Spark beräkningar för att öka frågans effektivitet.

## <a name="overview"></a>Översikt

Frågorna som du kör på en analys plattform, till exempel Apache Spark, delas upp i en frågeplan som innehåller mindre under frågor. Dessa under frågor kan visas flera gånger i fråge planerna för flera frågor. Varje gång de inträffar körs de igen för att returnera resultaten. Att köra samma fråga igen kan dock vara ineffektivt och leda till onödiga beräknings kostnader.

*SparkCruise* är en plattform för arbets belastnings optimering som kan återanvända vanliga beräkningar, vilket minskar den övergripande tiden för körning och kostnader för data överföring. Plattformen använder begreppet *materialiserad vy*, som är en fråga vars resultat lagras i förberäknad form. Dessa resultat kan sedan återanvändas när frågan visas igen senare, i stället för att återanvända resultatet igen.

## <a name="setup-and-installation"></a>Konfiguration och installation

SparkCruise är tillgängligt på alla HDInsight 4,0-kluster med Spark 2,3 eller 2,4. SparkCruise installeras i `/opt/peregrine/` katalogen i HDInsight-klustret. *SparkCruise* kräver följande konfigurations egenskaper som är inställda som standard för att fungera korrekt.

* `spark.sql.queryExecutionListeners` är inställt på `com.microsoft.peregrine.spark.listeners.PlanLogListener` , vilket möjliggör loggning av fråge planer.
* `spark.sql.extensions` är inställt på `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , vilket möjliggör optimerings regler för online-materialization och åter användning.

## <a name="computation-reuse-in-spark-sql"></a>Återanvänd beräkning i Spark SQL

Följande exempel scenario visar hur du använder *SparkCruise* för att optimera Apache Spark frågor. 

1. SSH till Head-noden i Spark-klustret.
1. Skriv `spark-shell`.
1. Kör följande kodfragment, som kör några få grundläggande frågor som använder exempel data i klustret.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Använd *SparkCruise* Query Analysis-verktyget för att analysera fråge planerna för föregående frågor, som lagras i Spark-programloggarna. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Visa resultatet av analys processen, som är en feedback-fil. Den här feedback-filen innehåller anteckningar för framtida Spark SQL-frågor. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze`Kommandot tolkar fråge planerna och skapar en tabell representation av arbets belastningen. Sedan `views` identifierar kommandot vanliga under Plans uttryck och väljer ett intressant under Plans uttryck för framtida materialization och åter användning. Utdata är en feedback-fil som innehåller anteckningar för framtida Spark SQL-frågor. 

`show`Kommandot visar utdata som liknar följande text:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Feedback-filen innehåller poster i följande format: `subplan-identifier [Materialize|Reuse] input/path/to/action` . I det här exemplet finns det två unika signaturer: en som representerar de två första upprepade frågorna och den andra som representerar filtervärdet i de senaste två frågorna. Med den här feedback-filen kommer följande frågor när de skickas igen automatiskt att materialisera och återanvända vanliga under planer. 

Testa Optimeringarna genom att köra en annan uppsättning exempel frågor.

1. Skriv `spark-shell`.
1. Kör följande kodfragment

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Visa feedback-filen igen för att se signaturerna för de frågor som har återanvänts.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show`Kommandot ger utdata som liknar följande text:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Även om literal-värdet i frågan har ändrats från `'11%'` till `'12%'` kan *SparkCruise* fortfarande matcha tidigare frågor till nya frågor med små variationer som utveckling av litterala värden och data uppsättnings versioner. Om det finns större ändringar i en fråga kan du köra analys verktyget igen för att identifiera nya frågor som kan återanvändas.

*SparkCruise* utlöser en under fråga i bakgrunden för att materialisera den valda under planen från den första frågan som innehåller den. Senare frågor kan läsa de materialiserade under planerna direkt i stället för att göra om dem. I den här arbets belastningen materialiseras under planerna på ett online sätt av de första och tredje frågorna. Vi kan se plan ändringen av frågor när de gemensamma under planerna har materialiserats.

Nu kan du se att det nu finns fyra nya materialiserade under uttryck som kan återanvändas i efterföljande frågor.

## <a name="clean-up"></a>Rensa

Feedback-filerna, materialiserade under planer och fråge loggar behålls i Spark-sessioner. Om du vill ta bort de här filerna kör du följande kommando:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Efterföljande moment

* [Förbättra prestanda för Apache Spark arbets belastningar med Azure HDInsight IO-cache](apache-spark-improve-performance-iocache.md)
* [Optimera Apache Spark jobb i HDInsight](./apache-spark-perf.md)
* [SparkCruise: åter användning av handsfree-beräkning i Spark](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Apache Spark rikt linjer på Azure HDInsight](./spark-best-practices.md)
