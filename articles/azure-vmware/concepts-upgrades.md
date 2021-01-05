---
title: Koncept – uppdateringar och uppgraderingar av privata moln
description: Lär dig mer om de viktiga uppgraderings processerna och funktionerna i Azure VMware-lösningen.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6fb65a0e192fbf1f054857223b3fdaf58fc6a903
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809657"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Uppdateringar och uppgraderingar av privata moln i Azure VMware-lösningar

En av de främsta fördelarna med privata moln för VMware-lösningar i Azure är att plattformen upprätthålls för dig. Plattforms underhåll innehåller automatiserade uppdateringar av en skyddad VMware-programpaket, vilket hjälper dig att se till att du använder den senaste versionen av det privata moln programmet för Azure VMware-lösningen.

Mer specifikt innehåller ett privat moln i Azure VMware-lösningen:

- Dedikerade Bare Metal Server-noder etablerade med VMware ESXi hypervisor 
- vCenter-Server för hantering av ESXi och virtuellt San 
- VMware NSX-T program varu definierade nätverk för vSphere VM-arbetsbelastningar  
- VMware virtuellt San data lager för vSphere arbets belastning VM  
- VMware HCX för arbets belastnings mobilitet  

Förutom dessa komponenter innehåller ett privat moln i Azure VMware-lösningen resurser i Azure-Underlay som krävs för anslutning och för att kunna använda det privata molnet. Azure VMware-lösningen övervakar kontinuerligt hälsan för både Underlay-och VMware-komponenterna. När Azure VMware-lösningen identifierar ett fel vidtar den åtgärder för att reparera de komponenter som misslyckades. 

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

Du får ett meddelande innan och efter att korrigeringarna har tillämpats på dina privata moln. Vi kommer också att arbeta med dig för att schemalägga en underhålls period innan du tillämpar uppdateringar eller uppgraderingar i ditt privata moln. 

## <a name="vmware-appliance-backup"></a>Säkerhets kopiering av VMware-apparat 

Förutom att göra uppdateringar, tar Azure VMware-lösningen en konfigurations säkerhets kopia av dessa VMware-komponenter:

- vCenter Server 
- NSX-T-chef 

Vid misslyckade försök kan Azure VMware-lösningen återställa dessa från konfigurations säkerhets kopian. 

Mer information om VMware-programversioner finns i [artikeln privata moln och kluster begrepp](concepts-private-clouds-clusters.md) och [vanliga frågor och svar](faq.md).

## <a name="next-steps"></a>Nästa steg

Nästa steg är att [skapa ett privat moln](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
