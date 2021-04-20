---
title: 'Azure ExpressRoute: Övervakning, mått och aviseringar'
description: Lär dig Azure ExpressRoute övervakning, mått och aviseringar med hjälp Azure Monitor, den enda butiken för alla mått, aviseringar och diagnostikloggar i Hela Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725790"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå övervakning, mått och aviseringar i ExpressRoute med hjälp av Azure Monitor. Azure Monitor en enda butik för alla mått, aviseringar och diagnostikloggar i hela Azure.
 
>[!NOTE]
>Användning **av klassiska mått** rekommenderas inte.
>

## <a name="expressroute-metrics"></a>ExpressRoute-mått

Om du **vill visa** mått går du *Azure Monitor* sidan och *väljer Mått.* Om du **vill visa ExpressRoute-mått** filtrerar du efter *Resurstyp ExpressRoute-kretsar*. Om du **Global Reach** visa mått filtrerar du efter *ExpressRoute-kretsar* av resurstyp och väljer en ExpressRoute-kretsresurs som har Global Reach aktiverat. Om du **ExpressRoute Direct** mått filtrerar du Resurstyp efter *ExpressRoute-portar.* 

När du har valt ett mått används standardaggregeringen. Om du vill kan du använda uppdelning, vilket visar måttet med olika dimensioner.

### <a name="aggregation-types"></a>Sammansättningstyper:

Metrics Explorer stöder SUM, MAX, MIN, AVG och COUNT som [aggregeringstyper.](../azure-monitor/essentials/metrics-charts.md#aggregation) Du bör använda den rekommenderade aggregeringstypen när du granskar insikterna för varje ExpressRoute-mått.

* Summa: Summan av alla värden som avbildas under aggregeringsintervallet. 
* Antal: Antalet mätningar som tagits under aggregeringsintervallet. 
* Genomsnitt: Medelvärdet av de måttvärden som avbildas under aggregeringsintervallet. 
* Min: Det minsta värdet som avbildas under aggregeringsintervallet. 
* Max: Det största värdet som avbildas under aggregeringsintervallet. 

### <a name="available-metrics"></a>Tillgängliga mått

|**Mått**|**Kategori**|**Dimensioner**|**Funktioner**|
| --- | --- | --- | --- |
|Tillgänglighet för ARP|Tillgänglighet|<ui><li>Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peeringtyp (privat/offentlig/Microsoft)</ui></li>|ExpressRoute|
|BGP-tillgänglighet|Tillgänglighet|<ui><li> Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peeringtyp</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Peeringtyp (ExpressRoute)</ui></li><ui><li>Länk (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute-gatewayanslutning</ui></li>|
|BitsOutPerSecond|Trafik| <ui><li>Peeringtyp (ExpressRoute)</ui></li><ui><li> Länk (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute-gatewayanslutning</ui></li>|
|CPU-användning|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network Gateway|
|Paket per sekund|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network Gateway|
|Antal vägar som annonseras till peer |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network Gateway|
|Antal inlärda vägar från peer |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network Gateway|
|Vägändringsfrekvens |Tillgänglighet| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network Gateway|
|Antal virtuella datorer i Virtual Network |Tillgänglighet| Ej tillämpligt |ExpressRoute Virtual Network Gateway|
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Peer-kretsens skeva (tjänstnyckel)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Peer-kretsens skeva (tjänstnyckel)</ui></li>|Global Reach|
|AdminState|Fysisk anslutning|Länk|ExpressRoute Direct|
|LineProtocol|Fysisk anslutning|Länk|ExpressRoute Direct|
|RxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas bara om minst en Global Reach-anslutning har upprättats.
>

## <a name="circuits-metrics"></a>Kretsmått

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits In och Out – Mått för alla peerings

Sammansättningstyp: *Genomsnitt*

Du kan visa mått över alla peerkopplingar på en viss ExpressRoute-krets.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="kretsmått":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits In och Out – Mått per peering

Sammansättningstyp: *Genomsnitt*

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar/sekund.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="mått per peering":::

### <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet – dela efter peer  

Sammansättningstyp: *Genomsnitt*

Du kan visa tillgänglighet i nära realtid för BGP (Layer-3-anslutning) mellan peer-kopplingar och peer-enheter (primära och sekundära ExpressRoute-routrar). Den här instrumentpanelen visar att den primära BGP-sessionsstatusen är upp för privat peering och den andra BGP-sessionsstatusen är nere för privat peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-tillgänglighet per peer":::

### <a name="arp-availability---split-by-peering"></a>Tillgänglighet för ARP – dela med peering  

Sammansättningstyp: *Genomsnitt*

Du kan visa tillgänglighet i nära realtid för [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (Layer-3-anslutning) mellan peer-kopplingar och peer-enheter (primära och sekundära ExpressRoute-routrar). Den här instrumentpanelen visar att ARP-sessionsstatusen för privat peering är upp för båda peer-na, men inte för Microsoft-peering för båda peer-na. Standardaggregeringen (genomsnitt) har använts mellan båda peer-arna.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Tillgänglighet för ARP per peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct mått

### <a name="admin-state---split-by-link"></a>Administratörstillstånd – Dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan visa administratörstillståndet för varje länk i ExpressRoute Direct portparet. Administratörstillståndet representerar om den fysiska porten är på eller av. Det här tillståndet krävs för att skicka trafik ExpressRoute Direct anslutningen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Direkt administratörstillstånd för ER":::

### <a name="bits-in-per-second---split-by-link"></a>Bitar per sekund – dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan visa bitarna i per sekund över båda länkarna i ExpressRoute Direct portparet. Övervaka den här instrumentpanelen för att jämföra inkommande bandbredd för båda länkarna.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER-direkt bitar i per sekund":::

### <a name="bits-out-per-second---split-by-link"></a>Bitar ut per sekund – dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan också visa bitarna ut per sekund över båda länkarna i ExpressRoute Direct portparet. Övervaka den här instrumentpanelen för att jämföra utgående bandbredd för båda länkarna.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER-direkt bitar ut per sekund":::

### <a name="line-protocol---split-by-link"></a>Line Protocol – Dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan visa radprotokollet över varje länk i ExpressRoute Direct portpar. Line Protocol anger om den fysiska länken är igång och körs ExpressRoute Direct. Övervaka den här instrumentpanelen och ställ in aviseringar för att se när den fysiska anslutningen har gått ned.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct Line Protocol":::

### <a name="rx-light-level---split-by-link"></a>Rx-ljusnivå – Dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan visa Rx-ljusnivån (den ljusnivå som ExpressRoute Direct tar **emot**) för varje port. Felfria Rx-ljusnivåer ligger vanligtvis inom intervallet -10 dBm till 0 dBm. Ställ in aviseringar för att meddelas om Rx-ljusnivån faller utanför det felfria intervallet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light Level":::

### <a name="tx-light-level---split-by-link"></a>Tx-ljusnivå – dela med länk

Sammansättningstyp: *Genomsnitt*

Du kan visa Tx-ljusnivån (den ljusnivå ExpressRoute Direct porten **överför**) för varje port. Felfria Tx-ljusnivåer faller vanligtvis inom ett intervall på -10 dBm till 0 dBm. Ange aviseringar som ska meddelas om Tx-ljusnivån faller utanför det felfria intervallet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light Level":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Mått för ExpressRoute Virtual Network Gateway

Sammansättningstyp: *Genomsnitt*

När du distribuerar en ExpressRoute-gateway hanterar Azure beräkning och funktioner för din gateway. Det finns sex tillgängliga gatewaymått för att bättre förstå gatewayens prestanda:

* CPU-användning
* Paket per sekund
* Antal vägar som annonseras till peer-peers
* Antal vägar som lärts in från peer-peers
* Frekvensen för ändrade vägar
* Antal virtuella datorer i det virtuella nätverket  

Vi rekommenderar starkt att du ställer in aviseringar för vart och ett av dessa mått så att du är medveten om när din gateway kan se prestandaproblem.

### <a name="cpu-utilization---split-instance"></a>CPU-användning – delad instans

Sammansättningstyp: *Genomsnitt*

Du kan visa processoranvändningen för varje gatewayinstans. Processoranvändningen kan öka tillfälligt under rutinunderhållet av värden, men om processoranvändningen ökar kan det tyda på att din gateway når en prestandaflaskhals. Att öka storleken på ExpressRoute-gatewayen kan lösa problemet. Ange en avisering för hur ofta CPU-användningen överskrider ett visst tröskelvärde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Skärmbild av cpu-användning – mått för delning.":::

### <a name="packets-per-second---split-by-instance"></a>Paket per sekund – uppdelat efter instans

Sammansättningstyp: *Genomsnitt*

Det här måttet visar antalet inkommande paket som skickas via ExpressRoute-gatewayen. Du kan förvänta dig att se en konsekvent dataström här om din gateway tar emot trafik från ditt lokala nätverk. Ange en avisering för när antalet paket per sekund sjunker under ett tröskelvärde som anger att din gateway inte längre tar emot trafik.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Skärmbild av paket per sekund – mått för delning.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Antal vägar som annonseras till peer – dela efter instans

Sammansättningstyp: *Antal*

Det här måttet är antalet vägar som ExpressRoute-gatewayen annonserar till kretsen. Adressutrymmena kan innehålla virtuella nätverk som är anslutna med VNet-peering och använder expressroute-fjärrgateway. Du bör förvänta dig att antalet vägar förblir konsekventa om det inte sker frekventa ändringar i adressutrymmena för det virtuella nätverket. Ange en avisering för när antalet annonserade vägar understiger tröskelvärdet för antalet adressutrymmen för virtuella nätverk som du känner till.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Skärmbild av antalet vägar som annonseras till peer.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Antal inlärda vägar från peer – dela efter instans

Sammansättningstyp: *Max*

Det här måttet visar antalet vägar som ExpressRoute-gatewayen lär sig från peer-datorer som är anslutna till ExpressRoute-kretsen. Dessa vägar kan antingen vara från ett annat virtuellt nätverk som är anslutet till samma krets eller läras in från en lokal plats. Ange en avisering för när antalet inlärda vägar sjunker under ett visst tröskelvärde. Detta kan tyda på att gatewayen ser ett prestandaproblem eller att fjärr-peer-datorer inte längre annonserar vägar till ExpressRoute-kretsen. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Skärmbild av antalet vägar som har lärts in från peer.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Ändringsfrekvens för vägar – dela efter instans

Sammansättningstyp: *Summa*

Det här måttet visar frekvensen för vägar som lärs in från eller annonseras till fjärr-peers. Du bör först undersöka dina lokala enheter för att förstå varför nätverket ändras så ofta. En hög frekvens i ruttändring kan tyda på ett prestandaproblem på ExpressRoute-gatewayen där skalning av gateway-SKU:n kan lösa problemet. Ange en avisering för ett tröskelvärde för frekvens som ska vara medveten om när din ExpressRoute-gateway ser onormala vägändringar.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Skärmbild av frekvensen för det ändrade måttet för vägar.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Antal virtuella datorer i Virtual Network

Sammansättningstyp: *Max*

Det här måttet visar antalet virtuella datorer som använder ExpressRoute-gatewayen. Antalet virtuella datorer kan innehålla virtuella datorer från peer-ade virtuella nätverk som använder samma ExpressRoute-gateway. Ange en avisering för det här måttet om antalet virtuella datorer går över ett visst tröskelvärde som kan påverka gatewayens prestanda. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Skärmbild av antalet virtuella datorer i måttet för det virtuella nätverket.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gatewayanslutningar i bitar/sekunder

Sammansättningstyp: *Genomsnitt*

Det här måttet visar bandbreddsanvändningen för en specifik anslutning till en ExpressRoute-krets.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Skärmbild av användningsstatistik för gatewayanslutningsbandbredd.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Aviseringar för ExpressRoute-gatewayanslutningar

1. Om du vill konfigurera aviseringar går **du Azure Monitor** och väljer sedan **Aviseringar.**

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="aviseringar":::
2. Välj **+Välj mål och** välj resursen ExpressRoute-gatewayanslutning.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Mål":::
3. Definiera aviseringsinformationen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="åtgärdsgrupp":::
4. Definiera och lägg till åtgärdsgruppen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="lägg till åtgärdsgrupp":::

## <a name="alerts-based-on-each-peering"></a>Aviseringar baserat på varje peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="varje peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurera aviseringar för aktivitetsloggar på kretsar

I **Aviseringsvillkor** kan du välja **Aktivitetslogg** som Signaltyp och välja Signal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="aktivitetsloggar":::

## <a name="more-metrics-in-log-analytics"></a>Fler mått i Log Analytics

Du kan också visa ExpressRoute-mått genom att gå till din ExpressRoute-kretsresurs och välja *fliken* Loggar. För mått som du frågar innehåller utdata kolumnerna nedan.

|**Kolumn**|**Typ**|**Beskrivning**|
| --- | --- | --- |
|TimeGrain|sträng|PT1M (måttvärden push-skickas varje minut)|
|Antal|real|Vanligtvis lika med 2 (varje MSEE push-lar ett enda måttvärde varje minut)|
|Minimum|real|Det minsta av de två måttvärdena som push-skickas av de två MRE:erna|
|Maximal|real|Det högsta av de två måttvärden som push-skickas av de två MRE:erna|
|Genomsnitt|real|Lika med (minimum + maximum)/2|
|Totalt|real|Summan av de två måttvärdena från båda MRE:erna (huvudvärdet att fokusera på för det mått som efterfrågas)|
  
## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
* [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
* [Skapa och ändra peering-konfiguration](expressroute-howto-routing-arm.md)
* [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
