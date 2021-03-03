---
title: NFS 3,0-prestanda överväganden i Azure Blob Storage (för hands version) | Microsoft Docs
description: Optimera prestanda för dina lagrings begär anden i Network File System (NFS) 3,0 med hjälp av rekommendationerna i den här artikeln.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: f8a780afba1f5703fbe457e113ed1b455f1e9b64
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746411"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Network File System (NFS) 3,0 prestanda överväganden i Azure Blob Storage (för hands version)

Blob Storage stöder nu NFS-protokollet (Network File System) 3,0. Den här artikeln innehåller rekommendationer som hjälper dig att optimera prestanda för dina lagrings begär Anden. Om du vill veta mer om NFS 3,0-stöd i Azure Blob Storage, se [Network File System (NFS) 3,0 protokoll stöd i Azure Blob Storage (för hands version)](network-file-system-protocol-support.md).

> [!NOTE]
> NFS 3,0 protokoll stöd i Azure Blob Storage finns i offentlig för hands version. Det stöder GPV2-lagrings konton med standard-nivå prestanda i följande regioner: östra Australien, Korea Central och södra centrala USA. För hands versionen stöder också Block-Blob med Premium prestanda nivå i alla offentliga regioner.

## <a name="add-clients-to-increase-throughput"></a>Lägga till klienter för att öka data flödet 

Azure Blob Storage skalas linjärt tills det når den maximala gränsen för utgående lagrings konto och inkommande trafik. Därför kan dina program uppnå högre genomflöde genom att använda fler klienter.  Om du vill visa utgående och ingångs gränser för lagrings kontot läser du [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md).

Följande diagram visar hur bandbredden ökar när du lägger till fler klienter. I det här diagrammet är en klient en virtuell dator (VM) och kontot använder standard prestanda nivån. 

> [!div class="mx-imgBorder"]
> ![Standard prestanda](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Följande diagram visar samma effekt när den tillämpas på ett konto som använder Premium Performance-nivån.

> [!div class="mx-imgBorder"]
> ![Standard prestanda](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Använd Premium Performance-nivå för små skalnings program

Det är inte alla program som kan skalas upp genom att lägga till fler klienter. För dessa program erbjuder [Azure Premium Block Blob Storage-kontot](storage-blob-create-account-block-blob.md) konsekvent låg latens och höga transaktions priser. Premium Block-Blob Storage-kontot kan uppnå maximal bandbredd med färre trådar och klienter. Till exempel, med en enda klient, kan ett Blob Storage-konto för Premium-block uppnå **2,3 x** bandbredd jämfört med samma konfiguration som används med ett standard prestanda för generell användning v2-lagrings konto. 

Varje stapel i följande diagram visar skillnaden i uppnådd bandbredd mellan lagrings konton för Premium-och standard-prestanda. När antalet klienter ökar, minskar skillnaden.  

> [!div class="mx-imgBorder"]
> ![Relativa prestanda](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-date"></a>Undvik frekvent överskrivning av datum

Det tar längre tid att slutföra en överskrivnings åtgärd än en ny Skriv åtgärd. Det beror på att en skrivskyddad NFS-åtgärd, särskilt en delvis fil redigering på plats, är en kombination av flera underliggande BLOB-åtgärder: en läsning, en ändring och en Skriv åtgärd. Därför passar inte ett program som kräver frekventa ändringar på plats för NFS-aktiverade Blob Storage-konton. 

## <a name="other-best-practice-recommendations"></a>Andra rekommendationer för bästa praxis 

- Använd virtuella datorer med tillräckligt nätverks bandbredd.

- Använd flera monterings punkter när dina arbets belastningar tillåter det.

- Använd så många trådar som möjligt.

- Använd stora block storlekar.

- Gör lagrings begär Anden från en klient som finns i samma region som lagrings kontot. Detta kan förbättra nätverks fördröjningen.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om NFS 3,0-stöd i Azure Blob Storage, se [Network File System (NFS) 3,0 protokoll stöd i Azure Blob Storage (för hands version)](network-file-system-protocol-support.md).

- För att komma igång, se [montera Blob Storage med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)](network-file-system-protocol-support-how-to.md).
