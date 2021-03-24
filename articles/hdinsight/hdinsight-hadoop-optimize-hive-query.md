---
title: Optimera Hive-frågor i Azure HDInsight
description: Den här artikeln beskriver hur du optimerar dina Apache Hive frågor i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: 551d985ea78e83397e507676c5fd7ecfce12ff7b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864252"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimera Apache Hive-frågor i Azure HDInsight

I den här artikeln beskrivs några av de vanligaste prestanda Optimeringarna som du kan använda för att förbättra prestandan hos dina Apache Hive-frågor.

## <a name="cluster-type-selection"></a>Val av kluster typ

I Azure HDInsight kan du köra Apache Hive frågor på några olika kluster typer. 

Välj lämplig kluster typ för att optimera prestanda för dina arbets belastnings behov:

* Välj kluster typen **interaktiv fråga** för att optimera för `ad hoc` interaktiva frågor. 
* Välj Apache **Hadoop** kluster typ för att optimera för Hive-frågor som används som en batch-process. 
* **Spark** -och **HBase** -kluster typer kan också köra Hive-frågor och kan vara lämpliga om du kör arbets belastningarna. 

Mer information om att köra Hive-frågor på olika typer av HDInsight-kluster finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hadoop/hdinsight-use-hive.md).

## <a name="scale-out-worker-nodes"></a>Skala ut arbetsnoder

Om du ökar antalet arbetsnoder i ett HDInsight-kluster kan arbetet använda fler mappningar och reducerare som ska köras parallellt. Det finns två sätt att öka utskalning i HDInsight:

* När du skapar ett kluster kan du ange antalet arbetsnoder med hjälp av Azure Portal, Azure PowerShell eller kommando rads gränssnittet.  Mer information finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Följande skärm bild visar konfigurationen för arbetsnoden på Azure Portal:
  
    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png" alt-text="Noder i Azure Portal kluster storlek":::

* När du har skapat kan du också redigera antalet arbetsnoder för att skala ut ett kluster ytterligare utan att skapa ett nytt:

    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png " alt-text="Azure Portal skalnings kluster storlek":::

Mer information om hur du skalar HDInsight finns i [skala HDInsight-kluster](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Använd Apache Tez i stället för kart minskning

[Apache Tez](https://tez.apache.org/) är en alternativ körnings motor till MapReduce-motorn. Linux-baserade HDInsight-kluster har Tez aktiverat som standard.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png" alt-text="HDInsight Apache Tez-översikt diagram":::

Tez är snabbare eftersom:

* **Kör riktat acykliska diagram (dag) som ett enda jobb i MapReduce-motorn**. DAG kräver att varje uppsättning med mappningar ska följas av en uppsättning med reducerare. Detta krav gör att flera MapReduce-jobb kan läsas av för varje Hive-fråga. Tez har inte någon sådan begränsning och kan bearbeta komplexa DAG som ett jobb som minimerar jobbets start kostnader.
* **Undviker onödiga skrivningar**. Flera jobb används för att bearbeta samma Hive-fråga i MapReduce-motorn. Utdata från varje MapReduce-jobb skrivs till HDFS för mellanliggande data. Eftersom Tez minimerar antalet jobb för varje Hive-fråga, kan det undvika onödiga skrivningar.
* **Minimerar start fördröjningar**. Tez kan förbättra start fördröjningen genom att minska antalet mappningar som krävs för att starta och även förbättra optimeringen i hela.
* **Återåteranvänd behållare**. När möjligt kan Tez återanvända behållare för att se till att svars tiden för att starta behållare minskas.
* **Metoder för kontinuerlig optimering**. Traditionellt gjorda optimering skedde under kompilerings fasen. Det finns dock mer information om indata som möjliggör bättre optimering under körning. Tez använder kontinuerlig optimerings teknik som gör det möjligt att optimera planen ytterligare i körnings fasen.

Mer information om dessa begrepp finns i [Apache TEZ](https://tez.apache.org/).

Du kan göra eventuella Hive-Tez aktiverade genom att aktivera frågan med följande set-kommando:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive-partitionering

I/O-åtgärder är den stora prestanda Flask halsen för att köra Hive-frågor. Prestanda kan förbättras om mängden data som behöver läsas kan minskas. Hive-frågor söker som standard igenom hela Hive-tabeller. Men för frågor som bara behöver söka igenom en liten mängd data (till exempel frågor med filtrering), skapar det här beteendet onödig omkostnader. Hive-partitionering gör det möjligt för Hive-frågor att endast komma åt den nödvändiga mängden data i Hive-tabeller.

Hive-partitionering implementeras genom att organisera om rå data i nya kataloger. Varje partition har en egen fil katalog. Partitioneringen definieras av användaren. Följande diagram illustrerar partitionering av en Hive-tabell efter kolumn *året*. En ny katalog skapas för varje år.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png" alt-text="HDInsight Apache Hive partitionering":::

Några partitionering överväganden:

* **Under partitionerings** partitionering i kolumner med bara några få värden kan det uppstå några partitioner. Till exempel skapar partitionering på kön bara två partitioner som ska skapas (hane och hona), så minska svars tiden med högst hälften.
* **Inte över partition** – på de andra extrema, skapar en partition i en kolumn med ett unikt värde (till exempel UserID) flera partitioner. Över-partitionen orsakar mycket stress på klustrets namenode eftersom det måste hantera det stora antalet kataloger.
* **Undvik data skevning** – Välj partitionerings nyckeln på ett bra sätt så att alla partitioner är jämnt utformade. Till exempel kan partitionering i kolumnen *State* snedställa data fördelningen. Eftersom status för California har en population som är nästan 30 i Vermont, är partitionsstorleken potentiellt skevad och prestandan kan variera med stor mängd.

Om du vill skapa en partitionstabell använder du den *partitionerade by* -satsen:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

När den partitionerade tabellen har skapats kan du antingen skapa statisk partitionering eller dynamisk partitionering.

* **Statisk partitionering** innebär att du redan har shardade data i lämpliga kataloger. Med statiska partitioner lägger du till Hive-partitioner manuellt utifrån katalogens plats. Följande kodfragment är ett exempel.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamisk partitionering** innebär att du vill att Hive ska skapa partitioner automatiskt åt dig. Eftersom du redan har skapat partitionerings tabellen från mellanlagrings tabellen behöver du bara infoga data i den partitionerade tabellen:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Mer information finns i [partitionerade tabeller](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Använd ORCFile-formatet

Hive stöder olika fil format. Exempel:

* **Text**: standard fil formatet och fungerar med de flesta scenarier.
* **Avro**: fungerar bra för samverkans scenarier.
* **Orc/Parquet**: passar bäst för prestanda.

ORC-formatet (optimerad rad kolumn) är ett mycket effektivt sätt att lagra Hive-data på. Jämfört med andra format har ORC följande fördelar:

* stöd för komplexa typer inklusive DateTime och komplexa och halv strukturerade typer.
* upp till 70% komprimering.
* indexerar varje 10 000 rader, vilket tillåter att rader hoppas över.
* en betydande minskning av körningen av körnings tid.

Om du vill aktivera ORC-formatet skapar du först en tabell med satsen *lagrad som Orc*:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Sedan infogar du data i tabellen ORC från mellanlagrings tabellen. Exempel:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Du kan läsa mer om ORC-formatet i [språk hand boken för Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization tillåter att Hive bearbetar en batch med 1024 rader i stället för att bearbeta en rad i taget. Det innebär att enkla åtgärder utförs snabbare eftersom mindre intern kod måste köras.

Så här aktiverar du vectorization-prefix för Hive-frågan med följande inställning:

```hive
set hive.vectorized.execution.enabled = true;
```

Mer information finns i [Vector Query Execution](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andra optimerings metoder

Det finns fler optimerings metoder som du kan överväga, till exempel:

* **Hive-Bucket:** en teknik som gör det möjligt att klustra eller segmentera stora data uppsättningar för att optimera prestanda för frågor.
* **Delta i optimering:** optimering av Hive-planering för att förbättra effektiviteten i kopplingar och minska behovet av användar tips. Mer information finns i [delta optimering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Öka minskaren**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera vanliga optimerings metoder för Hive-frågor. Mer information finns i följande artiklar:

* [Optimera Apache Hive](./optimize-hive-ambari.md)
* [Analysera flyg fördröjnings data med hjälp av interaktiv fråga i HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analysera Twitter-data med Apache Hive i HDInsight](hdinsight-analyze-twitter-data-linux.md)
