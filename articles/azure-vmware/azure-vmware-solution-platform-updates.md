---
title: Plattforms uppdateringar för Azure VMware-lösning
description: Lär dig mer om plattforms uppdateringar för Azure VMware-lösningen.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 73bd1d088f9055ebd80a28c6247ea9dfa6229093
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586241"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Plattforms uppdateringar för Azure VMware-lösning

Viktiga uppdateringar av Azure VMware-lösningen tillämpas från och med 2021 mars. Du får ett meddelande via Azure Service Health som innehåller tids linjen för underhållet. Mer information om de viktigaste uppgraderings processerna och funktionerna i Azure VMware-lösningen finns i [Azure VMware-lösningar privata moln uppdateringar och uppgraderingar](concepts-upgrades.md).

## <a name="march-15-2021"></a>15 mars 2021 

- Azure VMware Solution service utför underhålls arbetet till den 19 mars 20201, för att uppdatera vCenter-Server i ditt privata moln till vCenter Server 6,7 uppdatering 3L-version.

- Under den här tiden är VMware vCenter otillgänglig och du kan inte hantera virtuella datorer (stoppa, starta, skapa, ta bort). Skalning av privata moln (tillägg/borttagning av servrar och kluster) kommer inte heller att vara tillgängligt. VMware hög tillgänglighet (HA) fortsätter att fungera för att ge skydd för befintliga virtuella datorer. 
 
Mer information om den här vCenter-versionen finns [VMware vCenter Server 6,7 Update 3L viktig information](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 mars 2021

- Azure VMware-lösningar kommer att tillämpa korrigeringarna till och med 15 mars 2021 för att ESXi i befintliga privata moln till [VMware ESXi 6,7, patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Dokumenterade lösningar för vSphere-stacken, som per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), kommer också att gälla till och med 15 mars 2021.

>[!NOTE]
>Detta är ett avbrott som inte påverkar Azure VMware-tjänster eller arbets belastningar. Under underhållet visas olika VMware-aviseringar, till exempel _förlorad nätverks anslutning på DVPorts_ och _förlorad överordnad överordnad länk på DVPorts_, i vCenter och rensas automatiskt under underhålls förloppet.

## <a name="post-update"></a>Publicera uppdatering
När du är klar visas nyare versioner av VMware-komponenter. Om du upptäcker några problem eller har frågor kan du kontakta vårt support team genom att öppna ett support ärende.





