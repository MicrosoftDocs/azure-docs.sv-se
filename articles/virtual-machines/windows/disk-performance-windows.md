---
title: Virtuell dator och disk prestanda – Windows
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar tillsammans med prestanda i Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584127"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Virtuell dator och disk prestanda (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
 Virtuella datorer som har både Premium Storage aktiverat och Premium Storage-cachelagring aktiverat har två olika begränsningar för lagrings bandbredd. Nu ska vi fortsätta att titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](../dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Nu ska vi köra ett benchmarking-test på denna VM-och disk kombination som gör att du kan skapa IO-aktivitet och lära dig allt om hur du kan mäta lagrings-i/o på Azure [här](disks-benchmarks.md). Från benchmarking-verktyget kan du se att kombinationen av virtuell dator och disk kan uppnå 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]