---
title: Prestanda för virtuella datorer och diskar
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar i kombination för prestanda.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d3a89e7733cc033792056b8de5232232b8327025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580401"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestanda för virtuella datorer och diskar
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
Virtuella datorer som är aktiverade för både Premium Storage och Premium Storage-cachelagring har två olika begränsningar för lagrings bandbredd. Nu ska vi titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

