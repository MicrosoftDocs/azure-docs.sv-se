---
title: Prestanda för virtuella datorer och diskar
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar i kombination för prestanda.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540763"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestanda för virtuella datorer och diskar
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
Virtuella datorer som är aktiverade för både Premium Storage och Premium Storage-cachelagring har två olika begränsningar för lagrings bandbredd. Nu ska vi titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Nu ska vi köra ett benchmarking-test på den här virtuella datorn och den disk kombination som skapar IO-aktivitet. Information om hur du kan mäta lagrings-i/o i Azure finns i avsnittet [om att mäta ditt program på Azure-disklagring](disks-benchmarks.md). Från benchmarking-verktyget kan du se att kombinationen av virtuell dator och disk kan uppnå 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
