---
title: Azure VMware-lösnings gränser
description: Begränsningar i Azure VMware-lösningen.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622239"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

I följande tabell beskrivs de maximala gränserna för Azure VMware-lösningen.

| **Resurs** | **Gränserna** |
| :-- | :-- |
| Kluster per privat moln | 12 |
| Minsta antal noder per kluster | 3 |
| Maximalt antal noder per kluster | 16 |
| Noder per privat moln | 64 |
| vCenter per privat moln | 1  |
| HCX plats par | 3 med Advanced Edition, 10 med Enterprise Edition |
| AVS-ExpressRoute högsta länkade SDDCs | 4 |
| AVS-ExpressRoute portspeed | 10 Gbit/s<br />Den virtuella Nätverksgatewayen som används avgör den faktiska bandbredden. Mer information finns i [om ExpressRoute virtuella](../../expressroute/expressroute-about-virtual-network-gateways.md) nätverksgateway | 
| Offentliga IP-adresser som exponeras via vWAN | 100 |
| Virtuellt San kapacitets gränser | 75% av det totala antalet användbara (behåll 25% tillgängligt för SLA)  |

För andra VMware-begränsade gränser använder du [verktyget för maximalt VMware-konfiguration!](https://configmax.vmware.com/).
