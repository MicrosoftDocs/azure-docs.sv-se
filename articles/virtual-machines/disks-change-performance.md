---
title: Prestanda nivåer för Azure Managed disks
description: Lär dig mer om prestanda nivåer för hanterade diskar.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677317"
---
# <a name="performance-tiers-for-managed-disks"></a>Prestanda nivåer för hanterade diskar

Prestanda för din Azure-hanterade disk anges när du skapar disken i form av prestanda nivån. Prestanda nivån avgör den IOPS och det data flöde som din hanterade disk har. När du ställer in den allokerade storleken på disken väljs automatiskt en prestanda nivå. Prestanda nivån kan ändras vid distribution eller efteråt, utan att storleken på disken ändras.

Genom att ändra prestanda nivån kan du förbereda för och möta högre efter frågan utan att använda hård diskens burst-kapacitet. Det kan vara mer kostnads effektivt att ändra prestanda nivån i stället för att förlita dig på bursting, beroende på hur länge ytterligare prestanda är nödvändig. Detta är idealiskt för händelser som tillfälligt kräver en konsekvent högre prestanda nivå, till exempel helgdags köp, prestandatester eller att köra en tränings miljö. Om du vill hantera dessa händelser kan du använda en högre prestanda nivå så länge du behöver den. Du kan sedan gå tillbaka till den ursprungliga nivån när du inte längre behöver den ytterligare prestandan.

## <a name="how-it-works"></a>Så här fungerar det

När du först distribuerar eller etablerar en disk, anges bas linje prestanda nivån för den disken baserat på den allokerade disk storleken. Du kan använda en högre prestanda nivå än den ursprungliga bas linjen för att uppfylla högre efter frågan. När du inte längre behöver den prestanda nivån kan du återgå till den första prestanda nivån för bas linjen.

Dina fakturerings ändringar när prestanda nivån ändras. Om du till exempel etablerar en P10-disk (128 GiB), anges din bas linje prestanda nivå som P10 (500 IOPS och 100 Mbit/s). Du debiteras enligt P10-priset. Du kan uppgradera nivån så att den matchar prestandan för P50 (7 500 IOPS och 250 Mbit/s) utan att öka disk storleken. Under uppgraderingen debiteras du enligt P50-priset. När du inte längre behöver högre prestanda kan du gå tillbaka till P10-nivån. Disken kommer återigen att debiteras enligt P10-priset.

| Diskstorlek | Bas linje prestanda nivå | Kan uppgraderas till |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Inget |
| 8 TiB | p60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Inget |

Information om fakturering finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du ändrar prestanda nivån finns i artiklar om [Portal](disks-performance-tiers-portal.md) eller [PowerShell/CLI](disks-performance-tiers.md) .

