---
title: Azure VMware-lösnings gränser
description: Begränsningar i Azure VMware-lösningen.
ms.topic: include
ms.date: 02/25/2021
ms.openlocfilehash: 06160e967e7094cc3a6a51c1775873d0e4ab86b7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "102045793"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

I följande tabell beskrivs de maximala gränserna för Azure VMware-lösningen.

| **Resurs** | **Gränserna** |
| --- | --- |
| Kluster per privat moln | 4 |
| Minsta antal noder per kluster | 3 |
| Maximalt antal noder per kluster | 16 |
| Noder per privat moln | 64 |
|  vCenter per privat moln | 1  |
| HCX plats par | 3 med Advanced Edition, 10 med Enterprise Edition |
| ExpressRoute-gränser | 4 SDDCs, 10 SDDCs när Ultra Gateway används |
| Offentliga IP-adresser som exponeras via vWAN | 256 |
|  Virtuellt San kapacitets gränser | 75% av det totala antalet användbara (behåll 25% tillgängligt för SLA)  |
