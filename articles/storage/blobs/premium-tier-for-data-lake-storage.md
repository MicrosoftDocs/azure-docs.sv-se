---
title: Premium-nivå för Azure Data Lake Storage | Microsoft Docs
description: Använd prestanda nivån Premium med Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93324228"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Premium-nivå för Azure Data Lake Storage

Azure Data Lake Storage Gen2 stöder nu [prestanda nivån Premium](storage-blob-performance-tiers.md#premium-performance). Premium Performance-nivån är idealisk för Big data Analytics-program och arbets belastningar som kräver låg konsekvent svars tid och har ett stort antal transaktioner.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Arbets belastningar som kan dra nytta av Premium prestanda nivån

Exempel på arbets belastningar är interaktiva arbets belastningar, IoT, streaming Analytics, artificiell intelligens och Machine Learning. 

**Interaktiva arbets belastningar** 

Dessa arbets belastningar kräver omedelbara uppdateringar och feedback från användaren, till exempel e-handel och mappning av program, interaktiva video program osv. I ett e-handelsprogram är det till exempel troligt att mindre visade objekt inte cachelagras. De måste dock visas direkt för kunden på begäran. Ett annat exempel är att data forskare, analytiker och utvecklare kan härleda tids känsliga insikter ännu snabbare genom att köra frågor om data som lagras i ett konto som använder Premium Performance-nivån. 

**IoT/streaming Analytics** 

I ett IoT-scenario kan många mindre Skriv åtgärder flyttas till molnet varje sekund. Stora mängder data kan matas in, aggregeras i analys syfte och tas sedan bort nästan omedelbart. De höga inmatnings funktionerna i Premium Performance-nivån gör det effektivt för den här typen av arbets belastning. 

**Artificiell intelligens/Machine Learning (AI/ML)** 

AI/ML hanterar användning och bearbetning av olika data typer som visuella objekt, tal och text. Den här arbets belastnings typen med hög prestanda hanterar stora mängder data som kräver snabba svars tider och effektiva inmatnings tider för data analys. 

## <a name="cost-effectiveness"></a>Kostnadseffektivitet

Premium Performance-nivån har en högre lagrings kostnad men en lägre transaktionskostnader jämfört med standard prestanda nivån. Om dina program och arbets belastningar kör ett stort antal transaktioner kan Premium Performance-nivån vara kostnads effektiv.

Följande tabell visar kostnads effektiviteten för Premium-nivån för Azure Data Lake Storage. Varje kolumn representerar antalet transaktioner per månad.  Varje rad representerar procent andelen transaktioner som är Läs transaktioner. I varje cell i tabellen visas procent andelen kostnads minskning som är associerad med en Läs transaktions procent och antalet transaktioner som har utförts. 

Anta till exempel att ditt konto är i regionen USA, östra 2. antalet transaktioner med ditt konto överskrider 90, och 70% av dessa transaktioner är Läs transaktioner, Premium Performance-nivån är mer kostnads effektiv.

> [!div class="mx-imgBorder"]
> ![bilden går här](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Om du föredrar att utvärdera kostnads effektivitet baserat på antalet transaktioner per sekund för varje TB data, kan du använda kolumn rubrikerna som visas längst ned i tabellen.

Mer information om priser finns på sidan [Azure Data Lake Storage Gen2 prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/) .

## <a name="feature-availability"></a>Funktionstillgänglighet 

Vissa Blob Storage-funktioner kanske inte är tillgängliga eller har kanske bara delvis stöd för prestanda nivån Premium. En fullständig lista finns [i Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md). Granska sedan en lista över [kända problem](data-lake-storage-known-issues.md) för att utvärdera eventuella luckor i funktionerna.

## <a name="enabling-the-premium-performance-tier"></a>Aktivera prestanda nivån Premium 

Du kan använda Premium nivån för Azure Data Lake Storage genom att skapa ett BlockBlobStorage-konto med inställningen **hierarkiskt namn område** **aktive rad**. Fullständig vägledning finns i [skapa ett](storage-blob-create-account-block-blob.md) konto för BlockBlobStorage-kontot.

När du skapar kontot, se till att välja alternativet för **Premium** -prestanda och **BlockBlobStorage** -kontots typ.

> [!div class="mx-imgBorder"]
> ![Skapa blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Aktivera inställningen **hierarkiskt namn område** på fliken **Avancerat** på sidan **skapa lagrings konto** . Du måste aktivera den här inställningen när du skapar kontot. Du kan inte aktivera det senare.

Följande bild visar den här inställningen på sidan **skapa lagrings konto** .

> [!div class="mx-imgBorder"]
> ![Inställning av hierarkiskt namn område](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Premium-nivån för Azure Data Lake Storage är tillgänglig i följande regioner.

|Region|Redundans|
|--|--|
|East US|LRS, ZRS|
|USA, östra 2|LRS, ZRS|
|Central US|LRS|
|USA, västra|LRS|
|USA, västra 2|LRS, ZRS|
|USA, västra centrala|LRS|
|USA, södra centrala|LRS|
|Kanada, centrala|LRS|
|Kanada, östra|LRS|
|Europa, norra|LRS, ZRS|
|Europa, västra|LRS, ZRS|
|Storbritannien, södra|LRS|
|Storbritannien, västra|LRS|
|Frankrike, centrala|LRS|
|Asien, östra|LRS|
|Sydkorea, centrala|LRS|
|Sydkorea, södra|LRS|
|Indien, centrala|LRS|
|Indien, västra|LRS|
|Förenade Arabemiraten, norra|LRS|
|Japan, östra|LRS|
|Japan, västra|LRS|
|Sydostasien|LRS, ZRS|
|Australien, östra|LRS, ZRS|
|Sydöstra Australien|LRS|
|Brasilien, södra|LRS|

## <a name="next-steps"></a>Nästa steg

Använd Premium-nivån för Azure Data Lake Storage med din favorit analys tjänst som Azure Databricks, Azure HDInsight och Azure Synapse Analytics. 

- [Självstudie: Azure Data Lake Storage Gen2, Azure Databricks & Spark](data-lake-storage-use-databricks-spark.md) 
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight stöder för närvarande ett konto som använder Premium Performance-nivån tillsammans med ett HBase-kluster som har accelererade skrivningar aktiverade.
- [Snabb start: skapa en Synapse-arbetsyta](../../synapse-analytics/quickstart-create-workspace.md)

