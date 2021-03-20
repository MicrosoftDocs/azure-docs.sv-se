---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518088"
---
Den här artikeln hjälper till att klargöra disk prestanda och hur det fungerar när du kombinerar Azure Virtual Machines och Azure-diskar. Det beskriver också hur du kan diagnostisera Flask halsar för disk-i/o och de ändringar som du kan göra för att optimera prestanda.

## <a name="how-does-disk-performance-work"></a>Hur fungerar disk prestanda?
Virtuella Azure-datorer har indata/utdata-åtgärder per sekund (IOPS) och data flödes prestanda gränser baserat på den virtuella datorns typ och storlek. OS-diskar och data diskar kan kopplas till virtuella datorer. Diskarna har sina egna värden för IOPS och data flöde.

Programmets prestanda får en gräns när den begär mer IOPS eller data flöde än vad som tilldelas virtuella datorer eller anslutna diskar. När det är ett tak, är program upplevelsens optimala prestanda. Detta kan leda till negativa följder som ökad latens. Nu ska vi gå igenom ett par exempel för att klargöra det här konceptet. För att göra dessa exempel lätta att följa, tittar vi bara på IOPS. Men samma logik gäller för data flödet.

## <a name="disk-io-capping"></a>Disk-i/o-capping

**Installationsfilerna**

- Standard_D8s_v3
  - Ej cachelagrad IOPS: 12 800
- E30 OS-disk
  - IOPS: 500
- Två E30 data diskar × 2
  - IOPS: 500

![Diagram över capping på disk nivå.](media/vm-disk-performance/disk-level-throttling.jpg)

Programmet som körs på den virtuella datorn gör en begäran som kräver 10 000 IOPS på den virtuella datorn. Alla tillåts av den virtuella datorn eftersom den Standard_D8s_v3 virtuella datorn kan köra upp till 12 800 IOPS.

10 000 IOPS-begäranden delas upp i tre olika begär anden till de olika diskarna:

- 1 000 IOPS begärs till operativ system disken.
- 4 500 IOPS begärs till varje datadisk.

Alla anslutna diskar är E30 diskar och kan bara hantera 500 IOPS. De svarar så att de svarar med 500 IOPS var. Programmets prestanda är ett tak av de anslutna diskarna och kan bara bearbeta 1 500 IOPS. Programmet kan arbeta med högsta prestanda vid 10 000 IOPS om bättre diskar används, till exempel Premium SSD P30-diskar.

## <a name="virtual-machine-io-capping"></a>IO-capping för virtuell dator

**Installationsfilerna**

- Standard_D8s_v3
  - Ej cachelagrad IOPS: 12 800
- P30 OS-disk
  - IOPS: 5 000
- Två P30 data diskar × 2
  - IOPS: 5 000

![Diagram över capping på virtuell dator nivå.](media/vm-disk-performance/vm-level-throttling.jpg)

Programmet som körs på den virtuella datorn gör en begäran som kräver 15 000 IOPS. Tyvärr är den Standard_D8s_v3 virtuella datorn endast etablerad för att hantera 12 800 IOPS. Programmet är begränsat till den virtuella datorns gränser och måste allokera 12 800 IOPS.

De 12 800 IOPS-begär Anden delas upp i tre olika begär anden till de olika diskarna:

- 4 267 IOPS begärs till operativ system disken.
- 4 266 IOPS begärs till varje datadisk.

Alla anslutna diskar är P30 diskar som kan hantera 5 000 IOPS. De svarar därmed med de begärda beloppen.
