---
title: Azure ExpressRoute Insights med hjälp av nätverks insikter
description: Lär dig mer om Azure ExpressRoute Insights med hjälp av nätverks insikter.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050681"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Azure ExpressRoute Insights med hjälp av nätverks insikter

Den här artikeln förklarar hur du kan använda nätverks insikter för att visa dina ExpressRoute-mått och konfigurationer på ett och samma ställe. Med hjälp av nätverks insikter kan du Visa Top ologiska kartor och hälso instrument paneler som innehåller viktig ExpressRoute-information utan att behöva slutföra några extra inställningar.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Skärm bild av landnings sidan för ExpressRoute-övervakaren." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Visualisera funktions beroenden

Om du vill visa den här lösningen går du till sidan *Azure Monitor* , väljer *nätverk* och väljer sedan kortet *ExpressRoute-kretsar* . Välj sedan knappen topologi för kretsen som du vill visa.

Vyn funktionellt beroende innehåller en tydlig bild av ExpressRoute-installationen, som visar relationen mellan olika ExpressRoute-komponenter (peering, anslutningar, gatewayer).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Skärm bild av Topology-vy för nätverks insikter." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Hovra över valfri komponent i topologin för att Visa konfigurations information. Till exempel, Hovra över en ExpressRoute-peering-komponent för att visa information som krets bandbredd och Global Reach aktivering.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Skärm bild som visar hur du hovrar över topologin." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Visa en detaljerad och förinlästa mått panel

När du har granskat topologin för din ExpressRoute-installation med hjälp av vyn funktionell beroende väljer du **Visa detaljerade mått** för att navigera till vyn detaljerade mått för att förstå prestandan för din krets. Den här vyn innehåller en ordnad lista över länkade resurser och en omfattande instrument panel med viktiga ExpressRoute-mått.

I avsnittet **länkade resurser** visas anslutna ExpressRoute-gatewayer och konfigurerade peer-kopplingar, som du kan välja på för att navigera till motsvarande resurs sida.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Skärm bild av länkad resurs på Monitor-sidan.":::


Avsnittet **ExpressRoute mått** innehåller diagram över viktiga krets mått i kategorierna **tillgänglighet**, **data flöde**, **paket droppar** och **Gateway-mått**.

### <a name="availability"></a>Tillgänglighet

Fliken *tillgänglighet* spårar ARP och BGP-tillgänglighet, och ritar data för både kretsen som helhet och enskild anslutning (primär och sekundär). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Skärm bild av diagram över tillgänglighets mått." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Dataflöde

På samma sätt visar fliken *genomflöde* det totala data flödet för inkommande och utgående trafik för kretsen i bitar per sekund. Du kan också visa data flöde för enskilda anslutningar och varje typ av konfigurerad peering.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Skärm bild av diagram över data flödes mått." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Förlorade paket

Fliken *ignorerade paket* ritar ned bitar/sekund för ingångs-och utgående trafik genom kretsen. Den här fliken är ett enkelt sätt att övervaka prestanda problem som kan uppstå om du regelbundet behöver eller överskrider din krets bandbredd.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Skärm bild av diagram över ignorerade paket." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Gatewaymått

Slutligen fyller fliken Gateway-mått med nyckel mått diagram för en vald ExpressRoute-Gateway (från avsnittet länkade resurser). Använd den här fliken när du behöver övervaka anslutningen till vissa virtuella nätverk.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Skärm bild av Gateway-dataflöde och CPU-mått." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
* Lär dig mer om [Azure ExpressRoute](expressroute-introduction.md), [nätverks insikter](../azure-monitor/insights/network-insights-overview.md)och [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
* [Skapa och ändra peering-konfiguration](expressroute-howto-routing-arm.md)
* [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
* [Anpassa måtten](expressroute-monitoring-metrics-alerts.md) och skapa en [anslutnings övervakare](../network-watcher/connection-monitor-overview.md)
