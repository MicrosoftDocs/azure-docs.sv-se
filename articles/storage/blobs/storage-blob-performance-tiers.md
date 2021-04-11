---
title: Blockera prestanda nivåer för Blob Storage – Azure Storage
description: Beskriver skillnaden mellan Premium-och standard prestanda nivåer för Azure Block Blob Storage.
author: twooley
ms.author: twooley
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 4affed8a1226f02970928b2c227b0295167959de
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277105"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Prestandanivåer för blockbloblagring

När företag distribuerar prestanda känsliga moln program är det viktigt att du har alternativ för kostnads effektiv data lagring på olika prestanda nivåer.

Azure Block Blob Storage erbjuder två olika prestanda nivåer:

- **Premium**: optimerat för höga transaktions priser och ensiffriga konsekvent lagrings fördröjning
- **Standard**: optimerad för hög kapacitet och högt data flöde

Följande överväganden gäller för de olika prestanda nivåerna:

| Område |Standard prestanda  |Förstklassig prestanda  |
|---------|---------|---------|
|Regional tillgänglighet     |   Alla regioner      | I [Välj regioner](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|[Typer av lagrings konton](../common/storage-account-overview.md#types-of-storage-accounts) som stöds     |     Generell användning v2, BlobStorage, generell användning v1    |    BlockBlobStorage     |
|Stöder [block blobbar med hög genomflöde](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Ja, med fler än 4 MiB PutBlock-eller PutBlob-storlekar     |    Ja, med fler än 256 KiB-PutBlock eller PutBlob storlek    |
|Redundans     |     Se [typer av lagrings konton](../common/storage-account-overview.md#types-of-storage-accounts)   |  För närvarande endast stöd för lokalt redundant lagring (LRS) och redudant-lagring (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> Zone-redundant lagring (ZRS) är tillgänglig i SELECT-regioner för Premium Performance Block Blob Storage-konton.</div>

För kostnader ger Premium prestanda optimerade priser för program med höga transaktions priser för att [minska den totala lagrings kostnaden](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) för dessa arbets belastningar.

## <a name="premium-performance"></a>Förstklassig prestanda

Med Premium Performance Block Blob Storage blir data tillgängliga via maskin vara med hög prestanda. Data lagras i solid state-hårddiskar (SSD) som är optimerade för låg latens. SSD ger högre data flöde jämfört med traditionella hård diskar.

Premium Performance Storage är perfekt för arbets belastningar som kräver snabba och konsekventa svars tider. Det är bäst för arbets belastningar som utför många små transaktioner. Exempel på arbets belastningar är:

- **Interaktiva arbets belastningar**. Dessa arbets belastningar kräver omedelbara uppdateringar och feedback från användaren, till exempel e-handel och mappnings program. I ett e-handelsprogram är det till exempel troligt att mindre visade objekt inte cachelagras. De måste dock visas direkt för kunden på begäran.

- **Analys**. I ett IoT-scenario kan många mindre Skriv åtgärder flyttas till molnet varje sekund. Stora mängder data kan tas i, aggregeras i analys syfte och tas sedan bort nästan omedelbart. De höga inmatnings funktionerna i Premium Block Blob Storage gör det effektivt för den här typen av arbets belastning.

- **Artificiell intelligens/Machine Learning (AI/ml)**. AI/ML hanterar användning och bearbetning av olika data typer som visuella objekt, tal och text. Den här arbets belastnings typen med hög prestanda hanterar stora mängder data som kräver snabba svars tider och effektiva inmatnings tider för data analys.

- **Data omvandling**. Processer som kräver konstant redigering, ändring och data konvertering kräver omedelbara uppdateringar. För korrekt data åter givning måste användarna av dessa data se att ändringarna återspeglas direkt.

## <a name="standard-performance"></a>Standard prestanda

Standard prestanda stöder olika [åtkomst nivåer](storage-blob-storage-tiers.md) för att lagra data på det mest kostnads effektiva sättet. Den är optimerad för hög kapacitet och högt data flöde på stora data mängder.

- **Data uppsättningar för säkerhets kopiering och haveri beredskap**. Standard prestanda lagring erbjuder kostnads effektiva nivåer, vilket gör det till ett perfekt användnings fall för både kortsiktiga och långsiktiga data uppsättningar för haveri beredskap, sekundära säkerhets kopieringar och data arkivering för regelefterlevnad.

- **Medie innehåll**. Bilder och videor används ofta ofta när de först skapas och lagras, men den här innehålls typen används mindre ofta när den blir äldre. Standard prestanda lagring erbjuder lämpliga nivåer för medie innehålls behov. 

- **Mass bearbetning av data**. Dessa typer av arbets belastningar är lämpliga för standard lagring eftersom de kräver kostnads effektiv lagring med höga data flöden i stället för konsekvent låg latens. Stora data uppsättningar för RAW-datauppsättningar mellanlagras för bearbetning och slutligen till slut på låg frekventa nivåer.

## <a name="migrate-from-standard-to-premium"></a>Migrera från standard till Premium

Du kan inte konvertera ett befintligt lagrings konto för standard prestanda till ett Block-Blob Storage-konto med förstklassig prestanda. Om du vill migrera till ett lagrings konto för Premium-prestanda måste du skapa ett BlockBlobStorage-konto och migrera data till det nya kontot. Mer information finns i [skapa ett BlockBlobStorage-konto](storage-blob-create-account-block-blob.md).

Om du vill kopiera blobbar mellan lagrings konton kan du använda den senaste versionen av kommando rads verktyget [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) . Andra verktyg, till exempel Azure Data Factory, är också tillgängliga för data förflyttning och omvandling.

## <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Livs cykel hantering för Blob Storage erbjuder en omfattande, regelbaserade princip:

- **Premium**: data förfaller i slutet av livs cykeln.
- **Standard**: över gångs data till bästa åtkomst nivå och förfaller data i slutet av livs cykeln.

Mer information finns i [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md).

Du kan inte flytta data som lagras i ett Premium Block-Blob Storage-konto mellan frekventa, låg frekventa och Arkiv lag rings nivåer. Du kan dock kopiera blobbar från ett Block Blob Storage-konto till frekvent åtkomst nivå i ett *annat* konto. Om du vill kopiera data till ett annat konto använder du alternativet för att [blockera från URL](/rest/api/storageservices/put-block-from-url) -API eller [AzCopy v10](../common/storage-use-azcopy-v10.md). Funktionen för att **blockera från URL** : er kopierar synkront data på servern. Anropet slutförs först efter att alla data har flyttats från den ursprungliga Server platsen till mål platsen.

## <a name="next-steps"></a>Nästa steg

Utvärdera frekvent, låg frekvent och Arkiv lag ring i GPv2-och Blob Storage-konton.

- [Lär dig mer om återuppväcks BLOB-data från Arkiv lag rings nivån](storage-blob-rehydration.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](./monitor-blob-storage.md)
- [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
