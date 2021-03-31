---
title: Storlekar på virtuella Azure-datorer – lagring | Microsoft Docs
description: Visar en lista över de olika optimerings storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d97fe6cda1134d45468e257965fd5e28fe170e6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561032"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Optimera storlekar för virtuella datorer i Storage

Storage-optimerade VM-storlekar erbjuder hög disk data flöde och IO och är idealiska för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser.  Exempel är Cassandra, MongoDB, Cloudera och Redis. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lokalt lagrings data flöde och nätverks bandbredd för varje optimerad storlek.

[Lsv2-serien](lsv2-series.md) har högt data flöde, låg latens, direkt mappad lokal NVMe-lagring som körs på [AMD EPYC<sup>TM</sup> 7551-processorn](https://www.amd.com/en/products/epyc-7000-series) med en större ökning på 2.55 GHz och en maximal förstärkning på 3,0 GHz. De virtuella datorerna i Lsv2-serien har storlekar från 8 till 80 vCPU i en samtidig multi-threading-konfiguration.  Det finns 8 GiB minne per vCPU och en 1.92 TB NVMe SSD M. 2-enhet per 8 virtuella processorer, med upp till 19,2 TB (10X 1.92 TB) tillgängligt på L80s v2.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

Lär dig hur du optimerar prestandan för virtuella datorer i Lsv2-serien för [Windows](windows/storage-performance.md) eller [Linux](linux/storage-performance.md).

Mer information om hur Azure namnger sina virtuella datorer finns i [namngivnings konventioner för virtuella datorer i Azure](./vm-naming-conventions.md).