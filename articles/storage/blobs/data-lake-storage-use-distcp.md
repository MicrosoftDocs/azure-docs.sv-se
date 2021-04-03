---
title: Kopiera data till Azure Data Lake Storage Gen2 med DistCp | Microsoft Docs
description: Kopiera data till och från Azure Data Lake Storage Gen2 med hjälp av Apache Hadoop Distributed Copy Tool (DistCp).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912492"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen2

Du kan använda [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) för att kopiera data mellan ett General Purpose v2-lagrings konto och ett generell användning v2-lagrings konto med hierarkiskt namn område aktiverat. Den här artikeln innehåller anvisningar om hur du använder DistCp-verktyget.

DistCp innehåller en rad kommando rads parametrar och vi rekommenderar att du läser den här artikeln för att optimera användningen av den. Den här artikeln visar grundläggande funktioner när du fokuserar på att kopiera data till ett hierarkiskt namn områdes konto som är aktiverat.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett befintligt Azure Storage-konto utan Data Lake Storage Gen2 funktioner (hierarkiskt namn område) aktiverat.
* Ett Azure Storage konto med Data Lake Storage Gen2 funktioner (hierarkiskt namn område) aktiverat. Anvisningar om hur du skapar ett finns i [skapa ett Azure Storage konto](../common/storage-account-create.md)
* En behållare som har skapats i lagrings kontot med hierarkiskt namn område aktiverat.
* Ett Azure HDInsight-kluster med åtkomst till ett lagrings konto med funktionen hierarkiskt namn område aktive rad. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Se till att aktivera fjärr skrivbord för klustret.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Använda DistCp från ett HDInsight Linux-kluster

An-HDInsight kluster levereras med verktyget DistCp som kan användas för att kopiera data från olika källor till ett HDInsight-kluster. Om du har konfigurerat HDInsight-klustret så att det använder Azure Blob Storage och Azure Data Lake Storage tillsammans, kan verktyget DistCp användas direkt för att kopiera data mellan också. I det här avsnittet tittar vi på hur du använder DistCp-verktyget.

1. Skapa en SSH-session med ditt HDI-kluster. Se [ansluta till ett Linux-baserat HDInsight-kluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Kontrol lera om du kan komma åt ditt befintliga General Purpose v2-konto (utan hierarkiskt namn område aktiverat).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   Utdata ska innehålla en lista över innehåll i behållaren.

3. På samma sätt kan du kontrol lera om du kan komma åt lagrings kontot med hierarkiskt namn område som är aktiverat från klustret. Kör följande kommando:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    Utdata ska innehålla en lista över filer/mappar i Data Lake lagrings konto.

4. Använd DistCp för att kopiera data från WASB till ett Data Lake Storage-konto.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    Kommandot kopierar innehållet i mappen **/example/data/Gutenberg/** i Blob Storage till **/MyFolder** i data Lake Storage-kontot.

5. På samma sätt kan du använda DistCp för att kopiera data från Data Lake Storage-konto till Blob Storage (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    Kommandot kopierar innehållet i **/MyFolder** i data Lake Store-kontot till **/example/data/Gutenberg/** -mappen i WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestanda överväganden vid användning av DistCp

Eftersom DistCpens lägsta granularitet är en enda fil, är det största antalet samtidiga kopior som är den viktigaste parametern för att optimera den mot Data Lake Storage. Antalet samtidiga kopior är lika med antalet mappnings parametrar (**m**) på kommando raden. Den här parametern anger det maximala antalet mappningar som används för att kopiera data. Standardvärdet är 20.

**Exempel**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hur gör jag för att avgöra hur många mappningar som ska användas?

Här är några riktlinjer som du kan använda.

* **Steg 1: Bestäm det totala mängden minne som är tillgängligt för den "standard" garn app Queue** – det första steget är att avgöra vilket minne som är tillgängligt för den "standard" garn app Queue. Den här informationen finns i Ambari-portalen som är kopplad till klustret. Navigera till garn och Visa fliken configs om du vill se vilket garn minne som är tillgängligt för app-kön ' default '. Detta är den totala mängden tillgängligt minne för ditt DistCp-jobb (som faktiskt är ett MapReduce-jobb).

* **Steg 2: beräkna antalet mappningar** – värdet för **m** är lika med kvoten av det totala garn minnet delat med garn behållarens storlek. Information om garn behållarens storlek finns även tillgänglig i Ambari-portalen. Navigera till garn och Visa fliken configs. GARN behållarens storlek visas i det här fönstret. Ekvationen för att nå antalet Mapper (**m**) är

    m = (antal noder * garn minne för varje nod)/storlek på garn behållare

**Exempel**

Vi antar att du har ett 4x D14v2s-kluster och du försöker överföra 10 TB data från 10 olika mappar. Var och en av mapparna innehåller olika mängder data och fil storlekarna i varje mapp skiljer sig åt.

* **Totalt garn minne**: från Ambari-portalen fastställer du att garn minnet är 96 GB för en D14-nod. Därför är det totala garn minnet för fyra noder i klustret: 

    GARN minne = 4 * 96GB = 384GB

* **Antal mappningar**: från Ambari-portalen fastställer du att garn behållarens storlek är 3 072 MB för en D14-klusternod. Så, antalet mappningar är:

    m = (4 noder * 96GB)/3072MB = 128 mappningar

Om andra program använder minnet kan du välja att bara använda en del av klustrets garn minne för DistCp.

### <a name="copying-large-datasets"></a>Kopiera stora data uppsättningar

När storleken på data uppsättningen som ska flyttas är stor (till exempel >1 TB) eller om du har många olika mappar, bör du överväga att använda flera DistCp-jobb. Det finns troligt vis ingen prestanda ökning, men den sprider ut jobben så att om ett jobb Miss lyckas behöver du bara starta om det aktuella jobbet i stället för hela jobbet.

### <a name="limitations"></a>Begränsningar

* DistCp försöker skapa mappningar som liknar varandra i storlek för att optimera prestanda. Att öka antalet Mapper kan inte alltid öka prestandan.

* DistCp är begränsad till endast en Mapper per fil. Därför bör du inte ha fler mappningar än du har filer. Eftersom DistCp bara kan tilldela en mapp till en fil, begränsar detta den mängd samtidighet som kan användas för att kopiera stora filer.

* Om du har ett litet antal stora filer bör du dela upp dem i fil segment på 256 MB för att ge dig mer potentiell samtidighet.