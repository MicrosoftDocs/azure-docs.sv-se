---
title: Optimera data lagring för Apache Spark – Azure HDInsight
description: Lär dig hur du optimerar data lagring för användning med Apache Spark på Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926980"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Data lagrings optimering för Apache Spark

I den här artikeln beskrivs strategier för att optimera data lagring för effektiv Apache Spark jobb körning på Azure HDInsight.

## <a name="overview"></a>Översikt

Spark stöder många format, till exempel CSV, JSON, XML, Parquet, Orc och Avro. Spark kan utökas för att ge stöd för många fler format med externa data källor – mer information finns i [Apache Spark-paket](https://spark-packages.org).

Det bästa formatet för prestanda är Parquet med *Fästnings komprimering*, vilket är standard i Spark 2. x. Parquet lagrar data i kolumn format och optimeras mycket i Spark.

## <a name="choose-data-abstraction"></a>Välj data abstraktion

Tidigare Spark-versioner använder RDD till abstrakta data, Spark 1,3 och 1,6 införde DataFrames och data uppsättningar. Tänk på följande relativa fördelar:

* **DataFrames**
    * Bästa valet i de flesta situationer.
    * Tillhandahåller optimering av frågor via katalysator.
    * Generering av hel stegs kod.
    * Direkt minnes åtkomst.
    * Låg skräp insamlings kostnad (GC).
    * Inte som utvecklare-läsvänlig som data uppsättningar, eftersom det inte finns några kompilerings kontroller eller programmering av domän objekt.
* **Data uppsättningar**
    * Bra i komplexa ETL-pipelines där prestanda påverkan är acceptabel.
    * Inte lämpligt i agg regeringar där prestanda påverkan kan vara avsevärd.
    * Tillhandahåller optimering av frågor via katalysator.
    * Utvecklare – läsvänlig genom att tillhandahålla programmering av domän objekt och kompilerings tid.
    * Lägger till omkostnader för serialisering/deserialisering.
    * Hög global kostnad.
    * Delar upp kod generation i hela fasen.
* **RDD:er**
    * Du behöver inte använda RDD, om du inte behöver bygga en ny anpassad RDD.
    * Ingen fråga optimering via katalysator.
    * Ingen hel stegs kod genereras.
    * Hög global kostnad.
    * Måste använda Spark 1. x äldre API: er.

## <a name="select-default-storage"></a>Välj standard lagring

När du skapar ett nytt Spark-kluster kan du välja Azure Blob Storage eller Azure Data Lake Storage som kluster standard lagring. Båda alternativen ger dig fördelen med långsiktig lagring för tillfälliga kluster. Så dina data tas inte bort automatiskt när du tar bort klustret. Du kan återskapa ett tillfälligt kluster och fortfarande komma åt dina data.

| Butikstyp | Filsystem | Hastighet | Tillfälliga | Användningsfall |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Ja | Tillfälligt kluster |
| Azure Blob Storage (säker) | **wasbs:**//URL/ | **Standard** | Ja | Tillfälligt kluster |
| Azure Data Lake Storage Gen 2| **ABFS:**//URL/ | **Tid** | Ja | Tillfälligt kluster |
| Azure Data Lake Storage Gen 1| **ADL:**//URL/ | **Tid** | Ja | Tillfälligt kluster |
| Lokal HDFS | **HDFS:**//URL/ | **Snabbaste** | Inga | Interaktivt 24/7-kluster |

En fullständig beskrivning av lagrings alternativ finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Använd cachen

Spark tillhandahåller egna inbyggda funktioner för cachelagring som kan användas på olika sätt, till exempel `.persist()` , `.cache()` och `CACHE TABLE` . Denna inbyggda cachelagring är effektiv med små data uppsättningar och i ETL-pipelines där du behöver cachelagra mellanliggande resultat. Spark-intern cachelagring fungerar dock för närvarande inte bra med partitionering, eftersom en cachelagrad tabell inte behåller partitionerings data. En mer generisk och tillförlitlig caching-teknik är *cachelagring av lagrings lager*.

* Native Spark-cachelagring (rekommenderas inte)
    * Passar små data uppsättningar.
    * Fungerar inte med partitionering, vilket kan ändras i framtida Spark-versioner.

* Cachelagring på lagrings nivå (rekommenderas)
    * Kan implementeras i HDInsight med hjälp av funktionen för [IO-cache](apache-spark-improve-performance-iocache.md) .
    * Använder minnes-och SSD-cachelagring.

* Lokal HDFS (rekommenderas)
    * `hdfs://mycluster` sökväg.
    * Använder SSD-cachelagring.
    * Cachelagrade data går förlorade när du tar bort klustret, vilket kräver en återuppbyggnad av cache.

## <a name="optimize-data-serialization"></a>Optimera dataserialiseringen

Spark-jobb distribueras, så lämplig data serialisering är viktig för bästa möjliga prestanda.  Det finns två alternativ för serialisering för Spark:

* Java-serialisering är standard.
* `Kryo` serialisering är ett nyare format och kan resultera i en snabbare och mer kompakt serialisering än Java.  `Kryo` kräver att du registrerar klasserna i ditt program och ännu inte har stöd för alla serialiserbara typer.

## <a name="use-bucketing"></a>Använd bucket

Bucket liknar data partitionering. Men varje Bucket kan innehålla en uppsättning kolumn värden i stället för bara en. Den här metoden fungerar bra för att partitionera med stor (i miljon tals eller fler) värden, t. ex. produkt identifierare. En Bucket bestäms genom hashing av radens Bucket-nyckel. Bucked-tabeller ger unika optimeringar eftersom de lagrar metadata om hur de är i Bucket och sorterade.

Vissa avancerade Bucket-funktioner är:

* Fråga optimering baserat på Bucket meta-information.
* Optimerade agg regeringar.
* Optimerade kopplingar.

Du kan använda partitionering och Bucket på samma tid.

## <a name="next-steps"></a>Nästa steg

* [Optimera data bearbetning för Apache Spark](optimize-cluster-configuration.md)
* [Optimera minnes användning för Apache Spark](optimize-memory-usage.md)
* [Optimera kluster konfigurationen för Apache Spark](optimize-cluster-configuration.md)
