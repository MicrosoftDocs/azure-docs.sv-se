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
ms.openlocfilehash: 60e8a0dc93e19dfec0982080d0353466b08373de
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586842"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer SKU: er

Azure Load Balancer har två varianter eller SKU: er.

## <a name="sku-comparison"></a><a name="skus"></a> SKU-jämförelse

Load Balancer stöder både Basic-och standard-SKU: er. Dessa SKU: er skiljer sig åt i scenario skala, funktioner och priser. Alla scenarier som är möjliga med Basic Load Balancer kan skapas med standard Load Balancer.

Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [Översikt över Azure standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft rekommenderar standard Load Balancer.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Belastningsutjämnaren och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Belastnings utjämning och offentliga IP-SKU: er är inte föränderligt.

| | Standard Load Balancer | Grundläggande Load Balancer |
| --- | --- | --- |
| **[Storlek på serverdelspool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)** | Stöder upp till 1000 instanser. | Har stöd för upp till 300 instanser. |
| **Slutpunkter för serverdelspool** | Virtuella datorer eller skalnings uppsättningar för virtuella datorer i ett enda virtuellt nätverk. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| **[Hälsotillståndsavsökningar](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Beteende för hälso avsökning](./load-balancer-custom-probe-overview.md#probedown)** | TCP-anslutningar är aktiva vid en instans avsökning __och__ i alla avsökningar. | TCP-anslutningar är vilande vid en instans avsökning. Alla TCP-anslutningar avslutas när alla avsökningar är nere. |
| **Tillgänglighetszoner** | Zoner-redundanta och zonindelade-frontend-klienter för inkommande och utgående trafik. | Inte tillgängligt |
| **Diagnostik** | [Azure Monitor flerdimensionella mått](./load-balancer-standard-diagnostics.md) | [Azure Monitor loggar](./load-balancer-monitor-log.md) |
| **HA-portar** | [Tillgängligt för interna Load Balancer](./load-balancer-ha-ports-overview.md) | Inte tillgängligt |
| **Säker som standard** | Stängda för inkommande flöden om det inte tillåts av en nätverks säkerhets grupp. Observera att intern trafik från VNet till den interna belastningsutjämnaren tillåts. | Öppnas som standard. Nätverks säkerhets gruppen är valfri. |
| **Utgående regler** | [Deklarativ utgående NAT-konfiguration](./load-balancer-outbound-rules-overview.md) | Inte tillgängligt |
| **TCP-återställning vid inaktivitet** | [Tillgängligt för någon regel](./load-balancer-tcp-reset.md) | Inte tillgängligt |
| **[Flera frontend-sidor](./load-balancer-multivip-overview.md)** | Inkommande och [utgående](./load-balancer-outbound-connections.md) | Endast inkommande |
| **Hanterings åtgärder** | De flesta åtgärder < 30 sekunder | 60-90 + sekunders standard |
| **Serviceavtal** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Inte tillgängligt | 

Mer information finns i [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="limitations"></a>Begränsningar

- SKU: er är inte föränderligt. Du kan inte ändra SKU för en befintlig resurs.
- En fristående virtuell dator resurs, tillgänglighets uppsättnings resurs eller en resurs för skalnings uppsättning för virtuell dator kan referera till en SKU, aldrig båda.
- [Flytt av prenumerations åtgärder](../azure-resource-manager/management/move-resource-group-and-subscription.md) stöds inte för standard Load Balancer-och standard-offentliga IP-resurser.

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om hur du använder [Load Balancer för utgående anslutningar](load-balancer-outbound-connections.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
