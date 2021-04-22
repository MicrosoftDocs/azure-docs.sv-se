---
title: Begrepp – nätverksanslutning
description: Lär dig mer om viktiga aspekter och användningsfall för nätverk och sammankoppling i Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871695"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution nätverks- och sammankopplingsbegrepp

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Det finns två sätt att koppla samman i Azure VMware Solution privata molnet:

- [**Med enkel Azure-sammankoppling kan**](#azure-virtual-network-interconnectivity) du hantera och använda ditt privata moln med endast ett enda virtuellt nätverk i Azure. Den här implementeringen passar bäst Azure VMware Solution utvärderingar eller implementeringar som inte kräver åtkomst från lokala miljöer.

- [**Fullständig sammankoppling mellan**](#on-premises-interconnectivity) lokala och privata moln utökar den grundläggande Azure-implementeringen så att den omfattar sammankoppling mellan lokala och Azure VMware Solution privata moln.
 
I den här artikeln går vi in på de viktigaste begreppen som upprättar nätverk och sammankoppling, inklusive krav och begränsningar. Den här artikeln innehåller den information du behöver känna till för att konfigurera nätverket så att det fungerar med Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution användningsfall för privata moln

Användningsfallen för Azure VMware Solution privata moln är:
- Nya VMware VM-arbetsbelastningar i molnet
- Vm-arbetsbelastningen burst-körs till molnet (endast lokalt till Azure VMware Solution)
- Migrering av VM-arbetsbelastning till molnet (endast lokalt till Azure VMware Solution)
- Haveriberedskap (Azure VMware Solution till Azure VMware Solution eller lokalt till Azure VMware Solution)
- Förbrukning av Azure-tjänster

> [!TIP]
> Alla användningsfall för Azure VMware Solution-tjänsten är aktiverade med anslutning lokalt till privat moln.

## <a name="azure-virtual-network-interconnectivity"></a>Sammankoppling av virtuella Azure-nätverk

Du kan koppla samman ditt virtuella Azure-nätverk med Azure VMware Solution implementering av privata moln. Du kan hantera Azure VMware Solution privata molnet, använda arbetsbelastningar i ditt privata moln och få åtkomst till andra Azure-tjänster.

Diagrammet nedan visar den grundläggande nätverksanslutning som etablerades vid tidpunkten för distributionen av ett privat moln. Den visar det logiska nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen upprättas via en ExpressRoute-backend som är en del Azure VMware Solution tjänsten. Sammankopplingen uppfyller följande primära användningsfall:

- Inkommande åtkomst till vCenter-servern och NSX-T-hanteraren som är tillgänglig från virtuella datorer i din Azure-prenumeration.
- Utgående åtkomst från virtuella datorer i det privata molnet till Azure-tjänster.
- Inkommande åtkomst för arbetsbelastningar som körs i det privata molnet.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Grundläggande anslutning mellan virtuella nätverk och privata moln" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokal sammankoppling

I det fullständigt sammankopplade scenariot kan du komma åt Azure VMware Solution från dina virtuella Azure-nätverk och lokala nätverk. Den här implementeringen är en utökning av den grundläggande implementering som beskrivs i föregående avsnitt. En ExpressRoute-krets krävs för att ansluta från en lokal plats till Azure VMware Solution privata molnet i Azure.

Diagrammet nedan visar sammankopplingen mellan lokala och privata moln, vilket möjliggör följande användningsfall:

- Hot/Cold vCenter vMotion mellan lokala och Azure VMware Solution.
- Lokalt för att Azure VMware Solution åtkomst till hantering av privata moln.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Anslutning till virtuella nätverk och lokala fullständiga privata moln" border="false":::

För fullständig sammankoppling till ditt privata moln måste du aktivera ExpressRoute Global Reach och sedan begära en auktoriseringsnyckel och ett privat peering-ID för Global Reach i Azure Portal. Auktoriseringsnyckeln och peering-ID:t används för att upprätta Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt privata moln. När de är länkade dirigerar de två ExpressRoute-kretsarna nätverkstrafik mellan dina lokala miljöer till ditt privata moln. Mer information om procedurerna finns i [självstudien för att skapa en ExpressRoute-Global Reach till ett privat moln.](tutorial-expressroute-global-reach-private-cloud.md)

## <a name="limitations"></a>Begränsningar
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Nästa steg 

Nu när du har gått Azure VMware Solution nätverks- och sammankopplingsbegrepp kanske du vill lära dig mer om:

- [Azure VMware Solution lagringsbegrepp](concepts-storage.md)
- [Azure VMware Solution identitetsbegrepp](concepts-identity.md)
- [Så här aktiverar du Azure VMware Solution resurs](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
