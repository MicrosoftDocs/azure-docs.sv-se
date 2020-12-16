---
title: NCas T4 v3-serien
description: Specifikationer för virtuella datorer i NCas T4 v3-serien.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 2de6cceb1e3b85060a146b18a689b57573bc932e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585436"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3-serien (för hands version) 

De virtuella datorerna i NCasT4_v3 serien drivs av [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU-och AMD EPYC 7V12-processorer (Rom). De virtuella datorerna har upp till 4 NVIDIA T4-GPU: er med 16 GB minne vardera, upp till 64 non-multitråded AMD EPYC 7V12 (Roma) processor kärnor och 440 GiB av system minnet. De här virtuella datorerna är idealiska för att distribuera AI-tjänster, till exempel real tids inferencing av användare som genererar förfrågningar, eller för interaktiva arbets belastningar för grafik och visualiseringar med hjälp av NVIDIA: s driv rutin och virtuell GPU-teknik. Standard arbets belastningar för GPU-beräkning som baseras på CUDA, TensorRT, caffe, ONNX och andra ramverk, eller GPU-accelererade grafiska program baserade OpenGL och DirectX kan distribueras ekonomiskt, med nära till användare, i NCasT4_v3 serien.

> [!NOTe]
> [Skicka en begäran](https://aka.ms/NCT4v3Preview) som ska ingå i för hands versions programmet.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
Accelererat nätverk: stöds<br>
NVIDIA NVLink-Interconnect: stöds inte<br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4/8 000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i Azure NCasT4_v3-seriens virtuella datorer som kör Windows eller Linux måste nVidia GPU-drivrutiner vara installerade.

Om du vill installera nVidia GPU-drivrutiner manuellt, se [N-seriens installation av GPU-drivrutiner för Windows](./windows/n-series-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
