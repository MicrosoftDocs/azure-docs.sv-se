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
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89420225"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Storlekar för virtuella datorer i Azure

I den här artikeln beskrivs tillgängliga storlekar och alternativ för de virtuella Azure-datorer som du kan använda för att köra appar och arbets belastningar. Den innehåller också distributions överväganden för att vara medveten om när du planerar att använda dessa resurser. 

| Typ | Storlekar | Beskrivning |
|------|-------|-------------|
| [Generell användning](sizes-general.md)   | B, Dsv3, dv3, Dasv4, Dav4, DSv2, Dv2, AV2, DC, DCv2, DV4, Dsv4, Ddv4, Ddsv4  | Balanserat förhållande mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](sizes-compute.md) | F, FS, Fsv2 | Högt förhållande mellan processor och minne. Passar för medel stora trafik webb servrar, nätverks enheter, batch-processer och program servrar. |
| [Minnesoptimerad](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Högt förhållande mellan minne och processor. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](sizes-storage.md) | Lsv2 | Hög disk data flöde och i/o perfekt för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3 (för hands version), ND, NDv2 (för hands version), NV, NVv3, NVv4 | Specialiserade virtuella datorer som är riktade mot tung grafisk rendering och video redigering, samt modell utbildning och inferencing (ND) med djup inlärning. Tillgängligt med en eller flera grafikprocessorer. |
| [Databehandling med höga prestanda](sizes-hpc.md) | HB, HBv2, HC, H | Våra snabbaste och mest kraftfulla processor virtuella datorer med valfria nätverks gränssnitt (RDMA) med höga data flöden. |

- Information om priser för de olika storlekarna finns på sidan med pris information för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows).
- För tillgänglighet för VM-storlekar i Azure-regioner, se [produkter tillgängliga per region](https://azure.microsoft.com/regions/services/).
- Om du vill se allmänna gränser för virtuella Azure-datorer kan du läsa om [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Mer information om hur Azure namnger sina virtuella datorer finns i [namngivnings konventioner för virtuella datorer i Azure](./vm-naming-conventions.md).

## <a name="rest-api"></a>REST-API

Information om hur du använder REST API för att fråga efter VM-storlekar finns i följande avsnitt:

- [Lista tillgängliga storlekar för virtuella datorer för storleks ändring](/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](/rest/api/compute/resourceskus/list)
- [Visa en lista över tillgängliga storlekar för virtuella datorer i en tillgänglighets uppsättning](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

## <a name="benchmark-scores"></a>Benchmark-poäng

Lär dig mer om beräknings prestanda för virtuella Linux-datorer med [CoreMark-benchmark-resultat](./linux/compute-benchmark-scores.md).

Lär dig mer om beräknings prestanda för virtuella Windows-datorer med [SPECInt-benchmark-resultat](./windows/compute-benchmark-scores.md).

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
- Kontrol lera [föregående generations](sizes-previous-gen.md) sida för en standard, DV1 (D1-4 och D11-14 v1) och A8-A11-serien
