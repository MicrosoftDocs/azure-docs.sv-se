---
title: Matris för kompatibilitet med operativ system för SAP HANA (stora instanser) | Microsoft Docs
description: Kompatibilitetsrapporten representerar kompatibiliteten för olika versioner av operativ systemet med olika maskin varu typer (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccfdffc4e488de7f3cecb150305596743b3a9e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675410"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibla operativ system för HANA-stora instanser

## <a name="hana-large-instance-type-i"></a>HANA-stor instans typ I     
  | Operativsystem | Tillgänglighet        | SKU:er                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Tillhandahålls inte längre | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Tillgänglig           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Tillgänglig           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | Tillgänglig           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Tillgänglig           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7,6         | Tillgänglig           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Beständiga minnes-SKU: er
  | Operativsystem | Tillgänglighet | SKU:er                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Tillgänglig    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA, stor instans typ II     
  |  Operativsystem       | Tillgänglighet        | SKU:er                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | Tillhandahålls inte längre | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP3             | Tillgänglig           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP4             | Tillgänglig           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP5             | Tillgänglig           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Tillgänglig           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | RHEL 7,6                | Tillgänglig           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |

## <a name="related-documents"></a>Relaterade dokument

- Om du vill veta mer om [tillgängliga SKU: er](hana-available-skus.md)
- Information om hur [du uppgraderar operativ systemet](os-upgrade-hana-large-instance.md)
  

  
  
