---
title: 'Självstudie – Azure-ExpressRoute: Lägg till en gateway till ett VNet-Azure PowerShell'
description: Den här självstudien hjälper dig att lägga till VNet Gateway till ett redan skapat Resource Manager VNet för ExpressRoute med hjälp av Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c2fa1441f5b7d8af4d610c5341f60009e57173af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560955"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Självstudie: Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Den här självstudien hjälper dig att lägga till, ändra storlek på och ta bort en virtuell nätverks-Gateway (VNet) för ett redan befintligt VNet. Stegen för den här konfigurationen gäller för virtuella nätverk som skapades med distributions modellen Resource Manager för en ExpressRoute-konfiguration. Mer information finns i [om virtuella Nätverksgatewayen för ExpressRoute](expressroute-about-virtual-network-gateways.md).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa ett Gateway-undernät.
> - Skapa Virtual Network Gateway.

## <a name="prerequisites"></a>Förutsättningar

### <a name="configuration-reference-list"></a>Konfigurations referens lista

Stegen för den här aktiviteten använder ett VNet baserat på värdena i följande konfigurations referens lista. Ytterligare inställningar och namn beskrivs också i den här listan. Vi använder inte den här listan direkt i något av stegen, men vi lägger till variabler baserat på värdena i den här listan. Du kan kopiera listan som ska användas som referens och ersätta värdena med dina egna.

| Inställning                   | Värde                                              |
| ---                       | ---                                                |
| Virtual Network namn | *TestVNet* |    
| Virtual Network adress utrymme | *192.168.0.0/16* |
| Resursgrupp | *TestRG* |
| Subnet1 namn | *Delen* |   
| Subnet1-adressutrymme | *192.168.1.0/24* |
| Subnet1 namn | *Delen* |
| Gateway-undernätets namn | *GatewaySubnet* |    
| Adress utrymme för gateway-undernät | *192.168.200.0/26* |
| Region | *East US* |
| Gatewaynamn | *GW* |   
| Gatewayens IP-namn | *GWIP* |
| Konfigurations namn för Gateway-IP | *gwipconf* |
| Typ | *ExpressRoute* |
| Gatewayens offentliga IP-namn  | *gwpip* |

> [!IMPORTANT]
> IPv6-stöd för privat peering är för närvarande en **offentlig för hands version**. Om du vill ansluta ditt virtuella nätverk till en ExpressRoute-krets med IPv6-baserad privat peering konfigurerad kontrollerar du att ditt virtuella nätverk är dubbel stack och följer rikt linjerna som beskrivs [här](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="add-a-gateway"></a>Lägga till en gateway

1. Kör för att ansluta till Azure `Connect-AzAccount` .

1. Deklarera variablerna för den här övningen. Se till att redigera exemplet för att avspegla de inställningar som du vill använda.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Lagra det virtuella nätverks objektet som en variabel.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Lägg till ett Gateway-undernät till din Virtual Network. Gateway-undernätet måste ha namnet "GatewaySubnet". Gateway-undernätet måste vara/27 eller större (/26,/25 osv.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Om du använder ett virtuellt nätverk med dubbla stackar och planerar att använda IPv6-baserad privat peering över ExpressRoute, skapar du ett undernät med dubbla stack-gateway i stället.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Ange konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Lagra Gateway-undernätet som en variabel.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Begär en offentlig IP-adress. IP-adressen begärs innan gatewayen skapas. Du kan inte ange den IP-adress som du vill använda. den tilldelas dynamiskt. I nästa konfigurationsavsnitt ska du använda denna IP-adress. AllocationMethod måste vara dynamisk.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Skapa konfigurationen för din gateway. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. I det här steget ska du ange den konfiguration som ska användas när du skapar gatewayen. Använd följande exempel för att skapa din gateway-konfiguration.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Skapa gatewayen. I det här steget är **-GatewayType** särskilt viktigt. Du måste använda värdet **ExpressRoute**. När du har kört dessa cmdletar kan gatewayen ta 45 minuter eller mer att skapa.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Om du planerar att använda IPv6-baserad privat peering över ExpressRoute, se till att välja en AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) för **-GatewaySku**.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Kontrol lera att gatewayen har skapats
Använd följande kommandon för att kontrol lera att gatewayen har skapats:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [Gateway-SKU: er](expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU: n när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-Gateway. Om du vill ändra din gateway till en UltraPerformance-Gateway måste du först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance-Gateway. Om du vill nedgradera din gateway från en UltraPerformance-Gateway måste du först ta bort UltraPerformance-gatewayen och sedan skapa en ny Gateway.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Rensa resurser
Använd följande kommando för att ta bort gatewayen:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt VNet till en ExpressRoute-krets. 

> [!div class="nextstepaction"]
> [Länka en Virtual Network till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)