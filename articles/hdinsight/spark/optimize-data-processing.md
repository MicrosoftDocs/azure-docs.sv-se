---
title: Optimera data bearbetning för Apache Spark – Azure HDInsight
description: Lär dig hur du väljer de mest effektiva åtgärderna för att bearbeta dina data på Apache Spark med Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944716"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Optimering av data bearbetning för Apache Spark

Den här artikeln beskriver hur du optimerar konfigurationen av Apache Spark-klustret för bästa prestanda i Azure HDInsight.

## <a name="overview"></a>Översikt

Om du har långsamma jobb för en koppling eller en blandning är orsaken förmodligen *data skevning*. Data skevningen är asymmetry i dina jobb data. Till exempel kan ett kart jobb ta 20 sekunder. Men att köra ett jobb där data är anslutna eller blandade tar timmar. Om du vill åtgärda data skevningen bör du salta hela nyckeln eller använda ett *isolerat salt* för vissa nycklar. Om du använder ett isolerat salt bör du ytterligare filtrera för att isolera din delmängd av saltade nycklar i kart kopplingar. Ett annat alternativ är att introducera en Bucket-kolumn och församlad i Bucket först.

En annan faktor som orsakar långsamma kopplingar kan vara kopplings typen. Som standard använder Spark `SortMerge` typen Join. Den här typen av koppling passar bäst för stora data mängder. Men är i övrigt kostsamt, eftersom det måste först sortera vänster och höger sida med data innan de sammanfogas.

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

## <a name="optimize-job-execution"></a>Optimera jobbkörningen

* Cachelagra vid behov, till exempel om du använder data två gånger och sedan cachelagrar det.
* Broadcast-variabler till alla körningar. Variablerna serialiseras bara en gång, vilket resulterar i snabbare sökningar.
* Använd trådpoolen på driv rutinen, vilket leder till snabbare drift för många aktiviteter.

Övervaka dina jobb som körs regelbundet för prestanda problem. Om du behöver mer information om vissa problem kan du överväga något av följande verktyg för prestanda profilering:

* [Verktyget Intel PAL](https://github.com/intel-hadoop/PAT) övervakar processor-, lagrings-och nätverks bandbredds användning.
* [Oracle Java 8-uppdrags kontroll](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profiler Spark och utförar-kod.

Nyckeln till Spark 2. x-frågans prestanda är Tungsten-motorn, som är beroende av kodgenerering i hela fasen. I vissa fall kan generering av kod i hela fasen inaktive ras. Om du till exempel använder en icke-föränderligt typ ( `string` ) i agg regerings uttrycket visas i `SortAggregate` stället för `HashAggregate` . För bättre prestanda kan du till exempel prova följande och sedan återaktivera kodgenerering:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nästa steg

* [Optimera data lagring för Apache Spark](optimize-data-storage.md)
* [Optimera minnes användning för Apache Spark](optimize-memory-usage.md)
* [Optimera kluster konfigurationen för Apache Spark](optimize-cluster-configuration.md)
