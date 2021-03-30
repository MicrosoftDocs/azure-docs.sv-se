---
title: Optimera Spark-jobb för prestanda
description: Den här artikeln innehåller en introduktion till Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450933"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Optimera Apache Spark jobb i Azure Synapse Analytics

Lär dig hur du optimerar en [Apache Spark](https://spark.apache.org/) kluster konfiguration för din specifika arbets belastning.  Den vanligaste utmaningen är minnesbelastning på grund av felaktiga konfigurationer (särskilt utförare i fel storlek), långvariga åtgärder och uppgifter som resulterar i kartesiska operationer. Du kan påskynda jobben med lämplig cachelagring och genom att tillåta [data skevning](#optimize-joins-and-shuffles). För bästa prestanda kan du övervaka och granska långvarig körning av Spark-jobb och köra resurs krävande jobb.

I följande avsnitt beskrivs vanliga Spark-jobb optimeringar och rekommendationer.

## <a name="choose-the-data-abstraction"></a>Välj data abstraktion

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

## <a name="use-optimal-data-format"></a>Använd optimalt dataformat

Spark stöder många format, till exempel CSV, JSON, XML, Parquet, Orc och Avro. Spark kan utökas för att ge stöd för många fler format med externa data källor – mer information finns i [Apache Spark-paket](https://spark-packages.org).

Det bästa formatet för prestanda är Parquet med *Fästnings komprimering*, vilket är standard i Spark 2. x. Parquet lagrar data i kolumn format och optimeras mycket i Spark. Även om *överfästnings komprimering* kan resultera i större filer än att säga gzip-komprimering. På grund av den fildelnings bara typen av filerna kommer de att avkomprimeras snabbare.

## <a name="use-the-cache"></a>Använd cachen

Spark tillhandahåller egna inbyggda funktioner för cachelagring som kan användas på olika sätt, till exempel `.persist()` , `.cache()` och `CACHE TABLE` . Denna inbyggda cachelagring är effektiv med små data uppsättningar samt i ETL-pipelines där du behöver cachelagra mellanliggande resultat. Spark-intern cachelagring fungerar dock för närvarande inte bra med partitionering, eftersom en cachelagrad tabell inte behåller partitionerings data.

## <a name="use-memory-efficiently"></a>Använd minnet effektivt

Spark arbetar genom att placera data i minnet, så att hantering av minnes resurser är en viktig aspekt i att optimera körningen av Spark-jobb.  Det finns flera tekniker som du kan använda för att använda klustrets minne på ett effektivt sätt.

* Föredra mindre datapartitioner och konto för data storlek, typer och distribution i din partitionerings strategi.
* Överväg att använda den nyare, mer effektiva [kryo Dataserialisering](https://github.com/EsotericSoftware/kryo)i stället för standard-Java-serialisering.
* Övervaka och justera konfigurations inställningar för Spark.

För din referens visas Spark-minneskortet och vissa viktiga utförar-minnes parametrar i nästa bild.

### <a name="spark-memory-considerations"></a>Överväganden vid Spark-minne

Apache Spark i Azure Synapse använder garn [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), garn styr den maximala mängd minne som används av alla behållare på varje spark-nod.  Följande diagram visar de viktigaste objekten och deras relationer.

![GARN Spark minnes hantering](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Prova följande om du vill ta bort meddelanden om slut på minne:

* Läs om DAG hantering, blandade. Minska genom att minska från kopplings sidan, bucketiseras (eller) käll data, maximera enskilda blandade blandade och minska mängden data som skickas.
* Föredra `ReduceByKey` med den fasta minnes gränsen till `GroupByKey` , som tillhandahåller agg regeringar, fönster och andra funktioner, men har en obegränsad minnes gräns.
* Föredra `TreeReduce` , som fungerar mer i körnings-eller partitionerna, till `Reduce` , som gör allt arbete på driv rutinen.
* Utnyttja DataFrames i stället för RDD-objekt på lägre nivå.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel "Top N", olika agg regeringar eller fönster åtgärder.

## <a name="optimize-data-serialization"></a>Optimera dataserialiseringen

Spark-jobb distribueras, så lämplig data serialisering är viktig för bästa möjliga prestanda.  Det finns två alternativ för serialisering för Spark:

* Java-serialisering är standard.
* Kryo-serialisering är ett nyare format och kan resultera i snabbare och mer kompakt serialisering än Java.  Kryo kräver att du registrerar klasserna i ditt program och ännu inte har stöd för alla serialiserbara typer.

## <a name="use-bucketing"></a>Använd bucket

Bucket liknar data partitionering, men varje Bucket kan innehålla en uppsättning kolumn värden i stället för bara en. Bucket fungerar bra för partitionering på stora (i miljon tals eller fler) värden, t. ex. produkt identifierare. En Bucket bestäms genom hashing av radens Bucket-nyckel. Bucked-tabeller ger unika optimeringar eftersom de lagrar metadata om hur de är i Bucket och sorterade.

Vissa avancerade Bucket-funktioner är:

* Fråga optimering baserat på Bucket meta-information.
* Optimerade agg regeringar.
* Optimerade kopplingar.

Du kan använda partitionering och Bucket på samma tid.

## <a name="optimize-joins-and-shuffles"></a>Optimera kopplingar och blandningar

Om du har långsamma jobb för en koppling eller blanda är orsaken förmodligen *dataskevning*, som är asymmetry i dina jobb data. Till exempel kan ett kart jobb ta 20 sekunder, men att köra ett jobb där data är anslutna eller blandade tar timmar. Om du vill åtgärda data skevningen bör du salta hela nyckeln eller använda ett *isolerat salt* för vissa nycklar. Om du använder ett isolerat salt bör du ytterligare filtrera för att isolera din delmängd av saltade nycklar i kart kopplingar. Ett annat alternativ är att introducera en Bucket-kolumn och församlad i Bucket först.

En annan faktor som orsakar långsamma kopplingar kan vara kopplings typen. Som standard använder Spark `SortMerge` typen Join. Den här typen av anslutning lämpar sig bäst för stora data mängder, men är i övrigt kostsam eftersom det måste först sortera vänster och höger om data innan de sammanfogas.

En `Broadcast` koppling passar bäst för mindre data uppsättningar eller där en sida av kopplingen är mycket mindre än den andra sidan. Den här typen av anslutning sänder ut en sida till alla körningar och kräver därför mer minne för sändningar i allmänhet.

Du kan ändra kopplings typen i konfigurationen genom att ange `spark.sql.autoBroadcastJoinThreshold` eller så kan du ange ett JOIN-tips med DataFrame-API: erna ( `dataframe.join(broadcast(df2))` ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Om du använder Bucket tabeller har du en tredje kopplings typ, `Merge` kopplingen. En korrekt fördelad och försorterad data uppsättning hoppar över den dyra sorterings fasen från en `SortMerge` koppling.

Ordningen på kopplingar, särskilt i mer komplexa frågor. Börja med de mest selektiva kopplingarna. Du kan också flytta kopplingar som ökar antalet rader efter AGG regeringar när det är möjligt.

Om du vill hantera parallellitet för kartesiska-kopplingar kan du lägga till kapslade strukturer, fönster och kanske hoppa över ett eller flera steg i ditt Spark-jobb.

### <a name="select-the-correct-executor-size"></a>Välj rätt utförar-storlek

När du bestämmer din utförar-konfiguration bör du tänka på hur du ska använda ^ skräp insamlingen (GC).

* Faktorer för att minska utförar storlek:
  * Minska heap-storleken under 32 GB för att behålla GC-omkostnader < 10%.
  * Minska antalet kärnor för att behålla GC-omkostnader < 10%.

* Faktorer för att öka utförar storlek:
  * Minska kommunikations kostnader mellan körningar.
  * Minska antalet öppna anslutningar mellan körningar (N2) i större kluster (>100-körningar).
  * Öka heap-storleken så att den passar för minnes intensiva uppgifter.
  * Valfritt: minska minnes omkostnader per utförar.
  * Valfritt: öka användning och samtidighet med oversubscribing CPU.

Som en allmän tumregel när du väljer utförar storlek:

* Börja med 30 GB per utförar och distribuera tillgängliga maskin kärnor.
* Öka antalet utförar-kärnor för större kluster (> 100-körningar).
* Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Tänk på följande när du kör samtidiga frågor:

* Börja med 30 GB per utförar och alla dator kärnor.
* Skapa flera parallella Spark-program med oversubscribing CPU (cirka 30% fördröjnings förbättring).
* Distribuera frågor över parallella program.
* Ändra storlek baserat på både vid utvärderings körning och på föregående faktorer, till exempel GC-overhead.

Övervaka dina frågeresultat för avvikande eller andra prestanda problem genom att titta på vyn tids linje, SQL graf, jobb statistik och så vidare. Ibland är ett eller flera av körningarna långsammare än de andra, och uppgifter tar mycket längre tid att köra. Detta händer ofta i större kluster (> 30 noder). I det här fallet delar du in arbetet i ett större antal aktiviteter så att Scheduler kan kompensera för långsamma aktiviteter. 

Du kan till exempel ha minst två gånger så många uppgifter som antalet utförar-kärnor i programmet. Du kan också aktivera spekulativ körning av uppgifter med `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>Optimera jobbkörningen

* Cachelagra vid behov, till exempel om du använder data två gånger och sedan cachelagrar det.
* Broadcast-variabler till alla körningar. Variablerna serialiseras bara en gång, vilket resulterar i snabbare sökningar.
* Använd trådpoolen på driv rutinen, vilket leder till snabbare drift för många aktiviteter.

Nyckeln till Spark 2. x-frågans prestanda är Tungsten-motorn, som är beroende av kodgenerering i hela fasen. I vissa fall kan generering av kod i hela fasen inaktive ras. 

Om du till exempel använder en icke-föränderligt typ ( `string` ) i agg regerings uttrycket visas i `SortAggregate` stället för `HashAggregate` . För bättre prestanda kan du till exempel prova följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

- [Justerings Apache Spark](https://spark.apache.org/docs/2.4.5/tuning.html)
- [Så här finjusterar du dina Apache Spark jobb så att de fungerar](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo-serialisering](https://github.com/EsotericSoftware/kryo)
