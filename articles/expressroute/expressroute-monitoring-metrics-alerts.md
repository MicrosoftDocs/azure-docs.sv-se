---
title: 'Azure-ExpressRoute: övervakning, mått och aviseringar'
description: Lär dig mer om Azure ExpressRoute-övervakning, mått och aviseringar med hjälp av Azure Monitor, det enda steget att handla för alla mått, varningar, diagnostikloggar i Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091619"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med hjälp av Azure Monitor. Azure Monitor är ett stopp för alla mät värden, varningar, diagnostikloggar i hela Azure.
 
>[!NOTE]
>Det rekommenderas inte att använda **klassiska mått** .
>

## <a name="expressroute-metrics"></a>ExpressRoute mått

Om du vill visa **måtten** navigerar du till *Azure Monitor* sidan och klickar på *mått*. Om du vill visa **ExpressRoute** -mått filtrerar du efter resurs typ *ExpressRoute-kretsar*. Om du vill visa **Global Reach** mått filtrerar du efter resurs typ *ExpressRoute-kretsar* och väljer en ExpressRoute krets resurs som har Global Reach aktiverat. Om du vill visa **ExpressRoute Direct** -mått, filtrera resurs typ efter *ExpressRoute-portar*. 

När ett mått har valts tillämpas standard agg regeringen. Du kan också använda dela upp som visar måttet med olika dimensioner.

### <a name="available-metrics"></a>Tillgängliga mått

|**Mått**|**Kategori**|**Dimension (er)**|**Funktion (er)**|
| --- | --- | --- | --- |
|ARP-tillgänglighet|Tillgänglighet|<ui><li>Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ (privat/offentlig/Microsoft)</ui></li>|ExpressRoute|
|BGP-tillgänglighet|Tillgänglighet|<ui><li> Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Peering-typ (ExpressRoute)</ui></li><ui><li>Länk (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute Gateway-anslutning</ui></li>|
|BitsOutPerSecond|Trafik| <ui><li>Peering-typ (ExpressRoute)</ui></li><ui><li> Länk (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute Gateway-anslutning</ui></li>|
|CPU-användning|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Paket per sekund|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Antal vägar som annonseras till peer |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Antal vägar som har lärts från peer |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Ändrings frekvens för vägar |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Antal virtuella datorer i Virtual Network |Tillgänglighet| Ej tillämpligt |ExpressRoute Virtual Network-Gateway|
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global Reach|
|AdminState|Fysisk anslutning|Länk|ExpressRoute Direct|
|LineProtocol|Fysisk anslutning|Länk|ExpressRoute Direct|
|RxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Användning av *GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas bara om minst en global Reach anslutning har upprättats.
>

## <a name="circuits-metrics"></a>Krets mått

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in och ut-mått för alla peer kopplingar

Du kan visa mått för alla peer kopplingar på en angiven ExpressRoute-krets.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="krets mått":::

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS in och ut-mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar per sekund.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="mått per peering":::

### <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet – dela efter peer  

Du kan visa nära real tids tillgänglighet för BGP över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar primär BGP-sessionen för privat peering och den andra BGP-sessionen för privat peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-tillgänglighet per peer":::

### <a name="arp-availability---split-by-peering"></a>ARP-tillgänglighet – dela via peering  

Du kan visa nära real tids tillgänglighet för [ARP](./expressroute-troubleshooting-arp-resource-manager.md) över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar ARP-sessionen för privata peering på båda peer-datorerna, men slutfört för Microsoft-peering mellan peering. Standard agg regeringen (Average) utnyttjades över båda peer-datorerna.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-tillgänglighet per peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute direkt mått

### <a name="admin-state---split-by-link"></a>Admin-tillstånd – dela med länk

Du kan visa admin-tillstånd för varje länk i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Status för ER Direct-administratör":::

### <a name="bits-in-per-second---split-by-link"></a>Bitar i per sekund – dela med länk

Du kan visa bitarna i per sekund över båda länkarna i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER direkt bitar per sekund":::

### <a name="bits-out-per-second---split-by-link"></a>Bitar ut per sekund – dela med länk

Du kan också Visa bitarna per sekund över båda länkarna i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Antal återställnings direkt bitar per sekund":::

### <a name="line-protocol---split-by-link"></a>Rad protokoll – dela med länk

Du kan visa rad protokollet för varje länk i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Direkt anslutnings protokoll för ER":::

### <a name="rx-light-level---split-by-link"></a>RX ljus nivå – dela med länk

Du kan visa mottagnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **tar emot**) för varje port. Friska mottagnings ljus nivåer ligger vanligt vis inom intervallet-10 till 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Återställnings ljus nivå för ER direkt linje":::

### <a name="tx-light-level---split-by-link"></a>TX ljus nivå – dela med länk

Du kan visa avsändnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **överför**) för varje port. Felfria TX-ljusnivå ligger vanligt vis inom intervallet-10 till 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER direkt linje överförings ljus nivå":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network Gateway-mått

### <a name="cpu-utilization---split-instance"></a>PROCESSOR belastning – dela instans

Du kan visa processor användningen för gateway-instanserna.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU-delning":::

### <a name="packets-per-second---split-by-instance"></a>Paket per sekund – dela per instans

Du kan visa paket per sekund som passerar gatewayen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Paket per sekund-delning":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Antal vägar som annonseras till peer-Split per instans

Du kan visa antalet vägar som har annonser ATS till ExpressRoute-kretsen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Antal vägar som annonseras till peer":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Antal vägar som har lärts från peer-Split per instans

Du kan visa antalet vägar som tagits emot från ExpressRoute-kretsen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Antal vägar som har lärts från peer":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frekvens för väg ändring – dela per instans

Du kan visa hur ofta vägen ändras på gatewayen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Frekvens för vägar som har ändrats":::

### <a name="number-of-vms-in-the-virtual-network"></a>Antal virtuella datorer i Virtual Network

Du kan visa antalet virtuella datorer i det virtuella nätverket.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Antal virtuella datorer i det virtuella nätverket":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute Gateway-anslutningar i bitar/sekunder

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Gateway-anslutningar":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Aviseringar för ExpressRoute Gateway-anslutningar

1. För att konfigurera aviseringar går du till **Azure Monitor** och väljer **aviseringar**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="aviseringar":::
2. Klicka på **+ Välj mål** och välj anslutnings resurs för ExpressRoute Gateway.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="fokusera":::
3. Definiera aviserings informationen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="åtgärds grupp":::
4. Definiera och Lägg till åtgärds gruppen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Lägg till åtgärds grupp":::

## <a name="alerts-based-on-each-peering"></a>Aviseringar baserade på varje peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="varje peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurera aviseringar för aktivitets loggar på kretsar

I **aviserings villkoren** kan du välja **aktivitets logg** för signal typen och välja signalen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="aktivitets loggar":::

## <a name="additional-metrics-in-log-analytics"></a>Ytterligare mått i Log Analytics

Du kan också Visa ExpressRoute-mått genom att gå till ExpressRoute-krets-resursen och välja fliken *loggar* . För alla mät värden som du frågar innehåller utdata de kolumner som visas nedan.

|**Kolumn**|**Typ**|**Beskrivning**|
| --- | --- | --- |
|TimeGrain|sträng|PT1M (metriska värden skickas varje minut)|
|Antal|real|Vanligt vis lika med 2 (varje MSEE: N pushar ett enda mått värde varje minut)|
|Minimum|real|Det minsta värdet av de två mät värdena som pushas av de två msee|
|Maximal|real|Det maximala värdet för de två mät värdena som pushas av de två msee|
|Genomsnitt|real|Lika med (minst + max)/2|
|Totalt|real|Summan av de två mått värdena från båda msee (det huvudsakliga värdet som ska fokuseras på för det mått som har frågats)|
  
## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
* [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
* [Skapa och ändra peering-konfiguration](expressroute-howto-routing-arm.md)
* [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
