---
title: Ladda upp data för Apache Hadoop-jobb i HDInsight
description: Lär dig hur du laddar upp och kommer åt data för Apache Hadoop-jobb i HDInsight. Använd klassiska Azure CLI, Azure Storage Explorer, Azure PowerShell, Hadoop-kommandoraden eller Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482117"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Ladda upp data för Apache Hadoop-jobb i HDInsight

HDInsight tillhandahåller ett Hadoop-distribuerat filsystem (HDFS) över Azure Storage och Azure Data Lake Storage. Den här lagringen innehåller Gen1 och Gen2. Azure Storage och Data Lake Storage Gen1 Gen2 är utformade som HDFS-tillägg. De gör att hela uppsättningen komponenter i Hadoop-miljön kan arbeta direkt med de data som den hanterar. Azure Storage, Data Lake Storage Gen1 och Gen2 är olika filsystem. Systemen är optimerade för lagring av data och beräkningar på dessa data. Information om fördelarna med att använda Azure Storage finns i [Använda Azure Storage hdinsight.](hdinsight-hadoop-use-blob-storage.md) Se även Use Data Lake Storage Gen1 with HDInsight (Använda med [HDInsight)](hdinsight-hadoop-use-data-lake-storage-gen1.md)och [Use Data Lake Storage Gen2 with HDInsight (Använda Data Lake Storage Gen2 med HDInsight).](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Anvisningar finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Kunskap om följande artiklar:
    * [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Använda Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Använda Data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

### <a name="utilities"></a>Verktyg

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md) |âé" |âé" |âé" |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |âé" |âé" |âé" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |âé" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |âé" | |âé" |
| [Hadoop-kommando](#hadoop-command-line) |âé" |âé" |âé" |

> [!NOTE]  
> Hadoop-kommandot är endast tillgängligt i HDInsight-klustret. Kommandot tillåter endast inläsning av data från det lokala filsystemet till Azure Storage.  

### <a name="hadoop-command-line"></a>Hadoop-kommandorad

Hadoop-kommandoraden är bara användbar för att lagra data i Azure Storage Blob när data redan finns på klustrets huvudnod.

Om du vill använda Hadoop-kommandot måste du först ansluta till huvudnoden med [hjälp av SSH eller PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten kan du använda följande syntax för att ladda upp en fil till lagringen.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standardfilsystemet för HDInsight finns Azure Storage /example/data/data.txt faktiskt i Azure Storage. Du kan också referera till filen som:

`wasbs:///example/data/data.txt`

eller

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

En lista över andra Hadoop-kommandon som fungerar med filer finns i [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> I Apache HBase-kluster är standardblockstorleken som används vid skrivning av data 256 KB. Detta fungerar bra när du använder HBase-API:er eller REST API:er, men om du använder kommandona eller för att skriva data som är större än `hadoop` `hdfs dfs` ~12 GB resulterar det i ett fel. Mer information finns i [lagringsfel för skrivning på blob](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafiska klienter

Det finns också flera program som tillhandahåller ett grafiskt gränssnitt för att arbeta med Azure Storage. Följande tabell är en lista över några av dessa program:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |âé" |âé" |âé" |
| [Azure Lagringsutforskaren](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |âé" |âé" |âé" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |âé" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |âé" |
| [CloudBerry Explorer för Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |âé" |
| [Cyberduck](https://cyberduck.io/) | |âé" |âé" |

## <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet

Se [Montera Azure Storage som Lokal enhet.](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)

## <a name="upload-using-services"></a>Ladda upp med hjälp av tjänster

### <a name="azure-data-factory"></a>Azure Data Factory

Tjänsten Azure Data Factory är en fullständigt hanterad tjänst för att skapa data: lagrings-, bearbetnings- och rörelsetjänster till effektiva, anpassningsbara och tillförlitliga dataproduktionspipelines.

|Lagringstyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopiera data till och från Azure Blob Storage med hjälp av Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiera data till eller från Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Läsa in data i Azure Data Lake Storage Gen2 med Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop är ett verktyg som utformats för att överföra data mellan Hadoop och relationsdatabaser. Använd den för att importera data från ett hanteringssystem för relationsdatabaser (RDBMS), till exempel SQL Server, MySQL eller Oracle. Sedan till HDFS (Hadoop Distributed File System). Transformera data i Hadoop med MapReduce eller Hive och exportera sedan tillbaka data till en RDBMS.

Mer information finns i [Använda Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Utvecklings-SDK:er

Azure Storage kan också nås med hjälp av en Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure-SDK:er finns i [Azure-nedladdningar](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du hämtar data till HDInsight kan du läsa följande artiklar för att lära dig analys:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop-jobb programmatiskt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
