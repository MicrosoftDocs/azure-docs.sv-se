---
title: 'Azure Load Balancer SKU: er'
description: 'Översikt över Azure Load Balancer SKU: er'
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698095"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer SKU: er

Azure Load Balancer har två SKU: er.

## <a name="sku-comparison"></a><a name="skus"></a> SKU-jämförelse

Belastnings utjämning stöder både standard-och Basic-SKU: er. Dessa SKU: er skiljer sig åt i scenario skala, funktioner och priser. Alla scenarier som är möjliga med Basic Load Balancer kan skapas med standard Load Balancer.

Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [Översikt över Azure standard Load Balancer](./load-balancer-overview.md).

>[!NOTE]
> Microsoft rekommenderar standard Load Balancer.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Belastningsutjämnaren och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Belastnings utjämning och offentliga IP-SKU: er är inte föränderligt.

| | Standard Load Balancer | Grundläggande Load Balancer |
| --- | --- | --- |
| **[Storlek på serverdelspool](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Stöder upp till 1000 instanser. | Har stöd för upp till 300 instanser. |
| **Slutpunkter för serverdelspool** | Virtuella datorer eller skalnings uppsättningar för virtuella datorer i ett enda virtuellt nätverk. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| **[Hälsotillståndsavsökningar](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Beteende för hälso avsökning](./load-balancer-custom-probe-overview.md#probedown)** | TCP-anslutningar är aktiva vid en instans avsökning __och__ i alla avsökningar. | TCP-anslutningar är vilande vid en instans avsökning. Alla TCP-anslutningar slutar när alla avsökningar är nere. |
| **Tillgänglighetszoner** | Zoner-redundanta och zonindelade-frontend-klienter för inkommande och utgående trafik. | Inte tillgängligt |
| **Diagnostik** | [Azure Monitor flerdimensionella mått](./load-balancer-standard-diagnostics.md) | [Azure Monitor-loggar](./load-balancer-monitor-log.md) |
| **HA-portar** | [Tillgängligt för interna Load Balancer](./load-balancer-ha-ports-overview.md) | Inte tillgängligt |
| **Säker som standard** | Stängda för inkommande flöden om det inte tillåts av en nätverks säkerhets grupp. Intern trafik från det virtuella nätverket till den interna belastningsutjämnaren tillåts. | Öppnas som standard. Nätverks säkerhets gruppen är valfri. |
| **Utgående regler** | [Deklarativ utgående NAT-konfiguration](./load-balancer-outbound-connections.md#outboundrules) | Inte tillgängligt |
| **TCP-återställning vid inaktivitet** | [Tillgängligt för någon regel](./load-balancer-tcp-reset.md) | Inte tillgängligt |
| **[Flera frontend-sidor](./load-balancer-multivip-overview.md)** | Inkommande och [utgående](./load-balancer-outbound-connections.md) | Endast inkommande |
| **Hanteringsåtgärder** | De flesta åtgärder < 30 sekunder | 60-90 + sekunders standard |
| **Serviceavtal** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Inte tillgängligt | 

Mer information finns i [gränser för belastnings utjämning](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Information om Standard Load Balancer finns i [översikt](./load-balancer-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="limitations"></a>Begränsningar

- SKU: er är inte föränderligt. Du kan inte ändra SKU för en befintlig resurs.
- En fristående virtuell dator resurs, tillgänglighets uppsättnings resurs eller en resurs för skalnings uppsättning för virtuell dator kan referera till en SKU, aldrig båda.
- [Flytta åtgärder](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - Flytt av resurs grupps åtgärder (inom samma prenumeration) **stöds** för standard Load Balancer och standard-offentlig IP. 
  - [Flytt åtgärder för prenumerations grupper](../azure-resource-manager/management/move-support-resources.md) stöds **inte** för standard Load Balancer och offentliga standard-IP-resurser.

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om hur du använder [Load Balancer för utgående anslutningar](load-balancer-outbound-connections.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).