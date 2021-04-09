---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig hur du frågar efter data från Azure Storage och Azure Data Lake Storage att lagra resultatet av din analys.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: cedc0ff1b3c2aa64f32445eabc800748a753981d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945425"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

Du kan lagra data i [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Eller en kombination av dessa alternativ. Med dessa lagrings alternativ kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användar data.

Apache Hadoop stöder en begreppet standard fil system. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapande processen av HDInsight-kluster kan du ange en BLOB-behållare i Azure Storage som standard fil system. Eller med HDInsight 3,6 kan du välja antingen Azure Blob Storage eller Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2 som standard fil system med några få undantag. För att kunna använda Data Lake Storage Gen1 som standard och länkad lagring, se [tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. 
* Information om hur Data Lake Storage Gen1 fungerar med HDInsight-kluster finns i [använda Azure Data Lake Storage gen1 med Azure HDInsight-kluster](./hdinsight-hadoop-use-data-lake-storage-gen1.md).
* information om hur Data Lake Storage Gen2 fungerar med HDInsight-kluster finns i [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](./hdinsight-hadoop-use-data-lake-storage-gen2.md).
* Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Lagrings konto typen **BlobStorage** kan bara användas som sekundär lagring för HDInsight-kluster.

| Typ av lagrings konto | Tjänster som stöds | Prestanda nivåer som stöds |Prestanda nivåer stöds inte| Åtkomst nivåer som stöds |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (generell användning v2)  | Blob     | Standard                    |Premium| Frekvent, låg frekvent, Arkiv\*   |
| Lagring (generell användning v1)   | Blob     | Standard                    |Premium| Ej tillämpligt                    |
| BlobStorage                    | Blob     | Standard                    |Premium| Frekvent, låg frekvent, Arkiv\*   |

Vi rekommenderar inte att du använder standard-BLOB-behållaren för lagring av affärs data. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standard behållaren innehåller program-och system loggar. Se till att hämta loggarna innan du tar bort containern.

Delning av en BLOB-behållare som standard fil system för flera kluster stöds inte.

> [!NOTE]  
> Arkiv åtkomst nivån är en offline-nivå som har en svars tid på flera timmar och rekommenderas inte för användning med HDInsight. Mer information finns i [Arkiv åtkomst nivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Komma åt filer inifrån klustret

Det finns flera sätt som du kan använda för att komma åt filerna i Data Lake Storage från ett HDInsight-kluster. URI-schemat ger okrypterad åtkomst (med *wasb:* prefixet) och TLS-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till kluster roten med:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på data åtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvud nod. I exemplen används alla tre URI-scheman. Ersätt `CONTAINERNAME` och `STORAGEACCOUNT` med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några HDFS-kommandon

1. Skapa en fil på lokal lagrings plats.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i kluster lagringen.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till kluster lagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista katalog innehåll i kluster lagringen.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre fil platser visas i syfte att illustrera. Använd endast en av posterna för faktisk körning `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Komma åt filer från externa kluster

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identifiera lagrings Sök väg från Ambari

* Om du vill identifiera den fullständiga sökvägen till det konfigurerade standard arkivet går du till:

    **HDFS**  >  **Konfiguration** och ange `fs.defaultFS` i rutan Filter indatamängd.

* Om du vill kontrol lera om wasb Store har kon figurer ATS som sekundär lagring navigerar du till:

    **HDFS**  >  **Konfiguration** och ange `blob.core.windows.net` i rutan Filter indatamängd.

Information om hur du hämtar sökvägen med Ambari REST API finns i [Hämta standard lagringen](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Blobcontainrar

Om du vill använda blobar måste du först skapa ett [Azure Storage-konto](../storage/common/storage-account-create.md). Som en del av det här steget anger du en Azure-region där lagrings kontot skapas. Klustret och lagringskontot måste finnas i samma region. Hive-metaarkiv SQL Server-databasen och Apache Oozie metaarkiv SQL Server-databasen måste finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här behållaren kan vara en befintlig blob som skapats utanför HDInsight. Eller så kan det vara en behållare som skapats för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Den här åtgärden kan skada jobb historiken. Vi rekommenderar att du använder en annan behållare för varje kluster. Lägg till delade data på ett länkat lagrings konto som anges för alla relevanta kluster i stället för standard lagrings kontot. Mer information om hur du konfigurerar länkade lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. För HBase-kluster kan du faktiskt behålla HBase Table-schemat och data genom att skapa ett nytt HBase-kluster med hjälp av standard-BLOB-behållaren som används av ett borttaget HBase-kluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Du kan också lägga till ytterligare lagrings konton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapande processen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Med den här lagringen kan du bygga anpassningsbara, långsiktiga, arkivera data hämtnings lösningar och använda HDInsight för att låsa upp informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Snabb start: skapa Apache Hadoop kluster](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Självstudie: skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Överföra data till HDInsight](hdinsight-upload-data.md)
* [Självstudie: extrahera, transformera och läsa in data med hjälp av interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)