---
title: 'Självstudie: länka ett VNet till en ExpressRoute-krets – Azure CLI'
description: Den här självstudien visar hur du länkar virtuella nätverk (virtuella nätverk) till ExpressRoute-kretsar med hjälp av distributions modellen för Resource Manager och Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: b9dda384e2ef30808559d10012dea2909b2af0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206942"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Självstudie: ansluta ett virtuellt nätverk till en ExpressRoute-krets med CLI

Den här självstudien visar hur du länkar virtuella nätverk (virtuella nätverk) till Azure ExpressRoute-kretsar med Azure CLI. Om du vill länka med Azure CLI måste de virtuella nätverken skapas med distributions modellen för Resource Manager. De kan antingen finnas i samma prenumeration eller ingå i en annan prenumeration. Om du vill använda en annan metod för att ansluta ditt VNet till en ExpressRoute-krets kan du välja en artikel från följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Anslut ett virtuellt nätverk i samma prenumeration till en krets
> - Anslut ett virtuellt nätverk i en annan prenumeration till en krets
> - Ändra en virtuell nätverks anslutning
> - Konfigurera ExpressRoute-FastPath

## <a name="prerequisites"></a>Förutsättningar

* Du behöver den senaste versionen av kommando rads gränssnittet (CLI). Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).
* Granska kraven [](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](howto-circuit-cli.md) och ha den krets som Aktiver ATS av anslutnings leverantören. 
  * Se till att du har konfigurerat Azures privata peering för din krets. Mer information finns i artikeln [om konfigurering av routning](howto-routing-cli.md) . 
  * Se till att Azures privata peering har kon figurer ATS. BGP-peering mellan ditt nätverk och Microsoft måste upprättas så att du kan aktivera slutpunkt-till-slutpunkt-anslutning.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [Konfigurera en virtuell nätverksgateway för ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Var noga med att använda `--gateway-type ExpressRoute` .
* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region när man använder en standard ExpressRoute-krets. 
* Ett enda VNet kan länkas till upp till fyra ExpressRoute-kretsar. Använd följande process för att skapa ett nytt anslutnings objekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda.
* Om du aktiverar ExpressRoute Premium-tillägget kan du länka virtuella nätverk utanför det politiska området för ExpressRoute-kretsen. Med Premium-tillägget kan du också ansluta fler än 10 virtuella nätverk till din ExpressRoute-krets beroende på vilken bandbredd som valts. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets

Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av exemplet. Kontrol lera att den virtuella Nätverksgatewayen har skapats och är redo för länkning innan du kör kommandot.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets

Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation. Varje avdelning inom organisationen använder sin egen prenumeration för att distribuera tjänsterna – men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. Alla virtuella nätverk delar samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration – krets ägare och krets användare

Krets ägaren är en behörig privilegie rad användare av ExpressRoute-krets resursen. Krets ägaren kan skapa auktoriseringar som kan lösas av "krets användare". Krets användare är ägare till virtuella nätverksgateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Krets användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. När en auktorisering återkallas tas alla länk anslutningar bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Så här skapar du en auktorisering**

Krets ägaren skapar en auktorisering, som skapar en auktoriseringsregel som ska användas av en krets användare för att ansluta sina virtuella nätverksgateway till ExpressRoute-kretsen. En auktorisering är endast giltig för en anslutning.

I följande exempel visas hur du skapar en auktorisering:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Svaret innehåller verifierings nyckel och status:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Granska auktoriseringar**

Krets ägaren kan granska alla auktoriseringar som utfärdats på en viss krets genom att köra följande exempel:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Lägga till auktoriseringar**

Krets ägaren kan lägga till auktoriseringar med hjälp av följande exempel:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Ta bort auktoriseringar**

Krets ägaren kan återkalla/ta bort auktoriseringarna till användaren genom att köra följande exempel:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Krets användar åtgärder

Krets användaren behöver peer-ID och en nyckel för autentisering från krets ägaren. Auktoriseringsregeln är ett GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Så här löser du en auktorisering av anslutningen**

Krets användaren kan köra följande exempel för att lösa in en länk auktorisering:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Så här släpper du en auktorisering för anslutning**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverks anslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverks anslutning. 

**Så här uppdaterar du anslutnings vikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från fler än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skicka trafik till det här prefixet kan du ändra *RoutingWeight* för en anslutning. Trafiken kommer att skickas till anslutningen med den högsta *RoutingWeight*.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute-FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om din virtuella nätverksgateway är Ultra Performance eller ErGw3AZ. FastPath förbättrar data Sök vägens prestanda, till exempel paket per sekund och anslutningar per sekund mellan ditt lokala nätverk och ditt virtuella nätverk. 

**Konfigurera FastPath på en ny anslutning**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Uppdatera en befintlig anslutning för att aktivera FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver ExpressRoute-anslutningen kan du använda `az network vpn-connection delete` kommandot för att ta bort länken mellan gatewayen och kretsen från den prenumeration där gatewayen finns.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter ett virtuellt nätverk till en krets i samma prenumeration och en annan prenumeration. Mer information om ExpressRoute-gatewayen finns i: 

> [!div class="nextstepaction"]
> [Om ExpressRoute-gatewayer för virtuella nätverk](expressroute-about-virtual-network-gateways.md)