---
title: Koncept – nätverks anslutning
description: Lär dig mer om viktiga aspekter och användnings fall för nätverk och anslutningar i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: ddf8f5b6aa06154a6edde7b4a78902d8f13eab78
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364910"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Nätverks-och samanslutnings koncept i Azure VMware-lösningen

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Ett användbart perspektiv på samverkande är att överväga de två typerna av Azure VMware-lösningar för privata moln implementeringar:

1. [**Grundläggande Azure-anslutningar med enbart Azure**](#azure-virtual-network-interconnectivity) kan du hantera och använda ditt privata moln med bara ett enda virtuellt nätverk i Azure. Den här implementeringen passar bäst för Azures utvärdering av VMware-lösningar eller implementeringar som inte kräver åtkomst från lokala miljöer.

1. En [**fullständig lokal till privat moln anslutning**](#on-premises-interconnectivity) utökar den grundläggande Azure-bara implementeringen för att omfatta samanslutning mellan lokala och Azure VMware-lösningar privata moln.
 
I den här artikeln tar vi upp några viktiga begrepp som etablerar nätverk och anslutningar, inklusive krav och begränsningar. Vi kommer också att få mer information om de två typerna av Azure VMware-lösningars implementeringar av privata moln. Den här artikeln innehåller den information du behöver känna till för att konfigurera nätverket så att det fungerar med Azure VMware-lösningen korrekt.

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

I det virtuella nätverket för implementering av privata moln kan du hantera Azure VMware-lösningens privata moln, använda arbets belastningar i ditt privata moln och få åtkomst till Azure-tjänster via ExpressRoute-anslutningen. 

Diagrammet nedan visar den grundläggande nätverks anslutningen som upprättats vid tidpunkten för en distribution av privata moln. Det visar det logiska, ExpressRoute-baserade nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen uppfyller tre av de främsta användnings fallen:
* Inkommande åtkomst till vCenter Server och NSX-T-hanterare som är tillgängliga från virtuella datorer i din Azure-prenumeration och inte från dina lokala system. 
* Utgående åtkomst från virtuella datorer till Azure-tjänster. 
* Inkommande åtkomst och konsumtion av arbets belastningar som kör ett privat moln.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Grundläggande virtuellt nätverk för anslutning till privat moln" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokal anslutning

I det virtuella nätverket och lokalt för att få fullständig privat moln implementering kan du komma åt dina privata moln i Azure VMware-lösningen från lokala miljöer. Den här implementeringen är en utökning av den grundläggande implementering som beskrivs i föregående avsnitt. Precis som den grundläggande implementeringen krävs en ExpressRoute-krets, men med den här implementeringen används den för att ansluta från lokala miljöer till ditt privata moln i Azure. 

I diagrammet nedan visas samanslutningen mellan lokala och privata moln, vilket möjliggör följande användnings fall:
* Hett/kall över-vCenter-vMotion
* Lokal åtkomst till Azure VMware-lösning hantering av privata moln

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuella nätverk och lokala anslutningar med fullständig privat moln" border="false":::

För fullständig anslutning till ditt privata moln aktiverar du ExpressRoute Global Reach och begär sedan en auktoriseringspost och ett privat peering-ID för Global Reach i Azure Portal. Auktoriseringsregeln och peering-ID används för att etablera Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt nya privata moln. När de två ExpressRoute kretsarna dirigerar nätverks trafiken mellan dina lokala miljöer till ditt privata moln.  Mer information om procedurerna för att begära och använda auktoriseringsprincipen och peering-ID finns i [självstudien för att skapa en ExpressRoute Global Reach peering i ett privat moln](tutorial-expressroute-global-reach-private-cloud.md).

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
