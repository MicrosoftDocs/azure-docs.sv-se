---
title: 'Arkitektur: lokal Apache Hadoop till Azure HDInsight'
description: Lär dig metod tips för att migrera lokala Hadoop-kluster till Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 519dc53f6373ae1a9c8853d3fa90d137e9fa934b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435417"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrera lokala Apache Hadoop kluster till Azure HDInsight-arkitektur metod tips

Den här artikeln innehåller rekommendationer för arkitekturen i Azure HDInsight-system. Den ingår i en serie som ger bästa praxis för att hjälpa till att migrera lokala Apache Hadoop system till Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Använd flera arbets belastnings optimerade kluster

Många lokala Apache Hadoop distributioner består av ett enda stort kluster som har stöd för många arbets belastningar. Det här enkla klustret kan vara komplicerat och kan kräva kompromisser för enskilda tjänster för att allt ska fungera tillsammans. Att migrera lokala Hadoop-kluster till Azure HDInsight kräver en ändring i metoden.

Azure HDInsight-kluster har utformats för en speciell typ av beräknings användning. Eftersom lagringen kan delas mellan flera kluster, är det möjligt att skapa flera arbets belastnings optimerade beräknings kluster för att uppfylla behoven hos olika jobb. Varje kluster typ har den optimala konfigurationen för den aktuella arbets belastningen. I följande tabell visas de kluster typer som stöds i HDInsight och motsvarande arbets belastningar.

|Arbetsbelastning|HDInsight-kluster typ|
|---|---|
|Batchbearbetning (ETL/ELT)|Hadoop, Spark|
|Datalagerhantering|Hadoop, Spark, interaktiv fråga|
|IoT/streaming|Kafka, Storm, Spark|
|NoSQL-transaktionell bearbetning|HBase|
|Interaktiva och snabbare frågor med minnes intern cachelagring|Interaktiv fråga|
|Datavetenskap|ML-tjänster, Spark|

I följande tabell visas de olika metoder som kan användas för att skapa ett HDInsight-kluster.

|Verktyg|Webbläsare baserad|Kommandorad|REST-API|SDK|
|---|---|---|---|---|
|[Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Klammerparentes](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Mer information finns i artikel [kluster typer i HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Använd tillfälliga kluster på begäran

HDInsight-kluster kan gå oanvända under långa tids perioder. HDInsight stöder tillfälliga kluster på begäran, som kan tas bort när arbets belastningen har slutförts, för att hjälpa till att spara resurs kostnader.

När du tar bort ett kluster tas inte det associerade lagrings kontot och externa metadata bort. Klustret kan senare skapas på nytt med samma lagrings konton och meta-butiker.

Azure Data Factory kan användas för att schemalägga skapande av HDInsight-kluster på begäran. Mer information finns i artikeln [skapa på begäran Apache Hadoop kluster i HDInsight med hjälp av Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Frikoppla lagring från data bearbetning

Typiska lokala Hadoop-distributioner använder samma uppsättning datorer för data lagring och data bearbetning. Eftersom de är samplacerade, måste beräkning och lagring skalas tillsammans.

I HDInsight-kluster behöver inte lagring befinna sig med beräkning och kan antingen vara i Azure Storage, Azure Data Lake Storage eller båda. Koppling av lagring från Compute har följande fördelar:

- Data delning mellan kluster.
- Användning av tillfälliga kluster eftersom data inte är beroende av klustret.
- Minskad lagrings kostnad.
- Skala lagring och Compute separat.
- Datareplikering mellan regioner.

Beräknings kluster skapas nära lagrings konto resurser i en Azure-region för att minska prestanda kostnaderna för att åtskilja beräkning och lagring. Höghastighets nätverk gör det effektivt för datornoderna att komma åt data i Azure Storage.

## <a name="use-external-metadata-stores"></a>Använda extern metadatalagring

Det finns två huvudsakliga metastores som fungerar med HDInsight-kluster: [Apache Hive](https://hive.apache.org/) och [Apache Oozie](https://oozie.apache.org/). Hive-metaarkiv är den centrala schema lagrings platsen som kan användas av data bearbetnings motorer, däribland Hadoop, Spark, LLAP, Presto och Apache gris. Oozie-metaarkiv lagrar information om schemaläggning och status för pågående och slutförda Hadoop-jobb.

HDInsight använder Azure SQL Database för Hive-och Oozie-metastores. Det finns två sätt att konfigurera en metaarkiv i HDInsight-kluster:

1. Standard metaarkiv

    - Ingen ytterligare kostnad.
    - Metaarkiv tas bort när klustret tas bort.
    - Metaarkiv kan inte delas mellan olika kluster.
    - Använder grundläggande Azure SQL DB, som har en fem DTU-gräns.

1. Anpassad extern metaarkiv

    - Ange en extern Azure SQL Database som metaarkiv.
    - Kluster kan skapas och tas bort utan att förlora metadata, till exempel jobb information för Hive-schema Oozie.
    - Enkel metaarkiv DB kan delas med olika typer av kluster.
    - Metaarkiv kan skalas upp efter behov.
    - Mer information finns i [använda externa metadata butiker i Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Metod tips för Hive-Metaarkiv

Några HDInsight-Hive-metaarkiv bästa praxis är följande:

- Använd en anpassad extern metaarkiv för att separera beräknings resurser och metadata.
- Börja med en S2-nivå i Azure SQL-instansen, som tillhandahåller 50 DTU och 250 GB lagrings utrymme. Om du ser en Flask hals kan du skala databasen uppåt.
- Dela inte metaarkiv som skapats för en HDInsight-kluster version med kluster av en annan version. Olika Hive-versioner använder olika scheman. Till exempel kan en metaarkiv inte delas med både Hive 1,2-och Hive 2,1-kluster.
- Säkerhetskopiera de anpassade metaarkiv med jämna mellanrum.
- Behåll metaarkiv-och HDInsight-klustret i samma region.
- Övervaka metaarkiv för prestanda och tillgänglighet med hjälp av Azure SQL Database övervaknings verktyg som Azure Portal eller Azure Monitor loggar.
- Kör `ANALYZE TABLE` kommandot som krävs för att generera statistik för tabeller och kolumner. Till exempel `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Metod tips för olika arbets belastningar

- Överväg att använda LLAP-kluster för interaktiva Hive-frågor med förbättrad svars tid [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) är en ny funktion i Hive 2,0 som tillåter minnes intern cachelagring av frågor. LLAP gör Hive-frågor mycket snabbare, upp till [26x snabbare än Hive 1. x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Överväg att använda Spark-jobb i stället för Hive-jobb.
- Överväg att ersätta Impala-baserade frågor med LLAP-frågor.
- Överväg att ersätta MapReduce-jobb med Spark-jobb.
- Överväg att ersätta Spark-batchjobb med låg latens med hjälp av Spark strukturerade strömmande jobb.
- Överväg att använda Azure Data Factory (ADF) 2,0 för data dirigering.
- Överväg Ambari för kluster hantering.
- Ändra data lagring från en lokal HDFS till WASB eller ADLS eller ADFS för bearbetning av skript.
- Överväg att använda Ranger RBAC i Hive-tabeller och granskning.
- Överväg att använda CosmosDB i stället för MongoDB eller Cassandra.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metod tips för infrastruktur för lokal att Azure HDInsight Hadoop migrering](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)