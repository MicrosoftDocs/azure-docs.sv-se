---
title: Så här konfigurerar du OpenVPN på Azure VPN Gateway
description: Lär dig hur du använder PowerShell för att aktivera OpenVPN-protokoll på Azure VPN Gateway för en punkt-till-plats-miljö.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 34f24b8fbdb28e1b1f73e9db428c510d3f4661ce
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804855"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurera OpenVPN för Azure-punkt-till-plats-VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN® protokoll** på Azure VPN gateway. Du kan använda antingen portalen eller PowerShell-instruktionerna.

## <a name="prerequisites"></a>Förutsättningar

* Artikeln förutsätter att du redan har en fungerande punkt-till-plats-miljö. Om du inte gör det skapar du en med någon av följande metoder.

  * [Portal – skapa punkt-till-plats](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – skapa punkt-till-plats](vpn-gateway-howto-point-to-site-rm-ps.md)

* Kontrol lera att VPN-gatewayen inte använder den grundläggande SKU: n. Bas-SKU: n stöds inte för OpenVPN.

## <a name="portal"></a>Portal

1. I portalen navigerar du till din **virtuella nätverksgateway – > punkt-till-plats-konfiguration**.
1. För **tunnel typ** väljer du **OpenVPN (SSL)** eller **IKEv2 och OpenVPN (SSL)** i list rutan.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Välj OpenVPN SSL i list rutan":::
1. Spara ändringarna och fortsätt med **Nästa steg**.

Aktivera OpenVPN på din gateway.

1. Aktivera OpenVPN på din gateway med hjälp av följande exempel:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Fortsätt med **Nästa steg**.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar klienter för OpenVPN finns i [konfigurera OpenVPN-klienter](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
