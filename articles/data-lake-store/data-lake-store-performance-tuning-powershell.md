---
title: Azure Data Lake Storage Gen1 prestanda justering – PowerShell
description: Tips om hur du kan förbättra prestandan när du använder Azure PowerShell med Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723838"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Vägledning för prestanda justering för att använda PowerShell med Azure Data Lake Storage Gen1

Den här artikeln beskriver de egenskaper som du kan finjustera för att få bättre prestanda när du använder PowerShell för att arbeta med Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Prestanda relaterade egenskaper

| Egenskap            | Standardvärde | Beskrivning |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Med den här parametern kan du välja antalet parallella trådar för att ladda upp eller ned varje fil. Det här talet representerar Max antalet trådar som kan allokeras per fil, men du kan få färre trådar beroende på ditt scenario (om du till exempel laddar upp en 1 KB-fil får du en tråd även om du ber om 20 trådar).  |
| ConcurrentFileCount | 10      | Den här parametern är specifikt för att ladda upp och ned mappar. Den här parametern anger antalet samtidiga filer som kan laddas upp eller ned. Det här antalet representerar det maximala antalet samtidiga filer som kan överföras eller laddas ned samtidigt, men du kan få mindre samtidighet beroende på ditt scenario (om du t. ex. överför två filer, får du två samtidiga filer, även om du ber om 15). |

**Exempel:**

Det här kommandot laddar ned filer från Data Lake Storage Gen1 till användarens lokala enhet med 20 trådar per fil och 100 samtidiga filer.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Så här avgör du egenskaps värden

Nästa fråga som du kan ha är att avgöra vilket värde som ska tillhandahållas för prestanda relaterade egenskaper. Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ det totala antalet trådar** -börja genom att beräkna det totala antalet trådar som ska användas. Som en allmän rikt linje bör du använda sex trådar för varje fysisk kärna.

    `Total thread count = total physical cores * 6`

    **Exempel:**

    Vi antar att du kör PowerShell-kommandon från en virtuell D14-dator med 16 kärnor

    `Total thread count = 16 cores * 6 = 96 threads`

* **Steg 2: beräkna PerFileThreadCount** – vi beräknar vår PerFileThreadCount baserat på filernas storlek. För filer som är mindre än 2,5 GB behöver du inte ändra den här parametern eftersom standardvärdet 10 är tillräckligt. För filer som är större än 2,5 GB ska du använda 10 trådar som bas för de första 2,5 GB och lägga till 1 tråd för varje ytterligare 256-MB ökning i fil storleken. Om du kopierar en mapp med många olika filstorlekar bör du överväga att gruppera dem enligt liknande filstorlekar. Olika stora filstorlekar kan orsaka bristfälliga prestanda. Om du inte kan gruppera liknande filstorlekar ska du ställa in PerFileThreadCount baserat på den största filstorleken.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exempel:**

    Förutsatt att du har 100 filer som sträcker sig från 1 GB till 10 GB, använder vi 10 GB som största fil storlek för Equation, som skulle kunna läsas som följande.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Steg 3: beräkna ConcurrentFilecount** – Använd det totala antalet trådar och PerFileThreadCount för att beräkna ConcurrentFilecount baserat på följande ekvation:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exempel:**

    Utifrån de exempelvärden vi har använt

    `96 = 40 * ConcurrentFileCount`

    Så, **ConcurrentFileCount** är **2,4**, vilket vi kan runda av till **2**.

## <a name="further-tuning"></a>Ytterligare justering

Du kan behöva justera ytterligare eftersom det finns en mängd filstorlekar att arbeta med. Den föregående beräkningen fungerar bra om alla eller de flesta filerna är större och närmare intervallet på 10 GB. Om det finns i stället många olika filstorlekar med många filer som är mindre kan du minska PerFileThreadCount. Du kan öka ConcurrentFileCount genom att minska PerFileThreadCount. Så om vi antar att de flesta av våra filer är mindre inom 5 GB-intervallet kan vi göra om vår beräkning:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Därför blir **ConcurrentFileCount** 96/20, som är 4,8, avrundat till **4**.

Du kan fortsätta att finjustera inställningarna genom att ändra **PerFileThreadCount** upp och ned beroende på distributionen av din filstorlek.

### <a name="limitation"></a>Begränsning

* **Antalet filer är mindre än ConcurrentFileCount**: Om antalet filer som du laddar upp är mindre än **ConcurrentFileCount** du har beräknat ska du minska **ConcurrentFileCount** så att det motsvarar antalet filer. Du kan använda eventuella återstående trådar för att öka **PerFileThreadCount**.

* **För många trådar**: Om du ökar antalet trådar för mycket utan att öka klusterstorleken riskerar du att prestanda försämras. Det kan uppstå konkurrensproblem när du växlar innehåll på processorn.

* **Otillräcklig samtidighet**: Om samtidigheten inte är tillräcklig kan klustret vara för litet. Du kan öka antalet noder i klustret, vilket ger dig mer samtidighet.

* **Begränsningsfel**: Om konkurrensen är för hög kan det leda till begränsningsfel. Om du råkar ut för begränsningsfel ska du antingen minska samtidigheten eller kontakta oss.

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Data Lake Storage Gen1 för Big data-krav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

