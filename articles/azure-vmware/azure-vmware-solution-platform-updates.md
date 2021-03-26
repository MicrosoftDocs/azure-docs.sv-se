---
title: Plattforms uppdateringar för Azure VMware-lösning
description: Lär dig mer om plattforms uppdateringar för Azure VMware-lösningen.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045220"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Plattforms uppdateringar för Azure VMware-lösning

Azure VMware-lösningen kommer att tillämpa viktiga uppdateringar som börjar i mars 2021. Du får ett meddelande via Azure Service Health som innehåller tids linjen för underhållet. Mer information finns i [Azure VMware-lösningar privata moln uppdateringar och uppgraderingar](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 mars 2021
Alla nya privata moln i Azure VMware-lösningen distribueras med VMware vCenter version 6.7 U3l och NSX-T version 3.1.1. Alla befintliga privata moln kommer att uppdateras och uppgraderas till och med **juni 2021** till ovan nämnda versioner.

Du får ett e-postmeddelande med planerat underhålls datum och tid. Du kan schemalägga om en uppgradering. E-postmeddelandet innehåller också information om den uppgraderade komponenten, dess påverkan på arbets belastningar, privat moln åtkomst och andra Azure-tjänster.  En timme före uppgraderingen får du ett meddelande och sedan igen när det är klart.

## <a name="march-15-2021"></a>15 mars 2021 

- Azure VMware Solution service sköter underhålls arbetet **till den 19 mars 2021,** för att uppdatera vCenter-servern i ditt privata moln till vCenter Server 6,7 uppdatering 3L-version.

- VMware vCenter kommer inte att vara tillgängligt under den här tiden.  Därför kan du inte hantera dina virtuella datorer (stoppa, starta, skapa, ta bort) eller skalning av privata moln (lägga till/ta bort servrar och kluster). Men VMware hög tillgänglighet (HA) fortsätter att använda för att skydda befintliga virtuella datorer. 
 
Mer information om den här vCenter-versionen finns [VMware vCenter Server 6,7 Update 3L viktig information](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 mars 2021

- Azure VMware-lösningen tillämpar [VMware ESXi 6,7, korrigerings version ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) för befintliga privata användare **till och med 15 mars 2021**.

- Dokumenterade lösningar för vSphere-stacken, som per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), kommer också att gälla **till och med 15 mars 2021**.

>[!NOTE]
>Detta är ett avbrott som inte påverkar Azure VMware-tjänster eller arbets belastningar. Under underhållet visas olika VMware-aviseringar, till exempel _förlorad nätverks anslutning på DVPorts_ och _förlorad överordnad överordnad länk på DVPorts_, i vCenter och rensas automatiskt under underhålls förloppet.

## <a name="post-update"></a>Publicera uppdatering
När du är klar visas nyare versioner av VMware-komponenter. Om du upptäcker några problem eller har frågor kan du kontakta vårt support team genom att öppna ett support ärende.