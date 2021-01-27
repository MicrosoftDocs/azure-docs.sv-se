---
title: Plats-till-plats-VPN-anslutningar via privat ExpressRoute-peering
description: Den här artikeln hjälper dig att aktivera VPN för plats-till-plats via ExpressRoute privata peering för att kryptera trafik.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880533"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Konfigurera en plats-till-plats-VPN-anslutning via privat ExpressRoute-peering

Du kan konfigurera en plats-till-plats-VPN-anslutning till en virtuell nätverksgateway via en ExpressRoute privat peering med hjälp av en RFC 1918-IP-adress. Den här konfigurationen ger följande fördelar:

* Trafik över privat peering är krypterad.

* Punkt-till-plats-användare som ansluter till en virtuell nätverksgateway kan använda ExpressRoute (via plats-till-plats-tunneln) för att komma åt lokala resurser.

>[!NOTE]
>Den här funktionen stöds bara i zoner-redundanta gatewayer. Till exempel VpnGw1AZ, VpnGw2AZ osv.
>

För att slutföra den här konfigurationen kontrollerar du att du uppfyller följande krav:

* Du har en fungerande ExpressRoute-krets som är länkad till det virtuella nätverk där VPN-gatewayen är (eller kommer att skapas).

* Du kan komma åt resurser över RFC1918 (privat) IP i VNet över ExpressRoute-kretsen.

## <a name="routing"></a><a name="routing"></a>Routning

**Bild 1** visar ett exempel på en VPN-anslutning via privat ExpressRoute-peering. I det här exemplet visas ett nätverk i det lokala nätverket som är anslutet till Azure Hub VPN-gateway över ExpressRoute-privat peering. En viktig aspekt av den här konfigurationen är routningen mellan de lokala nätverken och Azure över både ExpressRoute-och VPN-sökvägar.

Bild 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Bild 1":::

Att upprätta anslutningar är enkelt:

1. Upprätta ExpressRoute-anslutningar med en ExpressRoute-krets och privat peering.

1. Upprätta en VPN-anslutning med hjälp av stegen i den här artikeln.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Trafik från lokala nätverk till Azure

För trafik från lokala nätverk till Azure annonseras Azure-prefixen via både ExpressRoute privata peering BGP och VPN BGP. Resultatet är två nätverks vägar (sökvägar) gentemot Azure från lokala nätverk:

• En nätverks väg över IPsec-skyddad sökväg.

• En nätverks väg direkt över ExpressRoute utan IPsec-skydd.

Om du vill använda kryptering för kommunikationen måste du se till att för det VPN-anslutna nätverket i **bild 1**, kommer Azure vägar via den lokala VPN-gatewayen att föredras över den direkta ExpressRoute-sökvägen.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Trafik från Azure till lokala nätverk

Samma krav gäller för trafiken från Azure till lokala nätverk. För att säkerställa att IPsec-sökvägen föredras över den direkta ExpressRoute-sökvägen (utan IPsec) har du två alternativ:

• **Annonsera mer detaljerade prefix på VPN BGP-sessionen för det VPN-anslutna nätverket**. Du kan annonsera ett större intervall som omfattar VPN-anslutna nätverk via ExpressRoute privat peering, och sedan mer exakta intervall i VPN BGP-sessionen. Annonsera till exempel 10.0.0.0/16 över ExpressRoute och 10.0.1.0/24 via VPN.

• **Annonsera åtskilda prefix för VPN-och ExpressRoute**. Om de VPN-anslutna nätverks intervallen är åtskilda från andra ExpressRoute anslutna nätverk, kan du annonsera prefixen i VPN-och ExpressRoute BGP-sessionerna. Meddela till exempel 10.0.0.0/24 över ExpressRoute och 10.0.1.0/24 via VPN.

I båda dessa exempel skickar Azure trafik till 10.0.1.0/24 via VPN-anslutningen i stället för direkt över ExpressRoute utan VPN-skydd.

>[!Warning]
>Om du annonserar samma prefix över både ExpressRoute-och VPN-anslutningar kommer >Azure att använda ExpressRoute-sökvägen direkt utan VPN-skydd.
>

## <a name="portal-steps"></a><a name="portal"></a>Portal steg

1. Konfigurera en plats-till-plats-anslutning. Anvisningar finns i artikeln [plats-till-plats-konfiguration](./tutorial-site-to-site-portal.md) . Se till att välja en zon – redundant Gateway-SKU för gatewayen. 

   Zoner – redundanta SKU: er har "AZ" i slutet av SKU: n. Till exempel **VpnGw1AZ**. Zon-redundanta gatewayer är bara tillgängliga i regioner där tillgänglighets zon tjänsten är tillgänglig. Information om de regioner där vi stöder tillgänglighets zoner finns i [regioner som har stöd för tillgänglighets zoner](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Gateway privata IP-adresser":::
1. Aktivera privata IP-adresser på gatewayen. Välj **konfiguration** och ställ sedan in **Gateway privata IP-adresser** till **aktive rad**. Välj **Spara** för att spara ändringarna.
1. På sidan **Översikt** väljer du **Visa mer** om du vill visa den privata IP-adressen. Anteckna informationen och Använd den senare i konfigurations stegen.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Översiktssidan" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Om du vill aktivera **Använd Azures privata IP-adress** på anslutningen väljer du  **konfiguration**. Ange **Använd Azures privata IP-adress** till **aktive rad** och välj sedan **Spara**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Gateway privat IP-aktive rad":::
1. Pinga den privata IP-adress som du skrev ned i steg 3 i brand väggen. Den privata IP-adressen ska kunna kontaktas via ExpressRoute privata peering.
1. Använd den här privata IP-adressen som fjärr-IP i den lokala brand väggen för att upprätta plats-till-plats-tunneln över ExpressRoute privata peering.

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell-steg

1. Konfigurera en plats-till-plats-anslutning. Anvisningar finns i artikeln [Konfigurera en plats-till-plats-VPN](./tutorial-site-to-site-portal.md) . Se till att välja en zon – redundant Gateway-SKU för gatewayen. Zoner – redundanta SKU: er har "AZ" i slutet av SKU: n. Till exempel VpnGw1AZ.
1. Ange att flaggan ska använda den privata IP-adressen på gatewayen med hjälp av följande PowerShell-kommandon:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Du bör se en offentlig och en privat IP-adress. Anteckna IP-adressen under avsnittet "TunnelIpAddresses" i utdata. Du kommer att använda den här informationen i ett senare steg.
1. Ange att anslutningen ska använda den privata IP-adressen med hjälp av följande PowerShell-kommando:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Pinga den privata IP-adress som du skrev ned i steg 2 i brand väggen. Den bör kunna kontaktas via ExpressRoute privata peering.
1. Använd den här privata IP-adressen som fjärr-IP i den lokala brand väggen för att upprätta plats-till-plats-tunneln över ExpressRoute privata peering.

## <a name="next-steps"></a>Nästa steg

Mer information om VPN Gateway finns i [Vad är VPN gateway?](vpn-gateway-about-vpngateways.md)