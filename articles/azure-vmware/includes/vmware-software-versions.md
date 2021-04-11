---
title: Versioner av VMware-programvara
description: Versioner av VMware-programvara som stöds för Azure VMware-lösning.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097035"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


De VMware-programversioner som används i nya distributioner av Azure VMware-lösningar privata moln-kluster är:

| Programvara              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3l    | 
| ESXi                  |    6,7 U3l    | 
| Virtuellt San                  |    6,7 U3l    |
| NSX-T <br />**Obs:** NSX-T är den enda version av NSX som stöds.               |      3.1.1     |


Nya kluster läggs till i ett befintligt privat moln, den program varu version som körs för tillfället tillämpas. Mer information finns i [versions kraven för VMware-programvaran](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Den privata moln program varu paketets uppgraderingar behåller program varan i en version av den senaste versionen av program varu paket från VMware. Program varu versionerna för det privata molnet kan skilja sig från de senaste versionerna av de enskilda program varu komponenterna (ESXi, NSX-T, vCenter, virtuellt SAN). Du hittar de allmänna uppgraderings principerna och processerna för Azure VMware Solution Platform-programvaran som beskrivs i [privata moln uppdateringar och uppgraderingar](../concepts-upgrades.md).

