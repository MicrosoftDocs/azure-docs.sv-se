---
title: 'Snabbstart: Skapa och konfigurera vägservern med hjälp av Azure PowerShell'
description: I den här snabbstarten får du lära dig hur du skapar och konfigurerar en vägserver med Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538713"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Snabbstart: Skapa och konfigurera vägservern med hjälp av Azure PowerShell

Den här artikeln hjälper dig att konfigurera Azure Route Server för peering med en virtuell nätverksinstallation (NVA) i ditt virtuella nätverk med hjälp av PowerShell. Azure Route Server lär sig vägar från den virtuella nätverksadministratören och programmerar dem på de virtuella datorerna i det virtuella nätverket. Azure Route Server annonserar även de virtuella nätverksvägarna till den virtuella nätverksadministratören. Mer information finns i [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (förhandsversion) är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Kontrollera att du har de senaste PowerShell-modulerna eller att du Azure Cloud Shell i portalen.
* Granska [tjänstgränserna för Azure Route Server.](route-server-faq.md#limitations)

## <a name="create-a-route-server"></a>Skapa en vägserver

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Innan du kan skapa en Azure Route Server behöver du ett virtuellt nätverk som värd för distributionen. Använd följande kommando för att skapa en resursgrupp och ett virtuellt nätverk. Om du redan har ett virtuellt nätverk kan du gå vidare till nästa avsnitt.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Lägg till ett undernät *med namnet RouteServerSubnet* att distribuera Azure Route Server till. Det här undernätet är endast ett dedikerat undernät för Azure Route Server. RouteServerSubnet måste vara /27 eller ett kortare prefix (till exempel /26, /25), eller så får du ett felmeddelande när du lägger till Azure Route Server.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Hämta RouteServerSubnet-ID:t. Om du vill se resurs-ID:t för alla undernät i det virtuella nätverket använder du det här kommandot:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

RouteServerSubnet-ID:t ser ut så här:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Skapa vägservern

Skapa vägservern med det här kommandot:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

Platsen måste matcha platsen för ditt virtuella nätverk. HostedSubnet är det RouteServerSubnet-ID som du fick i föregående avsnitt.

## <a name="create-peering-with-an-nva"></a>Skapa peering med en NVA

Använd följande kommando för att upprätta BGP-peering från vägservern till NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" är den virtuella nätverks-IP-adress som tilldelats den virtuella nätverksadministratören. "nva_asn" är det autonoma systemnumret (ASN) som konfigurerats i NVA. ASN kan vara ett annat 16-bitars tal än det som finns i intervallet 65515–65520. Det här intervallet av ASN:er reserveras av Microsoft.

Om du vill konfigurera peering med en annan NVA eller en annan instans av samma NVA för redundans använder du det här kommandot:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Slutför konfigurationen på NVA

För att slutföra konfigurationen på nva och aktivera BGP-sessioner behöver du IP och ASN för Azure Route Server. Du kan hämta den här informationen med hjälp av det här kommandot:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Utdata innehåller följande information:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurera vägutbyte

Om du har en ExpressRoute-gateway och en Azure VPN-gateway i samma virtuella nätverk och du vill att de ska utbyta vägar kan du aktivera utbyte av vägar på Azure Route Server.

1. Om du vill aktivera flödesutbyte mellan Azure Route Server och gatewayerna använder du följande kommando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Om du vill inaktivera vägutbyte mellan Azure Route Server och gatewayerna använder du följande kommando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Felsökning

Du kan visa de vägar som annonseras och tas emot av Azure Route Server med det här kommandot:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Azure Route Server använder du dessa kommandon för att ta bort BGP-peering och sedan ta bort vägservern. 

1. Ta bort BGP-peering mellan Azure Route Server och en NVA med det här kommandot:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Ta bort Azure Route Server med det här kommandot:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure Route Server kan du fortsätta att lära dig mer om hur Azure Route Server interagerar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute och Azure VPN-stöd](expressroute-vpn-support.md)
