---
title: NP-serien – Azure Virtual Machines
description: Specifikationer för de virtuella datorerna i NP-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 2606881cec5527084c3c2788d40f8bdfd8abf5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418044"
---
# <a name="np-series-preview"></a>NP-serien (för hands version)

De virtuella datorerna i NP-serien drivs av [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) -FPGAs för att påskynda arbets belastningar, inklusive maskin inlärnings störningar, video omkodning och databas sökning & analys. De virtuella datorerna i NP-serien drivs med Intel Xeon 8171M-processorer (Skylake) med all Core Turbo klock hastighet på 3,2 GHz.


[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
Stöd för VM-generering: generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | FPGA | FPGA-minne: GiB | Maximalt antal datadiskar | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) | 
|---|---|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds
Besök [Xilinx Runtime (XRT) viktig information](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) för att få en fullständig lista över operativ system som stöds.

Under för hands versions programmet Microsoft Azure teknik team delar vissa instruktioner för driv rutins installation.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
