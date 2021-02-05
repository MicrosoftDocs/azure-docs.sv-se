---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och virtuella Azure-datorer.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576201"
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
