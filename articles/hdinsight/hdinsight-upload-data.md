---
title: Ladda upp data för Apache Hadoop jobb i HDInsight
description: Lär dig att ladda upp och komma åt data för Apache Hadoop jobb i HDInsight. Använd den klassiska Azure CLI, Azure Storage Explorer, Azure PowerShell, Hadoop-kommandoraden eller Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931339"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Ladda upp data för Apache Hadoop jobb i HDInsight

HDInsight tillhandahåller ett Hadoop-distribuerat fil system (HDFS) över Azure Storage och Azure Data Lake Storage. Den här lagringen innehåller gen1 och Gen2. Azure Storage-och Data Lake Storage Gen1-och Gen2 har utformats som HDFS-tillägg. De gör det möjligt för den fullständiga uppsättningen komponenter i Hadoop-miljön att arbeta direkt med de data som hanteras. Azure Storage, Data Lake Storage Gen1 och Gen2 är distinkta fil system. Systemen är optimerade för lagring av data och beräkningar av dessa data. Information om fördelarna med att använda Azure Storage finns i [använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md). Se även [använda data Lake Storage gen1 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)och [Använd data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav innan du börjar:

* Ett Azure HDInsight-kluster. Instruktioner finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Kunskap om följande artiklar:
    * [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Använda Data Lake Storage Gen1 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Använda Data Lake Storage Gen2 med HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Ladda upp data till Azure Storage

### <a name="utilities"></a>Verktyg

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-kommando](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop-kommandot är bara tillgängligt i HDInsight-klustret. Kommandot tillåter endast inläsning av data från det lokala fil systemet i Azure Storage.  

### <a name="hadoop-command-line"></a>Hadoop-kommandorad

Hadoop-kommandoraden är bara användbar för att lagra data i Azure Storage BLOB när data redan finns på klustrets huvud nod.

Om du vill använda Hadoop-kommandot måste du först ansluta till huvudnoden med [SSH eller SparaTillFil](hdinsight-hadoop-linux-use-ssh-unix.md).

När du har anslutit kan du använda följande syntax för att ladda upp en fil till lagringen.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Till exempel `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Eftersom standard fil systemet för HDInsight är i Azure Storage, är/example/data/data.txt faktiskt i Azure Storage. Du kan också referera till filen som:

`wasbs:///example/data/data.txt`

eller

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

En lista över andra Hadoop-kommandon som fungerar med filer finns i [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> I Apache HBase-kluster är standard block storleken som används vid skrivning av data 256 KB. Även om det fungerar bra när du använder HBase-API: er eller REST-API: er kan du använda `hadoop` `hdfs dfs` kommandona eller för att skriva data som är större än ~ 12 GB. Mer information finns i [lagrings undantag för skrivning i BLOB](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafiska klienter

Det finns också flera program som tillhandahåller ett grafiskt gränssnitt för att arbeta med Azure Storage. Följande tabell är en lista över några av dessa program:

| Klient | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools för HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Lagringsutforskaren](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer för Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montera Azure Storage som lokal enhet

Se [montera Azure Storage som lokal enhet](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Ladda upp med tjänster

### <a name="azure-data-factory"></a>Azure Data Factory

Tjänsten Azure Data Factory är en fullständigt hanterad tjänst för att skapa data: lagring, bearbetning och drifts tjänster i strömlinjeformad, anpassningsbar och tillförlitlig data produktions pipelines.

|Lagringstyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopiera data till och från Azure Blob Storage med hjälp av Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopiera data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop är ett verktyg som är utformat för att överföra data mellan Hadoop och Relations databaser. Använd den för att importera data från ett relationellt databas hanterings system (RDBMS), till exempel SQL Server, MySQL eller Oracle. Sedan i Hadoop Distributed File System (HDFS). Transformera data i Hadoop med MapReduce eller Hive och exportera sedan data tillbaka till en RDBMS.

Mer information finns i [använda Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Utvecklings-SDK: er

Azure Storage kan också nås med hjälp av en Azure SDK från följande programmeringsspråk:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Mer information om hur du installerar Azure SDK: er finns i [Azure downloads](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du hämtar data till HDInsight kan du läsa följande artiklar för att lära dig hur du analyserar:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb program mässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)