---
title: Konfigurera aviseringar i Azure VPN Gateway mått
description: Lär dig hur du använder Azure Portal för att konfigurera Azure Monitor aviseringar baserat på mått för VPN-gatewayer för virtuella nätverk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435665"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Konfigurera aviseringar för VPN Gateway mått

Den här artikeln hjälper dig att ställa in aviseringar i Azure VPN Gateway mått. Azure Monitor ger möjlighet att konfigurera aviseringar för Azure-resurser. Du kan ställa in aviseringar för virtuella nätverksgateway för typen "VPN".


|**Mått**   | **Enhet** | **Precision** | **Beskrivning** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuter| Genomsnittlig användning av kombinerad bandbredd för alla plats-till-plats-anslutningar på gatewayen.     |
|**P2SBandwidth**| Byte/s  | 1 minut  | Genomsnittlig användning av kombinerad bandbredd för alla punkt-till-plats-anslutningar på gatewayen.    |
|**P2SConnectionCount**| Antal  | 1 minut  | Antal punkt-till-plats-anslutningar på gatewayen.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuter  | Genomsnittlig bandbredds användning av tunnlar som skapats på gatewayen. |
|**TunnelEgressBytes** | Byte | 5 minuter | Utgående trafik på tunnlar som skapats på gatewayen.   |
|**TunnelEgressPackets** | Antal | 5 minuter | Antal utgående paket för tunnlar som skapats på gatewayen.   |
|**TunnelEgressPacketDropTSMismatch** | Antal | 5 minuter | Antal utgående paket som utelämnats i tunnlar som orsakas av trafik matchnings fel. |
|**TunnelIngressBytes** | Byte | 5 minuter | Inkommande trafik på tunnlar som skapats på gatewayen.   |
|**TunnelIngressPackets** | Antal | 5 minuter | Antal inkommande paket för tunnlar som skapats på gatewayen.   |
|**TunnelIngressPacketDropTSMismatch** | Antal | 5 minuter | Antal inkommande paket som har släppts i tunnlar som orsakas av trafik matchnings fel. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Konfigurera Azure Monitor aviseringar baserat på mått med hjälp av Azure Portal

I följande exempel skapas en avisering på en gateway för:

- **Mått:** TunnelAverageBandwidth
- **Villkor:** Bandbredd > 10 byte/sekund
- **Fönster:** 5 minuter
- **Aviserings åtgärd:** Åtkomst



1. Gå till resursen för den virtuella Nätverksgatewayen och välj **aviseringar** på fliken **övervakning** . Skapa sedan en ny varnings regel eller redigera en befintlig aviserings regel.

   ![Val för att skapa en varnings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Skapa")

2. Välj din VPN-gateway som resurs.

   ![Välj-knappen och VPN-gatewayen i listan över resurser](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Välj")

3. Välj ett mått som ska konfigureras för aviseringen.

   ![Markerat mått i listan över mått](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Välj")
4. Konfigurera signal logiken. Det finns tre komponenter:

    a. **Dimensioner**: om måttet har dimensioner kan du välja vissa dimensions värden så att aviseringen endast utvärderar data för dimensionen. Dessa är valfria.

    b. **Villkor**: det här är åtgärden för att utvärdera måttets värde.

    c. **Tid**: Ange granularitet för mått data och hur lång tid det tar att utvärdera aviseringen.

   ![Information om hur du konfigurerar signal logik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Välj")

5. Om du vill visa de konfigurerade reglerna väljer du **Hantera aviserings regler**.

   ![Knapp för att hantera aviserings regler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Välj")

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar aviseringar för tunnel resurs loggar finns i [Konfigurera aviseringar på VPN gateway resurs loggar](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
