---
title: Om att använda ExpressRoute med Azure Site Recovery
description: Beskriver hur du använder Azure-ExpressRoute med Azure Site Recovery tjänsten för haveri beredskap och migrering.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 99fa8d4cf8f48d0fe72da36baef20c83add438c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94330265"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure-ExpressRoute med Azure Site Recovery

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster som Microsoft Azure, Microsoft 365 och Dynamics 365.

I den här artikeln beskrivs hur du kan använda Azure-ExpressRoute med Azure Site Recovery för haveri beredskap och migrering.

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts moln tjänster via en anslutnings leverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan anslutas till dina lokaler via olika anslutnings leverantörer. Lär dig mer om ExpressRoute-kretsar [här](../expressroute/expressroute-circuit-peerings.md).

En ExpressRoute-krets har flera kopplade routningsdomäner. Lär dig mer om och jämför ExpressRoute-routningsdomäner [här](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokal till Azure-replikering med ExpressRoute

Azure Site Recovery möjliggör haveri beredskap och migrering till Azure för lokala [virtuella Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md)och [fysiska servrar](physical-azure-architecture.md). För alla lokala Azure-scenarier skickas replikeringsdata till och lagras i ett Azure Storage-konto. Under replikeringen betalar du inga avgifter för virtuella datorer. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

Site Recovery replikerar data till ett Azure Storage konto eller en replik hanterad disk på Azure-målets Azure-region via en offentlig slut punkt. Om du vill använda ExpressRoute för Site Recovery replikeringstrafik kan du använda [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller en befintlig [offentlig peering](../expressroute/about-public-peering.md) (inaktuell för nya skapelser). Microsoft-peering är den rekommenderade routningsdomänen för replikering. Observera att replikering endast stöds via privat peering när [privata slut punkter har Aktiver ATS för valvet](hybrid-how-to-enable-replication-private-endpoints.md).

Se till att [nätverks kraven](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurations servern också är uppfyllda. Anslutning till vissa URL: er krävs av konfigurations servern för att dirigera Site Recovery replikering. ExpressRoute kan inte användas för den här anslutningen. 

Om du använder proxy lokalt och vill använda ExpressRoute för replikeringstrafik måste du konfigurera listan över återanvändade proxyservrar på konfigurations servern och process servrarna. Följ stegen nedan:

- Hämta PsExec-verktyget [härifrån för att få åtkomst till användar](/sysinternals/downloads/psexec) kontexten för systemet.
- Öppna Internet Explorer i system användar kontext genom att köra följande kommando rad PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Lägg till proxyinställningar i IE
- I listan över undantag lägger du till Azure Storage-URL: en *. blob.core.windows.net

Detta säkerställer att endast replikeringstrafik flödar genom ExpressRoute medan kommunikationen kan gå via proxy.

När virtuella datorer eller servrar växlar över till ett virtuellt Azure-nätverk kan du komma åt dem via [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Det kombinerade scenariot representeras i följande diagram: ![ lokal-till-Azure med ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure till Azure-replikering med ExpressRoute

Azure Site Recovery möjliggör haveri beredskap för [virtuella Azure-datorer](azure-to-azure-architecture.md). Beroende på om dina virtuella Azure-datorer använder [azure Managed disks](../virtual-machines/managed-disks-overview.md)skickas replikeringsdata till ett Azure Storage konto eller en replik hanterad disk i Azure-regionen. Även om replikeringens slut punkter är offentliga, kommer replikeringstrafiken för replikering av virtuella Azure-datorer som standard inte att passera Internet, oavsett vilken Azure-region det virtuella käll nätverket finns i. Du kan åsidosätta Azures standard system väg för adressprefixet 0.0.0.0/0 med en [anpassad väg och dirigera](../virtual-network/virtual-networks-udr-overview.md#custom-routes) VM-trafik till en lokal virtuell nätverks installation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar bör du [skapa en tjänst slut punkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för "lagring", så att replikeringstrafiken inte lämnar Azure-gränser.

För haveri beredskap för virtuella Azure-datorer krävs som standard inte ExpressRoute för replikering. När de virtuella datorerna redundansväxlas till Azure-regionen kan du komma åt dem via [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). Observera att priserna för data överföring gäller oavsett vilket läge som används för datareplikering i Azure-regioner.

Om du redan använder ExpressRoute för att ansluta från ditt lokala data Center till de virtuella Azure-datorerna i käll regionen kan du planera för att återupprätta ExpressRoute-anslutningen i mål regionen för redundans. Du kan använda samma ExpressRoute-krets för att ansluta till mål regionen via en ny virtuell nätverks anslutning eller använda en separat ExpressRoute-krets och anslutning för haveri beredskap. De olika möjliga scenarierna beskrivs [här](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Du kan replikera virtuella Azure-datorer till valfri Azure-region inom samma geografiska kluster som beskrivs [här](../site-recovery/azure-to-azure-support-matrix.md#region-support). Om den valda Azure-regionen inte är inom samma region som källan, kan du behöva aktivera ExpressRoute Premium. Om du vill ha mer information kontrollerar du [ExpressRoute-platser](../expressroute/expressroute-locations.md) och [ExpressRoute-priser](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Läs mer om [ExpressRoute-routningsdomäner](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md).
- Läs mer om haveri beredskap för [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).