---
title: Apache Hadoop-arkitektur – Azure HDInsight
description: Beskriver Apache Hadoop lagring och bearbetning i Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 6f291e5aa440a3e6e45a1dcdb872e18c8d4557ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945898"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-arkitektur i HDInsight

[Apache Hadoop](https://hadoop.apache.org/) innehåller två huvud komponenter: [Apache HADOOP Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som tillhandahåller lagring, och [Apache Hadoop än en annan resurs Negotiator (garn)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) som tillhandahåller bearbetning. Med lagrings-och bearbetnings funktioner kan ett kluster köra [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) -program för att utföra önskad data bearbetning.

> [!NOTE]  
> En HDFS distribueras vanligt vis inte i HDInsight-klustret för att tillhandahålla lagring. I stället används ett HDFS-kompatibelt gränssnitts lager av Hadoop-komponenter. Den faktiska lagrings kapaciteten tillhandahålls av antingen Azure Storage eller Azure Data Lake Storage. För Hadoop körs MapReduce-jobb som körs på HDInsight-klustret som om en HDFS fanns och det krävs inga ändringar för att stödja deras lagrings behov. I Hadoop på HDInsight är lagringen förflyttad, men garn bearbetning är fortfarande en kärn komponent. Mer information finns i [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md).

I den här artikeln introduceras garn och hur det koordinerar körningen av program i HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Grunderna för Apache Hadoop garn

GARN styr och dirigerar data bearbetning i Hadoop. GARN har två kärn tjänster som körs som processer på noder i klustret:

* ResourceManager
* NodeManager

Resurs hanteraren ger kluster beräknings resurser till program som MapReduce-jobb. Resurs hanteraren beviljar dessa resurser som behållare, där varje behållare består av en allokering av processor kärnor och RAM-minne. Om du kombinerar alla resurser som är tillgängliga i ett kluster och sedan distribuerar kärnor och minne i block, är varje resurs block en behållare. Varje nod i klustret har en kapacitet för ett visst antal behållare, och därför har klustret en fast gräns för antalet behållare som är tillgängliga. Tilldelningen av resurser i en behållare kan konfigureras.

När ett MapReduce-program körs i ett kluster, tillhandahåller ResourceManager programmet de behållare som ska köras. ResourceManager spårar statusen för program som körs, tillgänglig kluster kapacitet och spårar program när de är klara och släpper ut resurserna.

ResourceManager kör också en webb Server process som tillhandahåller ett webb användar gränssnitt för att övervaka program status.

När en användare skickar ett MapReduce-program för att köras i klustret skickas programmet till ResourceManager. I sin tur allokerar ResourceManager en behållare på tillgängliga NodeManager-noder. NodeManager-noderna är där programmet faktiskt körs. Den första behållaren som tilldelas kör ett särskilt program som kallas ApplicationMaster. Den här ApplicationMaster ansvarar för att förvärva resurser i form av efterföljande behållare som behövs för att köra programmet som skickas. ApplicationMaster undersöker programmets stadier, till exempel kart steget och minskar fasen, samt faktorer i hur mycket data som behöver bearbetas. ApplicationMaster begär sedan (*förhandlar*) resurserna från ResourceManager för programmets räkning. Resurs hanteraren i ger sin tur till gång till resurser från) Nodemanagers i klustret till ApplicationMaster som används för att köra programmet.

) Nodemanagers kör de uppgifter som utgör programmet och rapporterar sedan deras förlopp och status tillbaka till ApplicationMaster. ApplicationMaster i sin tur rapporterar status för programmet tillbaka till ResourceManager. ResourceManager returnerar alla resultat till klienten.

## <a name="yarn-on-hdinsight"></a>GARN på HDInsight

Alla typer av HDInsight-kluster distribuerar garn. ResourceManager distribueras för hög tillgänglighet med en primär och sekundär instans, som körs på de första och andra Head-noderna i klustret. Endast den enda instansen av ResourceManager är aktiv i taget. NodeManager-instanserna körs över tillgängliga arbetsnoder i klustret.

![Apache-garn på Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Mjuk borttagning

Om du vill ångra borttagningen av en fil från ditt lagrings konto, se:

### <a name="azure-storage"></a>Azure Storage

* [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/soft-delete-blob-overview.md)
* [Ångra borttagning av BLOB](/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Restore-AzDataLakeStoreDeletedItem](/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Kända problem med Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Rensning av skräp

`fs.trash.interval`Egenskapen från **HDFS**  >  **Advanced Core – site** ska vara kvar på standardvärdet `0` eftersom du inte bör lagra några data i det lokala fil systemet. Det här värdet påverkar inte fjär lagrings konton (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Nästa steg

* [Använda MapReduce i Apache Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)