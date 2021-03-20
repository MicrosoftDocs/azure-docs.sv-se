---
title: Azure Storage-lösningar för ML-tjänster i HDInsight – Azure
description: Lär dig mer om de olika lagrings alternativ som är tillgängliga med ML-tjänster i HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943911"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-lösningar för ML-tjänster på Azure HDInsight

ML-tjänster i HDInsight kan använda olika lagrings lösningar för att bevara data, kod eller objekt som innehåller resultat från analys. Dessa lösningar innehåller följande alternativ:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

Du kan också välja att komma åt flera Azure Storage-konton eller behållare med ditt HDInsight-kluster. Azure File Storage är ett användbart alternativ för data lagring som du kan använda på Edge-noden för att montera en Azure Storage-filresurs till exempelvis Linux-filsystemet. Men Azure-filresurser kan monteras och användas av alla system som har ett operativ system som stöds, till exempel Windows eller Linux.

När du skapar ett Apache Hadoop kluster i HDInsight, anger du antingen ett **Azure Blob Storage** -konto eller **data Lake Storage gen1**. En speciell lagrings behållare från det kontot innehåller fil systemet för det kluster som du skapar (till exempel Hadoop Distributed File System). Mer information och vägledning finns i:

- [Använda Azure Blob Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Använd Azure Blob Storage-konton med ML Services-kluster

Om du har angett fler än ett lagrings konto när du skapar ditt ML Services-kluster förklarar du följande instruktioner för att använda ett sekundärt konto för data åtkomst och åtgärder i ett ML Services-kluster. Antag följande lagrings konton och behållare: **storage1** och en standard behållare som heter **container1** och **storage2** med **container2**.

> [!WARNING]  
> I prestanda syfte skapas HDInsight-klustret i samma data Center som det primära lagrings konto som du anger. Det finns inte stöd för att använda ett lagrings konto på en annan plats än HDInsight-klustret.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Använd standard lagring med ML-tjänster i HDInsight

1. Med hjälp av en SSH-klient ansluter du till Edge-noden i klustret. Information om hur du använder SSH med HDInsight-kluster finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Kopiera en exempel fil mysamplefile.csv till katalogen/Share

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Växla till R Studio eller en annan R-konsol och skriv R-kod för att ange noden namn som **standard** och platsen för den fil som du vill komma åt.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Alla katalog-och fil referenser pekar på lagrings kontot `wasbs://container1@storage1.blob.core.windows.net` . Det här är det **standard lagrings konto** som är associerat med HDInsight-klustret.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Använd ytterligare lagring med ML-tjänster på HDInsight

Anta nu att du vill bearbeta en fil med namnet mysamplefile1.csv som finns i/Private-katalogen för **container2** i **storage2**.

I din R-kod pekar du på noden namn på **storage2** lagrings konto.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Alla katalog-och fil referenser pekar nu på lagrings kontot `wasbs://container2@storage2.blob.core.windows.net` . Det här är den **nodnamn som du** har angett.

Konfigurera `/user/RevoShare/<SSH username>` katalogen på **storage2** enligt följande:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Använd Azure Data Lake Storage Gen1 med ML-tjänst kluster

Om du vill använda Data Lake Storage Gen1 med ditt HDInsight-kluster måste du ge klustret åtkomst till varje Azure Data Lake Storage Gen1 som du vill använda. Instruktioner för hur du använder Azure Portal för att skapa ett HDInsight-kluster med en Azure Data Lake Storage Gen1 som standard lagrings utrymme eller som ytterligare lagrings utrymme finns i [skapa ett HDInsight-kluster med data Lake Storage gen1 med Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Sedan kan du använda lagrings utrymmet i ditt R-skript på ungefär samma sätt som du gjorde ett sekundärt Azure Storage-konto enligt beskrivningen i föregående procedur.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Lägg till kluster åtkomst till din Azure Data Lake Storage Gen1

Du får åtkomst till Data Lake Storage Gen1 med hjälp av ett Azure Active Directory (Azure AD)-tjänstens huvud namn som är associerat med ditt HDInsight-kluster.

1. När du skapar ett HDInsight-kluster väljer du **kluster-Azure AD-identitet** på fliken **data källa** .

2. I dialog rutan **kluster-Azure AD-identitet** under **Välj AD-tjänstens huvud namn** väljer du **Skapa ny**.

När du har angett ett namn för tjänsten och skapar ett lösen ord för det klickar du på **Hantera ADLS åtkomst** för att associera tjänstens huvud namn med din data Lake Storage.

Du kan också lägga till kluster åtkomst till ett eller flera Data Lake Storage gen1-konton när klustret skapas. Öppna Azure Portal posten för en Data Lake Storage Gen1 och gå till **Datautforskaren > åtkomst > Lägg till**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Få åtkomst till Data Lake Storage Gen1 från ML-tjänster i HDInsight

När du har fått åtkomst till Data Lake Storage Gen1 kan du använda lagrings utrymmet i ML-tjänst kluster på HDInsight på samma sätt som ett sekundärt Azure Storage-konto. Den enda skillnaden är att prefixet **wasbs://** ändras till **ADL://** på följande sätt:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Följande kommandon används för att konfigurera Data Lake Storage Gen1 med RevoShare-katalogen och lägga till exempel-. csv-filen från föregående exempel:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Använd Azure File Storage med ML-tjänster i HDInsight

Det finns också ett användbart alternativ för data lagring för användning på Edge-noden som kallas [Azure Files](https://azure.microsoft.com/services/storage/files/). Det gör att du kan montera en Azure Storage fil resurs till Linux-filsystemet. Det här alternativet kan vara användbart för att lagra datafiler, R-skript och resultat objekt som kan behövas senare, särskilt när det är klokt att använda det interna fil systemet på Edge-noden i stället för HDFS.

En stor fördel med Azure Files är att fil resurserna kan monteras och användas av alla system som har ett operativ system som stöds, till exempel Windows eller Linux. Den kan till exempel användas av ett annat HDInsight-kluster som du eller någon i ditt team har, av en virtuell Azure-dator eller till och med av ett lokalt system. Mer information finns i:

- [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Använda Azure File Storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nästa steg

- [Översikt över ML Services-kluster i HDInsight](r-server-overview.md)
- [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
