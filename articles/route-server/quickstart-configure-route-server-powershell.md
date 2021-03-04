---
title: 'Snabb start: skapa och konfigurera Route server med Azure PowerShell'
description: I den här snabb starten får du lära dig hur du skapar och konfigurerar en väg server med Azure PowerShell.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 5b40cfcde7aa1771c8a4b9025d35b2dc0c728676
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039792"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Snabb start: skapa och konfigurera Route server med Azure PowerShell

Den här artikeln hjälper dig att konfigurera Azure Route Server för peer-koppling med en virtuell nätverks installation (NVA) i det virtuella nätverket med hjälp av PowerShell. Azure Route server kommer att lära sig vägar från NVA och program på de virtuella datorerna i det virtuella nätverket. Azure Route server kommer också att annonsera de virtuella nätverks vägarna till NVA. Mer information finns i [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Se till att du har de senaste PowerShell-modulerna eller så kan du använda Azure Cloud Shell i portalen.
* Granska [tjänst gränserna för Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Skapa en Route-Server

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Skapa en resurs grupp och ett virtuellt nätverk

Innan du kan skapa en Azure Route-server behöver du ett virtuellt nätverk som värd för distributionen. Använd kommandot Följ för att skapa en resurs grupp och ett virtuellt nätverk. Om du redan har ett virtuellt nätverk kan du gå vidare till nästa avsnitt.

```azurepowershell-interactive
New-AzResourceGroup –Name “RouteServerRG” -Location “West US”
New-AzVirtualNetwork –ResourceGroupName “RouteServerRG -Location “West US” -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Lägg till ett undernät med namnet *RouteServerSubnet* för att distribuera Azure Route-servern till. Det här under nätet är endast ett dedikerat undernät för Azure Route Server. RouteServerSubnet måste vara/27 eller ett kortare prefix (till exempel/26,/25), eller så visas ett fel meddelande när du lägger till Azure Route-servern.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “myVirtualNetwork” - ResourceGroupName “RouteServerRG”
    Add-AzVirtualNetworkSubnetConfig –Name “RouteServerSubnet” -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Hämta RouteServerSubnet-ID: t. Om du vill se resurs-ID för alla undernät i det virtuella nätverket använder du följande kommando:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “vnet_name” -ResourceGroupName “
    $vnet.Subnets
    ```

RouteServerSubnet-ID: t ser ut ungefär så här:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Skapa en Route-Server

Skapa en Route-server med det här kommandot:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US” -HostedSubnet “RouteServerSubnet_ID”
```

Platsen måste matcha platsen för det virtuella nätverket. HostedSubnet är RouteServerSubnet-ID: t som du fick i föregående avsnitt.

## <a name="create-peering-with-an-nva"></a>Skapa peering med en NVA

Använd följande kommando för att upprätta BGP-peering från väg servern till NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" är den virtuella nätverks-IP som tilldelats till NVA. "nva_asn" är det autonoma system numret (ASN) som kon figurer ATS i NVA. ASN kan vara andra 16-bitarsnummer än de som finns i intervallet 65515-65520. Detta ASN: er är reserverat för Microsoft.

Använd följande kommando för att konfigurera peering med olika NVA eller en annan instans av samma NVA för redundans:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Slutför konfigurationen av NVA

För att slutföra konfigurationen på NVA och aktivera BGP-sessioner behöver du IP och ASN för Azure Route Server. Du kan hämta den här informationen med hjälp av det här kommandot:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Utdata har följande information:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurera Route Exchange

Om du har en ExpressRoute-gateway och en Azure VPN-gateway i samma VNet och du vill att de ska utbyta vägar, kan du aktivera routning av Exchange på Azure Route-servern.

1. Om du vill aktivera Route Exchange mellan Azure Route Server och gatewayen använder du följande kommando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Om du vill inaktivera Route Exchange mellan Azure Route Server och gatewayen använder du följande kommando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Felsökning

Du kan visa de vägar som annonseras och tas emot av Azure Route server med det här kommandot:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up"></a>Rensa

Om du inte längre behöver Azure Route-servern använder du dessa kommandon för att ta bort BGP-peering och tar sedan bort väg servern. 

1. Ta bort BGP-peering mellan Azure Route Server och en NVA med det här kommandot:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName “nva_name” -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Ta bort Azure Route server med det här kommandot:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure Route Server kan du fortsätta med att lära dig hur Azure Route Server samverkar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure-ExpressRoute och Azure VPN-support](expressroute-vpn-support.md)
