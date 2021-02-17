---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och virtuella Azure-datorer.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585061"
---
# <a name="managed-disk-bursting"></a>Hanterad disk-bursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Burst-överföring på virtuell dator har Aktiver ATS för följande VM-serier i alla regioner som de stöds i:
- [Lsv2-serien](lsv2-series.md)
- [Dsv3-serien](dv3-dsv3-series.md)
- [Esv3-serien](ev3-esv3-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vårt [Premium-SSD](disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner i Azures offentliga, offentliga myndigheter och i Kina. Disk-burst är aktiverat som standard på alla nya och befintliga distributioner av disk storlekarna som stöder det. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du får inblick i dina burst-resurser finns i [mått för disk burst](disks-metrics.md).
