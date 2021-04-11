---
title: 'Självstudie: länka ett VNet till en ExpressRoute-krets – Azure PowerShell'
description: I den här självstudien får du en översikt över hur du länkar virtuella nätverk (virtuella nätverk) till ExpressRoute-kretsar med hjälp av distributions modellen för Resource Manager och Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: eda575e0f29abbe18750a24fbfc212a9d84819cf
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110367"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Självstudie: ansluta ett virtuellt nätverk till en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln hjälper dig att länka virtuella nätverk (virtuella nätverk) till Azure ExpressRoute-kretsar med hjälp av distributions modellen för Resource Manager och PowerShell. Virtuella nätverk kan antingen finnas i samma prenumeration eller del av en annan prenumeration. Den här artikeln visar också hur du uppdaterar en virtuell nätverks länk.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region när man använder en standard ExpressRoute-krets. 

* Ett enda VNet kan länkas till upp till 16 ExpressRoute-kretsar. Följ stegen i den här artikeln för att skapa ett nytt anslutnings objekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Om du aktiverar ExpressRoute Premium-tillägget kan du länka virtuella nätverk utanför det politiska området för ExpressRoute-kretsen. Med Premium-tillägget kan du också ansluta fler än 10 virtuella nätverk till din ExpressRoute-krets beroende på vilken bandbredd som valts. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Anslut ett virtuellt nätverk i samma prenumeration till en krets
> - Anslut ett virtuellt nätverk i en annan prenumeration till en krets
> - Ändra en virtuell nätverks anslutning
> - Konfigurera ExpressRoute-FastPath

## <a name="prerequisites"></a>Förutsättningar

* Granska kraven [](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och ha den krets som Aktiver ATS av anslutnings leverantören. 
  * Se till att du har konfigurerat Azures privata peering för din krets. Mer information finns i artikeln [om konfigurering av routning](expressroute-howto-routing-arm.md) . 
  * Se till att Azures privata peering konfigureras och upprättar BGP-peering mellan ditt nätverk och Microsoft för anslutning från slut punkt till slut punkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType "ExpressRoute", inte VPN.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets
Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrol lera att den virtuella Nätverksgatewayen har skapats och är redo för länkning innan du kör cmdleten:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation. Varje avdelning inom organisationen använder sin egen prenumeration för att distribuera tjänsterna – men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings-och bandbredds avgifter för ExpressRoute-kretsen kommer att användas för Prenumerationens ägare. Alla virtuella nätverk delar samma bandbredd.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Anslutning mellan prenumerationer":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration – krets ägare och krets användare

Krets ägaren är en behörig privilegie rad användare av ExpressRoute-krets resursen. Krets ägaren kan skapa auktoriseringar som kan lösas av "krets användare". Krets användare är ägare till virtuella nätverksgateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Krets användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. Att återkalla ett auktoriserings resultat i alla länk anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Så här skapar du en auktorisering**

Krets ägaren skapar en auktorisering, som skapar en auktoriseringsregel som ska användas av en krets användare för att ansluta sina virtuella nätverksgateway till ExpressRoute-kretsen. En auktorisering är endast giltig för en anslutning.

Följande cmdlet-kodfragment visar hur du skapar en auktorisering:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Svaret på föregående kommandon innehåller verifierings nyckel och status:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Granska auktoriseringar**

Krets ägaren kan granska alla auktoriseringar som utfärdats på en viss krets genom att köra följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Lägga till auktoriseringar**

Krets ägaren kan lägga till auktoriseringar med hjälp av följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Ta bort auktoriseringar**

Krets ägaren kan återkalla/ta bort auktoriseringarna till användaren genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Krets användar åtgärder

Krets användaren behöver peer-ID och en nyckel för autentisering från krets ägaren. Auktoriseringsregeln är ett GUID.

Peer-ID kan kontrol leras från följande kommando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Så här löser du en auktorisering av anslutningen**

Krets användaren kan köra följande cmdlet för att lösa in en länk auktorisering:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Så här släpper du en auktorisering för anslutning**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverks anslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverks anslutning. 

**Så här uppdaterar du anslutnings vikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från fler än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skicka trafik till det här prefixet kan du ändra *RoutingWeight* för en anslutning. Trafiken kommer att skickas till anslutningen med den högsta *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute-FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om din virtuella nätverksgateway är Ultra Performance eller ErGw3AZ. FastPath förbättrar data Sök vägens prestanda, till exempel paket per sekund och anslutningar per sekund mellan ditt lokala nätverk och ditt virtuella nätverk. 

**Konfigurera FastPath på en ny anslutning**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Uppdatera en befintlig anslutning för att aktivera FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver ExpressRoute-anslutningen kan du använda `Remove-AzVirtualNetworkGatewayConnection` kommandot för att ta bort länken mellan gatewayen och kretsen från den prenumeration där gatewayen finns.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i vanliga frågor och svar om ExpressRoute.

> [!div class="nextstepaction"]
> [Vanliga frågor och svar för ExpressRoute](expressroute-faqs.md)
