---
title: Om Azure VPN Gateway
description: Lär dig mer om vad en VPN Gateway är och hur du använder en VPN Gateway för att ansluta till IPsec IKE-plats-till-plats-, VNet-till-VNet-och punkt-till-plats VPN-virtuella nätverk.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperf-fy21q1
ms.openlocfilehash: c19e1156f897873defaba4cebaabb904d034ead3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032753"
---
# <a name="what-is-vpn-gateway"></a>Vad är en VPN-gateway?

En VPN-gateway är en viss typ av virtuell nätverksgateway som används till att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via internet. Du kan också använda en VPN-gateway till att skicka krypterad trafik mellan virtuella Azure-nätverk via Microsofts nätverk. Ett virtuellt nätverk kan endast ha en VPN-gateway. Du kan dock skapa flera anslutningar till samma VPN-gateway. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Vad är en virtuell nätverksgateway?

En virtuell nätverksgateway består av två eller flera virtuella datorer som distribueras till ett speciellt undernät som du skapar som gateway- *undernätet*. Virtuella nätverksgateway VM: ar innehåller routningstabeller och kör vissa gateway-tjänster. De här virtuella datorerna skapas när du skapar den virtuella Nätverksgatewayen. Du kan inte konfigurera de virtuella datorerna som ingår i den virtuella Nätverksgatewayen.

När du konfigurerar en virtuell nätverksgateway konfigurerar du en inställning som anger typen av gateway. Gateway-typen avgör hur den virtuella Nätverksgatewayen ska användas och de åtgärder som gatewayen tar. Gateway-typen VPN anger att den typ av virtuell nätverksgateway som skapas är en VPN-gateway. Detta skiljer den från en ExpressRoute-Gateway, som använder en annan typ av gateway. Ett virtuellt nätverk kan ha två virtuella nätverksgateway; en VPN-gateway och en ExpressRoute-Gateway. Se [Gatewaytyper](vpn-gateway-about-vpn-gateway-settings.md#gwtype) för mer information.

Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway. När du skapar en virtuell nätverksgateway distribueras de virtuella gatewaydatorerna till gatewayundernätet och konfigureras med de inställningar du anger. När du har skapat en VPN-gateway kan du skapa en VPN-tunnelanslutning med IPsec/IKE mellan denna VPN-gateway och en annan VPN-gateway (VNet-till-VNet), eller en VPN-tunnel med IPsec/IKE mellan VPN-gatewayen och en lokal VPN-enhet (Plats-till-plats). Du kan också skapa en punkt-till-plats-VPN-anslutning (VPN över OpenVPN, IKEv2 eller SSTP), vilket gör att du kan ansluta till ditt virtuella nätverk från en annan plats, till exempel från en konferens eller hemifrån.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Konfigurera en VPN gateway

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta av resurserna kan konfigureras separat, men vissa resurser måste konfigureras i en viss ordning.

### <a name="design"></a><a name="diagrams"></a>Design

Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Du måste bestämma vilken konfiguration som passar bäst för dina behov. Till exempel har punkt-till-plats-, plats-till-plats-och sambefintliga ExpressRoute/plats-till-plats-anslutningar alla olika instruktioner och konfigurations krav. Information om hur du utformar och visar diagram för anslutnings miljön finns i [design](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Planeringstabell

Tabellen nedan kan hjälpa dig att bestämma det bästa anslutningsalternativet för din lösning.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Inställningar

De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning. Information om enskilda resurser och inställningar för VPN Gateway finns [Om inställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Den här artikeln innehåller information om gatewaytyper, gateway-SKU:er, VPN-typer, anslutningstyper, gatewayundernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

### <a name="deployment-tools"></a><a name="tools"></a>Distributionsverktyg

Du kan börja skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure Portal. Du kan senare välja att byta till ett annat verktyg, som PowerShell, för att konfigurera ytterligare resurser eller ändra befintliga resurser om det behövs. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er

När du skapar en virtuell nätverksgateway anger du vilken gateway-SKU som du vill använda. Välj den SKU som uppfyller dina krav baserat på typerna av arbetsbelastning, dataflöden, funktioner och serviceavtal.

* Mer information om Gateway-SKU: er, inklusive funktioner som stöds, produktions-och dev-test och konfigurations steg, finns i artikeln [VPN Gateway inställningar-Gateway SKU: er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) .
* Äldre SKU-information finns i [arbeta med äldre SKU: er](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU:er efter tunnel, anslutning och dataflöde

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Tillgänglighetszoner

VPN-gatewayer kan distribueras i Azure-tillgänglighetszoner. Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå. Se [om zoner – redundanta virtuella nätverksgateway i Azure-tillgänglighetszoner](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Prissättning

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Se [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er för VPN Gateway.

## <a name="faq"></a><a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor om VPN Gateway finns i [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Vad är det senaste?

Prenumerera på RSS-flödet och Visa de senaste VPN Gateway funktions uppdateringarna på sidan [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) .

## <a name="next-steps"></a>Nästa steg

- Mer information finns i avsnittet [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md).
- Läs [Prenumerations- och tjänstbegränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
