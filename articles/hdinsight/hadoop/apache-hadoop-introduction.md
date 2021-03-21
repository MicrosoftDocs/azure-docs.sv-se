---
title: Vad är Apache Hadoop och MapReduce – Azure HDInsight
description: En introduktion till HDInsight och Apache Hadoop Technology stack och komponenter.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 1e3fc23dd220a8d9764a64c453e9fb856307ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946520"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Vad är Apache Hadoop i Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning och analys av stordata-uppsättningar i kluster. Hadoop-eko systemet innehåller relaterad program vara och verktyg, inklusive Apache Hive, Apache HBase, Spark, Kafka och många andra.

Azure HDInsight är en fullständigt hanterad analys tjänst med full spektrum och öppen källkod i molnet för företag. Med kluster typen Apache Hadoop i Azure HDInsight kan du använda [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) resurs hantering och en enkel [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programmerings modell för att bearbeta och analysera batch-data parallellt.  Hadoop-kluster i HDInsight är kompatibla med [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage gen1](../../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Mer information om tillgängliga stackkomponenter med Hadoop-teknik i HDInsight finns i [Komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md). Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Vad är MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) är ett ramverk för program vara som används för att skriva jobb som bearbetar stora mängder data. Indata delas upp i oberoende segment. Varje segment bearbetas parallellt över noderna i klustret. Ett MapReduce-jobb består av två funktioner:

* **Mapper**: använder indata, analyserar dem (vanligt vis med filter-och sorterings åtgärder) och genererar tupler (nyckel/värde-par)

* **Minsknings** funktion: använder tupler som genereras av mappningen och utför en sammanfattnings åtgärd som skapar ett mindre kombinerat resultat från Mapper-data

Ett exempel på en grundläggande ord räkning för MapReduce-jobb illustreras i följande diagram:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Utdata för det här jobbet är antalet hur många gånger varje ord har inträffat i texten.

* Mapper tar varje rad från inmatade text som inmatade och delar upp den i ord. Den genererar ett nyckel/värde-par varje gången ett ord inträffar för ordet följt av 1. Utdata sorteras innan de skickas till minsknings verktyget.
* Minskningen summerar dessa enskilda antal för varje ord och avger ett nyckel/värde-par som innehåller ordet följt av summan av dess förekomster.

MapReduce kan implementeras på olika språk. Java är den vanligaste implementeringen och används i demonstrations syfte i det här dokumentet.

## <a name="development-languages"></a>Utvecklings språk

Språk eller ramverk som baseras på Java och Java Virtual Machine kan köras direkt som ett [MapReduce-jobb](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). Exemplet som används i det här dokumentet är ett Java MapReduce-program. Icke-java-språk, t. ex. C#, python eller fristående körbara filer, måste använda **Hadoop-direktuppspelning**.

Hadoop-direktuppspelning kommunicerar med mapper och minskar för STDIN och STDOUT. Mapper och dereducerar Läs data en rad i taget från STDIN och skriver utdata till STDOUT. Varje rad som läses eller genereras av mapper och-minskningen måste vara i formatet för ett nyckel/värde-par, avgränsade med ett tabbtecken:

`[key]\t[value]`

Mer information finns i [Hadoop streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Exempel på användning av Hadoop streaming med HDInsight finns i följande dokument:

* [Utveckla C# MapReduce-jobb](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Var börjar jag

* [Snabb start: skapa Apache Hadoop kluster i Azure HDInsight med Azure Portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Självstudie: skicka Apache Hadoop jobb i HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Utveckla Java MapReduce-program för Apache Hadoop i HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Använd Apache Hive som ett ETL-verktyg (Extract, Transform och Load)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Extrahera, transformera och läsa in (ETL) i skala](../hadoop/apache-hadoop-etl-at-scale.md)
* [Operationalisera en pipeline för dataanalys](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa Apache Hadoop kluster i HDInsight med hjälp av portalen](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Skapa Apache Hadoop kluster i HDInsight med ARM-mall](../hadoop/apache-hadoop-linux-tutorial-get-started.md)