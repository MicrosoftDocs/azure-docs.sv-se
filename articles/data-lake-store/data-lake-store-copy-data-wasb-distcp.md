---
title: Kopiera data till och från WASB till Azure Data Lake Storage Gen1 med DistCp
description: Använd DistCp-verktyget för att kopiera data till och från Azure Storage blobbar till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515674"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Om du har ett HDInsight-kluster med åtkomst till Azure Data Lake Storage Gen1 kan du använda Hadoop-eko verktyg som DistCp för att kopiera data till och från en HDInsight-kluster lagring (WASB) till ett Data Lake Storage Gen1-konto. Den här artikeln visar hur du använder DistCp-verktyget.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md).
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage gen1-konto. Se [skapa ett HDInsight-kluster med data Lake Storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att aktivera fjärr skrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använda DistCp från ett HDInsight Linux-kluster

An-HDInsight Cluster levereras med verktyget DistCp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret så att det använder Data Lake Storage Gen1 som ytterligare lagrings utrymme kan du använda DistCp out-of-box för att kopiera data till och från ett Data Lake Storage Gen1 konto. I det här avsnittet tittar vi på hur du använder DistCp-verktyget.

1. Använd SSH för att ansluta till klustret från Skriv bordet. Se [ansluta till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Kör kommandona från SSH-prompten.

1. Kontrol lera om du kan komma åt Azure Storage blobbar (WASB). Kör följande kommando:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Utdata innehåller en lista med innehåll i Storage-blobben.

1. På samma sätt kan du kontrol lera om du kan komma åt Data Lake Storage Gen1-kontot från klustret. Kör följande kommando:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Utdata innehåller en lista över filer och mappar i Data Lake Storage Gen1-kontot.

1. Använd DistCp för att kopiera data från WASB till ett Data Lake Storage Gen1-konto.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Kommandot kopierar innehållet i mappen **/example/data/Gutenberg/** i WASB till **/MyFolder** i data Lake Storage gen1-kontot.

1. På samma sätt kan du använda DistCp för att kopiera data från ett Data Lake Storage Gen1-konto till WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Kommandot kopierar innehållet i **/MyFolder** i data Lake Storage gen1-kontot till **/example/data/Gutenberg/** -mappen i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestanda överväganden vid användning av DistCp

Eftersom DistCp-verktygets lägsta granularitet är en enda fil, är det största antalet samtidiga kopior som är den viktigaste parametern för att optimera den mot Data Lake Storage Gen1. Du kan kontrol lera antalet samtidiga kopior genom att ange antalet Mapper-parametrar på kommando raden. Den här parametern anger det maximala antalet mappningar som används för att kopiera data. Standardvärdet är 20.

Exempel:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Ta reda på hur många Mapper som ska användas

Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ totalt garn minne** – det första steget är att fastställa det garn minne som är tillgängligt för klustret där du kör DistCp-jobbet. Den här informationen finns i Ambari-portalen som är kopplad till klustret. Navigera till garn och Visa fliken **configs** om du vill se garn minnet. Om du vill hämta det totala garn minnet multiplicerar du garn minnet per nod med antalet noder som du har i klustret.

* **Steg 2: beräkna antalet mappningar** – värdet för **m** är lika med kvoten av det totala garn minnet delat med garn behållarens storlek. Information om garn behållarens storlek är också tillgänglig i Ambari-portalen. Navigera till garn och Visa fliken **configs** . GARN behållarens storlek visas i det här fönstret. Ekvationen för att nå antalet Mapper (**m**) är:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Exempel:

Vi antar att du har fyra D14v2s-noder i klustret och att du vill överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och fil storlekarna i varje mapp skiljer sig åt.

* Totalt garn minne – från Ambari-portalen fastställer du att garn minnet är 96 GB för en D14-nod. Därför är det totala garn minnet för fyra noder i klustret:

   `YARN memory = 4 * 96GB = 384GB`

* Antal mappningar – från Ambari-portalen avgör du att storleken på garn container är 3072 för en D14-klusternod. Så är antalet mappningar:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Om andra program använder minne kan du välja att bara använda en del av klustrets garn minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora data uppsättningar

När storleken på data uppsättningen som ska flyttas är stor (till exempel > 1 TB) eller om du har många olika mappar, bör du överväga att använda flera DistCp-jobb. Det finns förmodligen ingen prestanda ökning, men den sprider ut jobben så att om ett jobb Miss lyckas behöver du bara starta om det aktuella jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappningar som liknar varandra i storlek för att optimera prestanda. Att öka antalet Mapper kan inte alltid öka prestandan.

* DistCp är begränsad till endast en Mapper per fil. Därför bör du inte ha fler mappningar än du har filer. Eftersom DistCp bara kan tilldela en mapp till en fil, begränsar detta den mängd samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer kan du dela upp dem i fil segment på 256 MB för att ge dig mer potentiell samtidighet.

* Om du kopierar från ett Azure Blob Storage-konto kan ditt kopierings jobb begränsas på Blob Storage-sidan. Detta minskar prestandan för ditt kopierings jobb. Mer information om gränserna för Azure Blob Storage finns i Azure Storage gränser för [Azure-prenumeration och tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Se även

* [Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
