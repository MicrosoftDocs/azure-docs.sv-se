---
title: Azure VMware-lösnings gränser
description: Begränsningar i Azure VMware-lösningen.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582772"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

I följande tabell beskrivs de maximala gränserna för Azure VMware-lösningen.

| **Resurs** | **Gränserna** |
| :-- | :-- |
| Kluster per privat moln | 12 |
| Minsta antal noder per kluster | 3 |
| Maximalt antal noder per kluster | 16 |
| Noder per privat moln | 96 |
| vCenter per privat moln | 1  |
| HCX plats par | 3 med Advanced Edition, 10 med Enterprise Edition |
| AVS-ExpressRoute högsta länkade SDDCs | 4 |
| AVS-ExpressRoute portspeed | 10 Gbit/s<br />Den virtuella Nätverksgatewayen som används avgör den faktiska bandbredden. Mer information finns i [om ExpressRoute virtuella](../../expressroute/expressroute-about-virtual-network-gateways.md) nätverksgateway | 
| Offentliga IP-adresser som exponeras via vWAN | 100 |
| Virtuellt San kapacitets gränser | 75% av det totala antalet användbara (behåll 25% tillgängligt för SLA)  |

För andra VMware-begränsade gränser använder du [verktyget för maximalt VMware-konfiguration!](https://configmax.vmware.com/).
