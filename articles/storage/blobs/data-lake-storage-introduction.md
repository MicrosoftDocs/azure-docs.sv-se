---
title: Azure Data Lake Storage Gen2 introduktion
description: Läs en introduktion till Azure Data Lake Storage Gen2. Lär dig viktiga funktioner. Granska stödda Blob Storage-funktioner, Azure Service integrations och plattformar.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879906"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduktion till Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys och bygger på [Azure Blob Storage](storage-blobs-introduction.md). 

Data Lake Storage Gen2 konvergerar funktionerna i [Azure Data Lake Storage gen1](../../data-lake-store/index.yml) med Azure Blob Storage. Data Lake Storage Gen2 innehåller till exempel fil systemets semantik, säkerhet på filnivå och skalning. Eftersom dessa funktioner bygger på Blob Storage får du också låg kostnad, nivå lagring med hög tillgänglighet och haveri beredskap.

## <a name="designed-for-enterprise-big-data-analytics"></a>Utformad för företags Big data Analytics

Data Lake Storage Gen2 gör Azure Storage grunden för att skapa företags data sjöar på Azure. Data Lake Storage Gen2 är utformad från start-till-tjänst-flera petabyte med information medan hundratals Gigabit av data flödet används, så att du enkelt kan hantera enorma mängder data.

En grundläggande del av Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](data-lake-storage-namespace.md) till Blob Storage. Det hierarkiska namn området ordnar objekt/filer i en hierarki med kataloger för effektiv data åtkomst. En vanlig namngivnings konvention för objekt lagring använder snedstreck i namnet för att imitera en hierarkisk katalog struktur. Den här strukturen blir riktig med Data Lake Storage Gen2. Åtgärder som att byta namn på eller ta bort en katalog blir enkla atomiska metadata-åtgärder i katalogen. Du behöver inte räkna upp och bearbeta alla objekt som delar namn-prefixet för katalogen.

Data Lake Storage Gen2 bygger på Blob Storage och förbättrar prestanda, hantering och säkerhet på följande sätt:

-   **Prestanda** optimeras eftersom du inte behöver kopiera eller transformera data som ett krav för analys. Jämfört med det flata namn området på Blob Storage förbättrar det hierarkiska namn området prestandan för katalog hanterings åtgärder, vilket förbättrar jobbets övergripande prestanda.

-   Det är enklare att **Hantera hantering** eftersom du kan organisera och manipulera filer via kataloger och under kataloger.

-   **Säkerhet** är tvingande eftersom du kan definiera POSIX-behörigheter för kataloger eller enskilda filer.

Data Lake Storage Gen2 är också mycket kostnads effektiv eftersom det bygger på [Azure Blob Storage](storage-blobs-introduction.md)med låg kostnad. De ytterligare funktionerna sänker längre den totala ägande kostnaden för att köra stor data analys på Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Viktiga funktioner i Data Lake Storage Gen2

-   **Hadoop-kompatibel åtkomst**: data Lake Storage Gen2 gör att du kan hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den nya [ABFS-drivrutinen](data-lake-storage-abfs-driver.md) (som används för att komma åt data) är tillgänglig i alla Apache Hadoop miljöer. I dessa miljöer ingår [Azure HDInsight](../../hdinsight/index.yml)*,* [Azure Databricks](/azure/databricks/)och [Azure Synapse Analytics](../../synapse-analytics/index.yml).

-   **En supermängd av POSIX-behörigheter**: säkerhets modellen för data Lake Gen2 stöder ACL-och POSIX-behörigheter tillsammans med viss extra detaljerad information som är speciell för data Lake Storage Gen2. Inställningarna kan konfigureras via Storage Explorer eller genom ramverk som Hive och Spark.

-   **Kostnads effektiv**: data Lake Storage Gen2 erbjuder lagrings kapacitet och transaktioner med låg kostnad. Funktioner som [Azure Blob Storage-livscykel](storage-lifecycle-management-concepts.md) optimerar kostnader som data över gångar genom livs cykeln.

-   **Optimerad driv rutin**: ABFS-drivrutinen har [optimerats specifikt](data-lake-storage-abfs-driver.md) för stor data analys. Motsvarande REST-API: er visas genom slut punkten `dfs.core.windows.net` .

### <a name="scalability"></a>Skalbarhet

Azure Storage är skalbart genom att designa om du kommer åt via Data Lake Storage Gen2-eller Blob Storage-gränssnitt. Den kan lagra och betjäna *många exabyte data*. Den här mängden lagring är tillgänglig med data flöde mätt i gigabit per sekund (Gbit/s) vid höga nivåer av in-/utdata-åtgärder per sekund (IOPS). Bearbetning utförs i nära konstant svar per begäran som mäts på tjänst-, konto-och fil nivåer.

### <a name="cost-effectiveness"></a>Kostnadseffektivitet

Eftersom Data Lake Storage Gen2 byggts ovanpå Azure Blob Storage, är lagrings kapacitet och transaktionskostnader lägre. Till skillnad från andra moln lagrings tjänster behöver du inte flytta eller transformera dina data innan du kan analysera dem. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage).

Dessutom förbättrar funktioner som det [hierarkiska namn området](data-lake-storage-namespace.md) avsevärt den övergripande prestandan för många analys jobb. Den här förbättringen av prestanda innebär att du behöver mindre beräknings kraft för att bearbeta samma data mängd, vilket resulterar i en lägre totalkostnad (TCO) för det slutliga analys jobbet.

### <a name="one-service-multiple-concepts"></a>En tjänst, flera koncept

Eftersom Data Lake Storage Gen2 skapas ovanpå Azure Blob Storage kan flera koncept beskriva samma delade saker.

Följande är motsvarande entiteter, enligt beskrivningen i olika koncept. Om inget annat anges är dessa entiteter direkt synonyma:

| Koncept                                | Organisation på högsta nivå | Organisation på lägre nivå                                            | Data behållare |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobbar – lagring av generell användnings objekt | Container              | Virtuell katalog (endast SDK – ger inte atomiska modifieringar) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Katalog                                                           | Fil           |

## <a name="supported-blob-storage-features"></a>Blob Storage-funktioner som stöds

Blob Storage-funktioner som [diagnostisk loggning](../common/storage-analytics-logging.md), [åtkomst nivåer](storage-blob-storage-tiers.md)och [Blob Storage principer för livs cykel hantering](storage-lifecycle-management-concepts.md) är tillgängliga för ditt konto. 

En lista över stödda Blob Storage-funktioner finns i [Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Azure Service-integration som stöds

Data Lake Storage Gen2 stöder flera Azure-tjänster. Du kan använda dem för att mata in data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plattformar för öppen källkod som stöds

Flera plattformar med öppen källkod har stöd för Data Lake Storage Gen2. En fullständig lista finns i [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Se även

- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Åtkomst till flera protokoll på Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)