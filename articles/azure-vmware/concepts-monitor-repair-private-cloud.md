---
title: Koncept – Övervaka och reparera Azure VMware Solution privata moln
description: Lär dig Azure VMware Solution övervakar och reparerar VMware ESXi servrar i ett Azure VMware Solution privat moln.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 9fa94d6093e03432f20672ecf5c0160ce479e175
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871677"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Övervaka och reparera Azure VMware Solution privata moln

Azure VMware Solution övervakar kontinuerligt VMware ESXi servrar i ett Azure VMware Solution privat moln. 

## <a name="what-azure-vmware-solution-monitors"></a>Vilka Azure VMware Solution övervakare

Azure VMware Solution övervakar följande villkor på värden:  

- Processorstatus 
- Minnesstatus 
- Anslutning och energisparläge 
- Status för maskinvaru-fläkt 
- Förlust av nätverksanslutning 
- Status för maskinvarusystemkort 
- Fel uppstod på en eller flera diskar på en vSAN-värd 
- Maskinvarans spänning 
- Maskinvarutemperaturstatus 
- Status för maskinvarans ström 
- Lagringsstatus 
- Anslutningen misslyckades 

> [!NOTE]
> Azure VMware Solution klientorganisationsadministratörer får inte redigera eller ta bort ovanstående definierade VMware vCenter-larm, eftersom dessa hanteras av det Azure VMware Solution kontrollplanet på vCenter. Dessa larm används av Azure VMware Solution för att utlösa Azure VMware Solution för värdåtgärder.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware Solution värdreparation  

När Azure VMware Solution identifierar en försämring eller ett fel på en Azure VMware Solution-nod utlöser den värdreparationsprocessen. Värdreparation innebär att ersätta den felaktiga noden med en ny felfri nod.  

Värdreparationen börjar med att lägga till en ny felfri nod i klustret. När det är möjligt placeras den felaktiga värden i VMware vSphere underhållsläge. VMware vMotion flyttar de virtuella datorerna från den felaktiga värden till andra tillgängliga servrar i klustret, vilket potentiellt ger noll driftstopp för direktmigrering av arbetsbelastningar. Om den felaktiga värden inte kan placeras i underhållsläge tas värden bort från klustret.

## <a name="next-steps"></a>Nästa steg

Nu när du har gått Azure VMware Solution övervakar och reparerar privata moln kanske du vill veta mer om:

- [Azure VMware Solution uppgraderingar av privata moln](concepts-upgrades.md)
- [Så här aktiverar du Azure VMware Solution resurs](enable-azure-vmware-solution.md)
