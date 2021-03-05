---
title: Azure VMware-lösnings gränser
description: Begränsningar i Azure VMware-lösningen.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193906"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

I följande tabell beskrivs de maximala gränserna för Azure VMware-lösningen.

| **Resurs** | **Gränserna** |
| :-- | :-- |
| Kluster per privat moln | 4 |
| Minsta antal noder per kluster | 3 |
| Maximalt antal noder per kluster | 16 |
| Noder per privat moln | 64 |
| vCenter per privat moln | 1  |
| HCX plats par | 3 med Advanced Edition, 10 med Enterprise Edition |
| AVS-ExpressRoute högsta länkade SDDCs | 4 |
| AVS-ExpressRoute portspeed | 10 Gbit/s | 
| Offentliga IP-adresser som exponeras via vWAN | 100 |
| Virtuellt San kapacitets gränser | 75% av det totala antalet användbara (behåll 25% tillgängligt för SLA)  |

För andra VMware-begränsade gränser använder du [verktyget för maximalt VMware-konfiguration!](https://configmax.vmware.com/).
