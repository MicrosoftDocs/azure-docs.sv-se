---
title: Visa BGP-status och mått
titleSuffix: Azure VPN Gateway
description: Visa viktig BGP-relaterad information för fel sökning.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103148943"
---
# <a name="view-bgp-metrics-and-status"></a>Visa BGP-mått och status

Du kan visa BGP-mått och status med hjälp av Azure Portal eller genom att använda Azure PowerShell.

## <a name="azure-portal"></a>Azure Portal

I Azure Portal kan du Visa BGP-peers, inlärda vägar och annonserade vägar. Du kan också hämta CSV-filer som innehåller dessa data.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din virtuella nätverksgateway.
1. Under **övervakning** väljer du **BGP-peers** för att öppna sidan BGP-peers.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Skärm bild av mått i Azure Portal.":::

### <a name="learned-routes"></a>Inlärda vägar

1. Du kan visa upp till 50 inlärda vägar i portalen.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Skärm bild av inlärda vägar.":::

1. Du kan också hämta filen med inlärda vägar. Om du har fler än 50 inlärda vägar är det enda sättet att visa alla dessa genom att ladda ned och Visa. csv-filen. Hämta genom att välja **Hämta inlärda vägar**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Skärm bild av hämtning av inlärda vägar.":::
1. Sedan kan du Visa filen.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Skärm bild av hämtade inlärda vägar.":::

### <a name="advertised-routes"></a>Annonserade vägar

1. Om du vill visa annonserade vägar väljer du **...** i slutet av det nätverk som du vill visa och klickar sedan på **Visa annonserade vägar**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Skärm bild som visar hur du visar annonserade vägar." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. På sidan **vägar som annonser ATS till peer** kan du visa upp till 50 annonserade vägar.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Skärm bild av annonserade vägar.":::
1. Du kan också hämta filen med annonserade vägar. Om du har fler än 50 annonserade vägar är det enda sättet att visa alla dessa genom att ladda ned och Visa. csv-filen. Hämta genom att välja **Hämta annonserade vägar**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Skärm bild av val av hämtade annonserade vägar.":::
1. Sedan kan du Visa filen.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Skärm bild av hämtade annonserade vägar.":::

### <a name="bgp-peers"></a>BGP-peers

1. Du kan visa upp till 50 BGP-peer-datorer i portalen.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Skärm bild av BGP-peers.":::
1. Du kan också hämta BGP-peers-filen. Om du har mer än 50 BGP-peer-datorer är det enda sättet att visa alla dessa genom att ladda ned och Visa. csv-filen. Hämta genom att välja **Hämta BGP-peer-datorer** på Portal sidan.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Skärm bild av hämtning av BGP-peer-datorer.":::
1. Sedan kan du Visa filen.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Skärm bild av hämtade BGP-peer-datorer.":::

## <a name="powershell"></a>PowerShell

Använd **Get-AzVirtualNetworkGatewayBGPPeerStatus** för att visa alla BGP-peer-datorer och status.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Använd **Get-AzVirtualNetworkGatewayLearnedRoute** för att visa alla vägar som gatewayen har lärt sig via BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Använd **Get-AzVirtualNetworkGatewayAdvertisedRoute** för att visa alla vägar som gatewayen annonserar till sina peer-datorer via BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Nästa steg

Mer information om BGP finns i [Konfigurera BGP för VPN gateway](bgp-howto.md).
