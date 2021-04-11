---
title: Koncept – uppdateringar och uppgraderingar av privata moln
description: Lär dig mer om de viktiga uppgraderings processerna och funktionerna i Azure VMware-lösningen.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586156"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Uppdateringar och uppgraderingar av privata moln i Azure VMware-lösningar

En fördel med privata moln i Azure VMware-lösningar är att plattformen upprätthålls för dig. Underhåll inkluderar automatiserade uppdateringar av en VMware-verifierad program varu samling för att se till att du använder den senaste versionen av Azure VMware-lösningen privat moln program vara.

Mer specifikt innehåller ett privat moln i Azure VMware-lösningen:

- Dedikerade Bare Metal Server-noder etablerade med VMware ESXi hypervisor 
- vCenter-Server för hantering av ESXi och virtuellt San 
- VMware NSX-T program varu definierade nätverk för vSphere VM-arbetsbelastningar  
- VMware virtuellt San data lager för vSphere arbets belastning VM  
- VMware HCX för arbets belastnings mobilitet  

Ett privat moln i Azure VMware-lösningen innehåller också resurser i Azure-Underlay som krävs för anslutning och för att kunna använda det privata molnet. Azure VMware-lösningen övervakar kontinuerligt hälsan för både Underlay-och VMware-komponenterna. När Azure VMware-lösningen identifierar ett fel vidtar den åtgärder för att reparera de komponenter som misslyckades. 

## <a name="what-components-get-updated"></a>Vilka komponenter kommer att uppdateras?   

Azure VMware-lösningen uppdaterar följande VMware-komponenter: 

- vCenter Server och ESXi som körs på de Bare Metal-noderna 
- Virtuellt San 
- NSX-T 

Azure VMware-lösningen uppdaterar också program varan i Underlay, till exempel driv rutiner, program vara på nätverks växlarna och den inbyggda program varan på Bare Metal-noderna. 

## <a name="types-of-updates"></a>Typer av uppdateringar

Azure VMware-lösningen använder följande typer av uppdateringar av VMware-komponenter:

- Korrigeringar: säkerhets korrigeringar och fel korrigeringar som publicerats av VMware. 
- Uppdateringar: lägre versions uppdateringar av en eller flera VMware-komponenter. 
- Uppgraderingar: högre versions uppdateringar av en eller flera VMware-komponenter.

Du får ett meddelande innan och efter att korrigeringsfiler har tillämpats på dina privata moln. Vi kommer också att arbeta med dig för att schemalägga en underhålls period innan du tillämpar uppdateringar eller uppgraderingar i ditt privata moln. 

## <a name="vmware-appliance-backup"></a>Säkerhets kopiering av VMware-apparat 

Azure VMware-lösningen tar även en konfigurations säkerhets kopia av följande VMware-komponenter:

- vCenter Server 
- NSX-T-chef 

Vid misslyckade försök kan Azure VMware-lösningen återställa dessa komponenter från konfigurations säkerhets kopian. 

## <a name="vmware-software-versions"></a>Versioner av VMware-programvara
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Nästa steg

Nu när du har täckt de viktiga uppgraderings processerna och funktionerna i Azure VMware-lösningen kanske du vill lära dig mer om:

- [Så här skapar du ett privat moln](tutorial-create-private-cloud.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
