---
title: Koncept – nätverks anslutning
description: Lär dig mer om viktiga aspekter och användnings fall för nätverk och anslutningar i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491817"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Nätverks-och samanslutnings koncept i Azure VMware-lösningen

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Det finns två sätt att ansluta till ett privat moln i Azure VMware-lösningen:

- [**Grundläggande Azure-anslutningar med enbart Azure**](#azure-virtual-network-interconnectivity) kan du hantera och använda ditt privata moln med bara ett enda virtuellt nätverk i Azure. Den här implementeringen passar bäst för Azures utvärdering av VMware-lösningar eller implementeringar som inte kräver åtkomst från lokala miljöer.

- En [**fullständig lokal till privat moln anslutning**](#on-premises-interconnectivity) utökar den grundläggande Azure-bara implementeringen för att omfatta samanslutning mellan lokala och Azure VMware-lösningar privata moln.
 
I den här artikeln tar vi upp viktiga begrepp som etablerar nätverk och anslutningar, inklusive krav och begränsningar. Den här artikeln innehåller den information du behöver känna till för att konfigurera nätverket så att det fungerar med Azure VMware-lösningen.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Användnings fall för privata moln lösningar i Azure VMware

Användnings fallen för privata moln i Azure VMware-lösningen är:
- Nya virtuella VMware-arbetsbelastningar i molnet
- VM-arbetsbelastning till molnet (endast lokalt till Azure VMware-lösning)
- Migrering av VM-arbetsbelastning till molnet (endast lokalt till Azure VMware-lösning)
- Haveri beredskap (Azure VMware-lösning till Azure VMware-lösning eller lokal Azure VMware-lösning)
- Användning av Azure-tjänster

> [!TIP]
> Alla användnings fall för tjänsten Azure VMware Solution är aktiverade med lokal anslutning till privat moln.

## <a name="azure-virtual-network-interconnectivity"></a>Anslutning mellan virtuella Azure-nätverk

Du kan sammanställa ditt virtuella Azure-nätverk med Azure VMware-lösningen privat moln implementering. Du kan hantera Azure VMware-lösningens privata moln, använda arbets belastningar i ditt privata moln och få åtkomst till andra Azure-tjänster.

Diagrammet nedan visar den grundläggande nätverks anslutningen som upprättats vid tidpunkten för en distribution av privata moln. Det visar det logiska nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen upprättas via en backend-ExpressRoute som ingår i Azure VMware Solution service. Anslutningarna uppfyller följande primära användnings fall:

- Inkommande åtkomst till vCenter Server och NSX-T-hanterare som är tillgängliga från virtuella datorer i din Azure-prenumeration.
- Utgående åtkomst från virtuella datorer i det privata molnet till Azure-tjänster.
- Inkommande åtkomst till arbets belastningar som körs i det privata molnet.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Grundläggande virtuellt nätverk för anslutning till privat moln" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokal anslutning

I det helt sammankopplade scenariot kan du komma åt Azure VMware-lösningen från dina virtuella Azure-nätverk och lokala. Den här implementeringen är en utökning av den grundläggande implementering som beskrivs i föregående avsnitt. En ExpressRoute-krets krävs för att ansluta lokalt till ditt privata moln i Azure VMware-lösningen i Azure.

I diagrammet nedan visas samanslutningen mellan lokala och privata moln, vilket möjliggör följande användnings fall:

- Varmt/kall vCenter-vMotion mellan lokal och Azure VMware-lösning.
- Lokal åtkomst till Azure VMware-lösningar hantering av privata moln.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuella nätverk och lokala anslutningar med fullständig privat moln" border="false":::

För fullständig anslutning till ditt privata moln måste du aktivera ExpressRoute Global Reach och sedan begära en nyckel för autentisering och privat peering-ID för Global Reach i Azure Portal. Nyckel-och peering-ID används för att upprätta Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt privata moln. När de två ExpressRoute kretsarna dirigerar nätverks trafiken mellan dina lokala miljöer till ditt privata moln. Mer information om procedurerna finns i [självstudien för att skapa en ExpressRoute Global Reach peering i ett privat moln](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Begränsningar
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Nästa steg 

Nu när du har täckt Azure VMware-lösningen nätverk och samanslutnings koncept, kanske du vill lära dig mer om:

- [Lagrings koncept för Azure VMware-lösningar](concepts-storage.md).
- [Identitets koncept för Azure VMware-lösning](concepts-identity.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
