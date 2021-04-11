---
title: 'Azure-VPN Gateway: hantering av VPN-sessioner med punkt-till-plats'
description: Den här artikeln hjälper dig att visa och koppla från punkt-till-plats-VPN-sessioner.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: b55fe0bf404ecb8a81e3fe1975dfa9f5ba5dfb06
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103359"
---
# <a name="point-to-site-vpn-session-management"></a>Hantering av VPN-sessioner med punkt-till-plats

Virtuella Azure-nätverksgateway ger ett enkelt sätt att visa och koppla från nuvarande punkt-till-plats-VPN-sessioner. Den här artikeln hjälper dig att visa och koppla från aktuella sessioner.

>[!NOTE]
>Sessionens status uppdateras var 5: e minut. Den uppdateras inte omedelbart.
>

## <a name="portal"></a>Portal

Visa och koppla från en session i portalen:

1. Navigera till VPN-gatewayen.
1. Under avsnittet **övervakning** väljer **du punkt-till-plats-sessioner**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Portal exempel":::
1. Du kan visa alla aktuella sessioner i Windowpane.
1. Välj **"..."** för den session som du vill koppla från och välj sedan **Koppla från**.

För närvarande kan du inte använda den här funktionen i portalen för VpnGw4-och VpnGw5-SKU: er. Om du har någon av dessa gateways använder du PowerShell-metoden som beskrivs i nästa avsnitt.

## <a name="powershell"></a>PowerShell

Så här visar du och kopplar från en session med PowerShell:

1. Kör följande PowerShell-kommando för att visa en lista över aktiva sessioner:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Kopiera **VpnConnectionId** för den session som du vill koppla från.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell-exempel":::
1. Kör följande kommando för att koppla från sessionen:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats-anslutningar finns i [om punkt-till-plats-VPN](point-to-site-about.md).
