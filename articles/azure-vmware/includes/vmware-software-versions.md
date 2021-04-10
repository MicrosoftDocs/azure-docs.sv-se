---
title: Versioner av VMware-programvara
description: Versioner av VMware-programvara som stöds för Azure VMware-lösning.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462530"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


De VMware-programversioner som används i nya distributioner av Azure VMware-lösningar privata moln-kluster är:

| Programvara              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Virtuellt San                  |    6,7 U3    |
| NSX-T <br />**Obs:** NSX-T är den enda version av NSX som stöds.               |      2.5     |


Nya kluster läggs till i ett befintligt privat moln, den program varu version som körs för tillfället tillämpas. Mer information finns i [versions kraven för VMware-programvaran](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Den privata moln program varu paketets uppgraderingar behåller program varan i en version av den senaste versionen av program varu paket från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN). Du hittar de allmänna uppgraderings principerna och processerna för Azure VMware Solution Platform-programvaran som beskrivs i [privata moln uppdateringar och uppgraderingar](../concepts-upgrades.md).

