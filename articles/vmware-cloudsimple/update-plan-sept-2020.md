---
title: Azure VMware-lösning från CloudSimple september 2020 Update
description: I den här artikeln får du veta mer om vad du kan förväntar dig under underhålls åtgärden och ändringar i ditt privata moln.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89448028"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware-lösning från CloudSimple september 2020 Update

En viktig uppdatering till Azure VMware Solution service utförs i september. Ett e-postmeddelande som skickas som en del av underhållet innehåller tids linjen för underhållet. I den här artikeln får du lära dig hur du förväntar dig för den här underhålls åtgärden och ändringar i ditt privata moln.

> [!NOTE]
> Detta är en icke-störnings uppgradering. Under uppgraderingen kan en av de redundanta komponenterna visas.

## <a name="vmware-infrastructure-upgrade"></a>Uppgradering av VMware-infrastruktur

VMware-infrastrukturen i ditt privata moln kommer att uppdateras till en nyare version. Detta inkluderar uppdateringar av HCX-komponenter (vCenter, ESXi, NSX-T och Hybrid Cloud Extension) (om de har distribuerats) i ditt privata moln.

Under uppgraderingen läggs en ny nod till i ditt privata moln innan noden placeras i underhålls läge för uppgraderings åtgärder. Detta säkerställer att det privata molnets kapacitet och tillgängligheten för ditt privata moln upprätthålls under uppgraderings processen. Under uppgraderingen av VMware-komponenter kan du se alarm som visas i vCenter-webbgränssnittet. Larmen är en del av de underhålls åtgärder som utförs av tjänst drifts teamet.

**Komponent version**

- ESXi 6,7-U3
- vCenter 6.7-U3
- Virtuellt San 6,7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Data Center uppdateringar

Uppdateringen innehåller uppdateringar av data Center infrastruktur. Icke-störande uppdateringar utförs under underhålls perioden. Du märker att du har minskat redundans under uppdaterings processen. Aviseringar kan genereras i din privata moln VMware-infrastruktur, dina ExpressRoute-kretsar, GlobalReach-anslutningar och alla plats-till-plats VPN-enheter som är relaterade till en av länk tillgänglighet. Detta är normalt under uppdateringen när komponenterna startas om som en del av uppdateringen.

-   Om en plats-till-plats-VPN distribueras som en enskild instans (icke-HA) kan du behöva återupprätta VPN-anslutningen.

-   Om du använder en punkt-till-plats-VPN-anslutning måste du återupprätta VPN-anslutningen igen.

## <a name="post-update"></a>Publicera uppdatering

När uppdateringarna har slutförts bör du se nyare versioner av VMware-komponenter. Om du upptäcker några problem eller har frågor kan du kontakta vårt support team genom att öppna ett [support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
