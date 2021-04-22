---
title: Begrepp – uppdateringar och uppgraderingar i privata moln
description: Lär dig mer om viktiga uppgraderingsprocesser och funktioner i Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ced5832a6d994f6cbc7e659d44ce4f6ac88681d0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876822"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution uppdateringar och uppgraderingar av privata moln

En fördel med Azure VMware Solution privata moln är att plattformen underhålls åt dig. Underhållet omfattar automatiserade uppdateringar av ett VMware-verifierat programpaket för att säkerställa att du använder den senaste versionen Azure VMware Solution programvara i privata moln.

Mer specifikt innehåller Azure VMware Solution privata moln:

- Dedikerade bare metal-servernoder som etablerats med VMware ESXi hypervisor 
- vCenter-server för att hantera ESXi och vSAN 
- VMware NSX-T-programvarudefinierat nätverk för virtuella vSphere-arbetsbelastningsbaserade datorer  
- VMware vSAN-datalager för virtuella vSphere-arbetsbelastningar  
- VMware HCX för arbetsbelastningsmobilitet  

Ett Azure VMware Solution privat moln innehåller även resurser i Azure-underlagret som krävs för anslutning och för att driva det privata molnet. Azure VMware Solution övervakar kontinuerligt hälsotillståndet för både de ingående komponenterna och VMware-komponenterna. När Azure VMware Solution upptäcker ett fel vidtar den åtgärder för att reparera de felande komponenterna. 

## <a name="what-components-get-updated"></a>Vilka komponenter uppdateras?   

Azure VMware Solution uppdaterar följande VMware-komponenter: 

- vCenter Server och ESXi som körs på bare metal-servernoderna 
- vSAN 
- NSX-T 

Azure VMware Solution uppdaterar även programvaran i underlagret, till exempel drivrutiner, programvara på nätverksväxlarna och inbyggd programvara på noder utan dator. 

## <a name="types-of-updates"></a>Typer av uppdateringar

Azure VMware Solution tillämpar följande typer av uppdateringar på VMware-komponenter:

- Korrigeringar: Säkerhetskorrigeringar och felkorrigeringar som har släppts av VMware. 
- Uppdateringar: Mindre versionsuppdateringar av en eller flera VMware-komponenter. 
- Uppgraderingar: Större versionsuppdateringar av en eller flera VMware-komponenter.

Du meddelas före och efter att korrigeringarna har tillämpats på dina privata moln. Vi kommer också att samarbeta med dig för att schemalägga en underhållsfönstret innan du tillämpar uppdateringar eller uppgraderingar i ditt privata moln. 

## <a name="vmware-appliance-backup"></a>Säkerhetskopiering av VMware-installation 

Azure VMware Solution också en konfigurationssäkerhetskopia av följande VMware-komponenter:

- vCenter Server 
- NSX-T Manager 

Vid fel kan Azure VMware Solution dessa komponenter från konfigurationssäkerhetskopieringen. 

## <a name="vmware-software-versions"></a>VMware-programvaruversioner
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på viktiga uppgraderingsprocesser och funktioner i Azure VMware Solution kanske du vill veta mer om:

- [Så här skapar du ett privat moln](tutorial-create-private-cloud.md)
- [Så här aktiverar du Azure VMware Solution resurs](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
