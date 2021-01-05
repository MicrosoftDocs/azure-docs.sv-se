---
title: Versioner av VMware-programvara
description: Versioner av VMware-programvara som stöds för Azure VMware-lösning.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825078"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


De aktuella program varu versionerna av VMware-programvaran som används i Azure VMware-lösningar privata moln kluster är:

| Programvara              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Virtuellt San                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T är den enda version av NSX som stöds.

För alla nya kluster i ett privat moln, matchar program varu versionen vad som körs för tillfället. Program varu stackens senaste version installeras för alla nya privata moln i en prenumeration. Mer information finns i [versions kraven för VMware-programvaran](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Den privata moln program varu paketets uppgraderingar behåller program varan i en version av den senaste versionen av program varu paket från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN). Du hittar de allmänna uppgraderings principerna och processerna för Azure VMware Solution Platform-programvaran som beskrivs i [privata moln uppdateringar och uppgraderingar](../concepts-upgrades.md).

