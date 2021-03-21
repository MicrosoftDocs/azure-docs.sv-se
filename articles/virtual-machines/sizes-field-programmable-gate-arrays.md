---
title: Storlekar för virtuella Azure-datorer för fält-programmerbara grindar (FPGA)
description: Visar en lista över olika FPGA-optimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557683"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA optimerade storlekar för virtuella datorer

FPGA-optimerade VM-storlekar är specialiserade virtuella datorer som är tillgängliga med en eller flera FPGAs. Dessa storlekar är utformade för beräknings intensiva arbets belastningar. Den här artikeln innehåller information om antalet och typen av FPGAs, virtuella processorer, data diskar och nätverkskort. Lagrings data flöde och nätverks bandbredd ingår också för varje storlek i grupperingen.

- Storleken på [NP-serien](np-series.md) är optimerad för arbets belastningar, inklusive maskin inlärnings störningar, video omkodning och databas sökning & analys. NP-serien drivs av Xilinx U250-acceleratorer.


## <a name="deployment-considerations"></a>Distributionsöverväganden

- Tillgänglighet för virtuella datorer i N-serien finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

- Virtuella datorer i N-serien kan bara distribueras i distributions modellen för Resource Manager.

- Om du vill distribuera fler än några få virtuella datorer i N-serien bör du överväga att betala per användning-prenumeration eller andra köp alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

- Du kan behöva öka kvoten för kärnor (per region) i din Azure-prenumeration och öka den separata kvoten för NP-kärnor. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. Standard gränserna kan variera beroende på din prenumerations kategori.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [GPU-accelererad beräkning](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
