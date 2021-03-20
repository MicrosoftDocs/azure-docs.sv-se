---
title: 'Azure-ExpressRoute: Konfigurera BFD'
description: Den här artikeln innehåller anvisningar om hur du konfigurerar BFD (identifiering av dubbelriktad vidarebefordran) över privat peering av en ExpressRoute-krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511271"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurera BFD över ExpressRoute

ExpressRoute stöder identifiering av dubbelriktad vidarebefordran (BFD) både över privat och Microsoft-peering. När du aktiverar BFD över ExpressRoute kan du påskynda hämtningen av länkfel mellan Microsoft Enterprise Edge-enheter (MSEE: N) och de routrar som din ExpressRoute-krets har konfigurerat (CE/PE). Du kan konfigurera ExpressRoute över dina Edge-routningsservrar eller dina partner gräns enheter för routning (om du gick med i den hanterade Layer 3-anslutnings tjänsten). Det här dokumentet vägleder dig genom behovet av BFD och hur du aktiverar BFD över ExpressRoute.

## <a name="need-for-bfd"></a>Behov av BFD

Följande diagram visar fördelarna med att aktivera BFD över ExpressRoute-kretsen: [![1]][1]

Du kan aktivera ExpressRoute-kretsen antingen via Layer 2 anslutningar eller hanterade Layer 3-anslutningar. I båda fallen, om det finns fler än en nivå 2-enheter i ExpressRoute anslutnings Sök väg, är ansvaret för att identifiera eventuella länkfel i sökvägen med den överliggande BGP-sessionen.

På MSEE: N-enheterna är BGP Keep-Alive och Hold-Time vanligt vis konfigurerade som 60 respektive 180 sekunder. Av den anledningen kan det ta upp till tre minuter att identifiera eventuella länkfel och växla trafik till en annan anslutning om det uppstår ett länkfel.

Du kan kontrol lera BGP-tidslägena genom att konfigurera en lägre BGP Keep-Alive och Hold-Time på din Edge peering-enhet. Om BGP-tidsutrustningen inte är samma mellan de två peering-enheterna, upprättas BGP-sessionen med det lägre tids värdet. BGP Keep-Alive kan anges till så lite som tre sekunder, och Hold-Time så lågt som 10 sekunder. Att ställa in en mycket aggressiv BGP-timer rekommenderas dock inte eftersom protokollet är process intensivt.

I det här scenariot kan BFD hjälpa dig. BFD ger låg overheadkostnad för länkfel i ett under sekunds tidsintervall. 


## <a name="enabling-bfd"></a>Aktivera BFD

BFD konfigureras som standard under alla nyligen skapade ExpressRoute privata peering-gränssnitt på msee. Därför behöver du bara konfigurera BFD på både dina primära och sekundära enheter för att aktivera BFD. Konfiguration av BFD är två stegs process. Du konfigurerar BFD på gränssnittet och länkar det sedan till BGP-sessionen.

Ett exempel på CE/PE-konfiguration (med Cisco IOS XE) visas nedan. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Så här aktiverar du BFD under en redan befintlig privat peering. Du måste återställa peer kopplingen. Se [återställa ExpressRoute-peering][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD timer-förhandling

Mellan BFD-peer-datorer fastställer överföringshastigheten för de två peer-datorerna. Msee BFD-överföring/mottagnings intervall anges till 300 millisekunder. I vissa fall kan intervallet anges till ett högre värde på 750 millisekunder. Genom att konfigurera ett högre värde kan du tvinga dessa intervall att vara längre, men det går inte att göra dem kortare.

>[!NOTE]
>Om du har konfigurerat geo-redundanta ExpressRoute-kretsar eller använda VPN-anslutning från plats till plats som säkerhets kopiering. Aktivering av BFD skulle hjälpa till att växla snabbare efter ett ExpressRoute anslutnings fel. 
>

## <a name="next-steps"></a>Nästa steg

Mer information eller hjälp finns i följande länkar:

- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD påskyndar länkning" av länkfel

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md