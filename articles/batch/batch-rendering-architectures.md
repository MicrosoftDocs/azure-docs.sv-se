---
title: Referens arkitekturer för Azure rendering
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka en lokal rendering-servergrupp genom burst-överföring till molnet
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83726527"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referens arkitekturer för Azure-åter givning

Den här artikeln visar arkitektur diagram på hög nivå för scenarier som kan utökas eller "burst", en lokal åter givnings Server grupp till Azure. I exemplen visas olika alternativ för Azure Compute, nätverk och lagrings tjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

Följande diagram visar ett hybrid scenario som innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagrings** -och utdatafiler: NFS eller CFS med virtuella Azure-datorer, synkroniserade med lokal lagring via Azure File Sync eller RSync. Alternativt kan du: aver vFXT till indata-eller utdatafiler från lokala NAS-enheter med hjälp av NFS.

  ![Cloud bursting – hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med Blobfuse

Följande diagram visar ett hybrid scenario som innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagrings** -och utdatafiler: Blob Storage, monteras på beräknings resurser via Azure Blobfuse.

  ![Cloud bursting – hybrid med Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt kopplat hybrid scenario för både beräkning och lagring och innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagring** – mellan platser: aver vFXT. Valfri arkivering av lokala filer via Azure Data Box till Blob Storage eller lokalt AVERT FXT för NAS-acceleration.

  ![Cloud burst – hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du använder [åter givnings hanterare](batch-rendering-render-managers.md) med Azure Batch.

* Lär dig mer om alternativ för [åter givning i Azure](batch-rendering-service.md).