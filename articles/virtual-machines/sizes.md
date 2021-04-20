---
title: VM-storlekar
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella datorer i Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: f2a4083e8cbbdbfbf971c441d8942b48e712c472
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740036"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Storlekar för virtuella datorer i Azure

I den här artikeln beskrivs tillgängliga storlekar och alternativ för de virtuella Azure-datorer som du kan använda för att köra dina appar och arbetsbelastningar. Den innehåller också distributionsöverväganden som du bör känna till när du planerar att använda dessa resurser. 

:::image type="content" source="media/sizes/azurevmsthumb.jpg" alt-text="YouTube-video för att välja rätt storlek för den virtuella datorn." link="https://youtu.be/zOSvnJFd3ZM":::

| Typ | Storlekar | Beskrivning |
|------|-------|-------------|
| [Generell användning](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Balanserat förhållande mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](sizes-compute.md) | F, Fs, Fsv2 | Högt förhållande mellan processor och minne. Bra för webbservrar med medelhög trafik, nätverksutrustning, batchprocesser och programservrar. |
| [Minnesoptimerad](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Högt förhållande mellan minne och processor. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](sizes-storage.md) | Lsv2 | Högt diskgenomflöde och I/O passar perfekt för stordata, SQL, NoSQL-databaser, informationslager och stora transaktionsdatabaser.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | Specialiserade virtuella datorer som är avsedda för tung grafikrendering och videoredigering, samt modellträning och inferens (ND) med djupinlärning. Tillgängligt med en eller flera grafikprocessorer. |
| [Databehandling med höga prestanda](sizes-hpc.md) | HB, HBv2, HC, H | Våra snabbaste och mest kraftfulla virtuella CPU-datorer med valfria nätverksgränssnitt med högt dataflöde (RDMA). |

- Information om priser för de olika storlekarna finns på prissättningssidorna för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) eller [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)
- Information om tillgänglighet för VM-storlekar i Azure-regioner finns i [Produkttillgänglighet per region.](https://azure.microsoft.com/regions/services/)
- Allmänna begränsningar för virtuella Azure-datorer finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Mer information om hur Azure namnger sina virtuella datorer finns i [Namngivningskonventioner för storlekar på virtuella Azure-datorer.](./vm-naming-conventions.md)

## <a name="rest-api"></a>REST-API

Information om hur du använder REST API för att fråga efter VM-storlekar finns i följande:

- [Lista tillgängliga storlekar för virtuella datorer för storleksändring](/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](/rest/api/compute/resourceskus/list)
- [Lista tillgängliga storlekar för virtuella datorer i en tillgänglighetsuppsättning](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Läs mer om hur [Azure-beräkningsenheter (ACU) kan hjälpa](acu.md) dig att jämföra beräkningsprestanda mellan Azure-SKU:er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Läs mer om beräkningsprestanda för virtuella Linux-datorer med [benchmark-poäng för CoreMark.](./linux/compute-benchmark-scores.md)

Läs mer om beräkningsprestanda för virtuella Windows-datorer med hjälp av [BENCHMARK-poäng för SPECInt.](./windows/compute-benchmark-scores.md)

## <a name="manage-costs"></a>Hantera kostnader

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika VM-storlekar som är tillgängliga:

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- Kontrollera sidan [Föregående generation](sizes-previous-gen.md) för serien A Standard, Dv1 (D1-4 och D11-14 v1) och A8-A11
