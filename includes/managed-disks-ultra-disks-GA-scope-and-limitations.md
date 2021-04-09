---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aafeb7ebd939805ae18e29670f359660db12e2e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555895"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

De enda alternativen för infrastruktur redundans som för närvarande är tillgängliga för Ultra disks är tillgänglighets zoner. Virtuella datorer som använder andra alternativ för redundans kan inte ansluta en Ultra disk.

Följande tabell beskriver regionerna Ultra disks finns i, samt motsvarande tillgänglighets alternativ:

> [!NOTE]
> Om en region i följande lista inte har några tillgänglighets zoner med Ultra disk-kapacitet måste de virtuella datorerna i regionen distribueras utan några alternativ för replikering av infrastrukturen för att ansluta en Ultra disk.

|Regioner  |Alternativ för redundans  |
|---------|---------|
|Brasilien, södra     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|Indien, centrala     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|Asien, östra     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|Tyskland, västra centrala     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|Sydkorea, centrala     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|USA, södra centrala    |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|US Gov, Arizona     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|US Gov, Virginia     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|US Gov, Texas     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|USA, västra     |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)        |
|Australien, centrala    |Endast enskilda virtuella datorer (tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer stöds inte)|
|Australien, östra     |Tre tillgänglighets zoner         |
|Sydostasien    |Tre tillgänglighets zoner        |
|Kanada, centrala     |Tre tillgänglighets zoner          |
|Central US     |Tre tillgänglighets zoner          |
|East US     |Tre tillgänglighets zoner          |
|USA, östra 2     |Tre tillgänglighets zoner         |
|Frankrike, centrala    |Två tillgänglighets zoner        |
|Japan, östra    |Tre tillgänglighets zoner        |
|Europa, norra    |Tre tillgänglighets zoner        |
|Storbritannien, södra    |Tre tillgänglighets zoner        |
|Europa, västra    | Tre tillgänglighets zoner|
|USA, västra 2    |Tre tillgänglighets zoner|

- Stöds endast i följande VM-serien:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [Avstånd](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla VM-storlekar är inte tillgängliga i alla regioner som stöds med Ultra disks.
- Är bara tillgängliga som data diskar. 
- Stöd för fysisk sektor storlek på 4K som standard. 512E sektor storlek är tillgänglig som ett allmänt tillgängligt erbjudande (ingen registrering krävs). De flesta program är kompatibla med storleken på 4K-sektorn men vissa kräver 512 byte sektor storlek. Ett exempel skulle vara Oracle Database, vilket kräver version 12,2 eller senare för att kunna stödja de 4K-inhemska diskarna. För äldre versioner av Oracle DB krävs en sektor storlek på 512 byte.
- Kan bara skapas som tomma diskar.
- Stöder för närvarande inte disk ögonblicks bilder, VM-avbildningar, tillgänglighets uppsättningar, Azure-dedikerade värdar eller Azure Disk Encryption.
- Stöder för närvarande inte integrering med Azure Backup eller Azure Site Recovery.
- Har endast stöd för icke-cachelagrade läsningar och icke-cachelagrade skrivningar.
- Den aktuella maximala gränsen för IOPS på GA-VM: ar är 80 000.

Azure Ultra disks erbjuder upp till 32 TiB per region per prenumeration som standard, men Ultra disks stöder högre kapacitet på begäran. Om du vill begära en ökning av kapaciteten, begär en kvot ökning eller kontakta Azure-supporten.
