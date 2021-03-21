---
title: Tillgänglighets alternativ för Azure Virtual Machines
description: Lär dig mer om tillgänglighets alternativen för att köra virtuella datorer i Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507882"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Tillgänglighets alternativ för Azure Virtual Machines
Den här artikeln innehåller en översikt över tillgänglighets alternativ för virtuella Azure-datorer (VM).

## <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighets zoner](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) utökar kontroll nivån som du måste ha för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. En tillgänglighets zon är en fysiskt separat zon i en Azure-region. Det finns tre Tillgänglighetszoner per Azure-region som stöds. 

Varje tillgänglighetszon har en distinkt strömkälla, nätverk och kylning. Genom att utforma dina lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina appar och data från förlust av ett Data Center. Om en zon komprometteras, är replikerade appar och data omedelbart tillgängliga i en annan zon. 

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En [tillgänglighets uppsättning](availability-set-overview.md) är en logisk gruppering av virtuella datorer som gör det möjligt för Azure att förstå hur ditt program är utformat för att tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighets uppsättning för att tillhandahålla ett program med hög tillgänglighet och uppfylla [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För själva tillgänglighets uppsättningen kostar du bara att betala för varje VM-instans som du skapar.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines skalnings uppsättningar 

Med [skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) kan du skapa och hantera en grupp med belastningsutjämnade virtuella datorer. Antalet VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Skalnings uppsättningar ger hög tillgänglighet till dina program och gör att du kan hantera, konfigurera och uppdatera många virtuella datorer centralt. Vi rekommenderar att två eller flera virtuella datorer skapas i en skalnings uppsättning för att tillhandahålla ett program med hög tillgänglighet och som uppfyller [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Du betalar ingen kostnad för själva skalnings uppsättningen. du betalar bara för varje VM-instans som du skapar.

Virtuella datorer i en skalnings uppsättning kan också distribueras till en enda tillgänglighets zon eller regionalt. Distributions alternativen för tillgänglighets zon kan variera beroende på [Dirigerings läge](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Lastbalanserare
Kombinera [Azure Load Balancer](../load-balancer/load-balancer-overview.md) med en tillgänglighets zon eller tillgänglighets uppsättning för att få flest program återhämtning. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastnings utjämning för virtuella datorer finns i **belastnings utjämning för virtuella datorer** för [Linux](linux/tutorial-load-balancer.md) eller [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redundans i Azure Storage
Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Redundans garanterar att ditt lagrings konto uppfyller sina tillgänglighets-och hållbarhets mål även om de inte är i rätt tid.

När du bestämmer vilket alternativ för redundans som passar bäst för ditt scenario bör du fundera över kompromisserna mellan lägre kostnader och högre tillgänglighet. De faktorer som hjälper dig att avgöra vilket alternativ för redundans som du bör välja bland:
- Hur dina data replikeras i den primära regionen
- Om dina data replikeras till en andra region som är geografiskt avlägsen till den primära regionen, för att skydda mot regionala haverier
- Huruvida programmet kräver Läs behörighet till replikerade data i den sekundära regionen om den primära regionen blir otillgänglig av någon anledning

Mer information finns i [Azure Storage redundans](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Som organisation måste du införa en strategi för affärs kontinuitet och haveri beredskap (BCDR) som skyddar dina data och dina appar och arbets belastningar online, när planerade och oplanerade avbrott inträffar.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) hjälper till att säkerställa affärs kontinuiteten genom att hålla affärsappar och arbets belastningar som körs under drift avbrott. Site Recovery replikerar arbetsbelastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats. När ett driftstopp uppstår på den primära platsen sker redundansväxling till den sekundära platsen, och det går att komma åt apparna därifrån. När den primära platsen körs igen kan du växla tillbaka till den.

Site Recovery kan hantera replikering för:
- Replikera virtuella Azure-datorer mellan Azure-regioner.
- Lokala virtuella datorer, Azure Stack virtuella datorer och fysiska servrar.

## <a name="next-steps"></a>Nästa steg
- [Skapa en virtuell dator i en tillgänglighets zon](/linux/create-cli-availability-zone.md)
- [Skapa en virtuell dator i en tillgänglighets uppsättning](/linux/tutorial-availability.md)
- [Skapa en VM-skalningsuppsättning](../virtual-machine-scale-sets/quick-create-portal.md)