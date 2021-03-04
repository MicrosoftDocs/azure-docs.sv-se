---
title: 'Azure-ExpressRoute: Lägg till stöd för IPv6 med hjälp av Azure PowerShell'
description: Lär dig hur du lägger till IPv6-stöd för att ansluta till Azure-distributioner med hjälp av Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: c0d153ff8125a6ef16a69aec72a27dd4b234eab5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100057"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Lägg till stöd för IPv6 för privat peering med Azure PowerShell (för hands version)

Den här artikeln beskriver hur du lägger till IPv6-stöd för att ansluta via ExpressRoute till dina resurser i Azure med hjälp av Azure PowerShell.

> [!Note]
> Den här funktionen är för närvarande tillgänglig för för hands version i [Azure-regioner med Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). ExpressRoute-kretsen kan därför skapas med valfri peering-plats, men de IPv6-baserade distributioner som den ansluter till måste finnas i en region med Tillgänglighetszoner.

## <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Registrera dig för offentlig för hands version
Innan du lägger till stöd för IPv6 måste du först registrera din prenumeration. Registrera dig genom att göra följande via Azure PowerShell:
1.  Logga in på Azure och Välj prenumerationen. Du måste göra detta för prenumerationen som innehåller din ExpressRoute-krets, samt prenumerationen som innehåller dina Azure-distributioner (om de är olika).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Begäran om att registrera din prenumeration för offentlig för hands version med följande kommando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Din begäran kommer sedan att godkännas av ExpressRoute-teamet inom 2-3 arbets dagar.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Lägg till en offentlig IPv6-peering till din ExpressRoute-krets

1. [Skapa en ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) eller Använd en befintlig krets. Hämta kretsen genom att köra kommandot **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Hämta den privata peering-konfigurationen för kretsen genom att köra **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Lägg till en privat IPv6-peering i din befintliga IPv4-privata peering-konfiguration. Ange ett par med/126 IPv6-undernät som du äger för din primära länk och sekundära länkar. Från vart och ett av dessa undernät tilldelar du den första användbara IP-adressen till routern som Microsoft använder den andra användbara IP-adressen för sin router.

    > [!Note]
    > Peer-ASN och VlanId ska matcha dem i din IPv4-konfiguration för privat peering.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. När konfigurationen har sparats hämtar du kretsen igen genom att köra kommandot **Get-AzExpressRouteCircuit** . Svaret bör se ut ungefär som i följande exempel:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Uppdatera anslutningen till ett befintligt virtuellt nätverk

Följ stegen nedan om du har en befintlig miljö av Azure-resurser i en region med Tillgänglighetszoner som du vill använda din IPv6-privata peering med.

1. Hämta det virtuella nätverk som din ExpressRoute-krets är ansluten till.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Lägg till ett IPv6-adressutrymme i det virtuella nätverket.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Lägg till IPv6-adressutrymme i Gateway-undernätet. Gatewayens IPv6-undernät ska vara/64 eller större.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Om du har en befintlig zon – redundant Gateway kör du följande för att aktivera IPv6-anslutning. Annars [skapar du den virtuella Nätverksgatewayen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) med en zon-redundant SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Skapa en anslutning till ett nytt virtuellt nätverk

Följ stegen nedan om du planerar att ansluta till en ny uppsättning Azure-resurser i en region med Tillgänglighetszoner med din IPv6-privata peering.

1. Skapa ett virtuellt nätverk med dubbla staplar med både IPv4-och IPv6-adressutrymme. Mer information finns i [skapa ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Skapa Gateway-undernätet med dubbla stackar](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Skapa den virtuella Nätverksgatewayen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) med en zon-redundant SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Om du planerar att använda FastPath använder du ErGw3AZ.

4. [Länka ditt virtuella nätverk till din ExpressRoute-krets](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Begränsningar
Även om IPv6-stöd är tillgängligt för anslutningar till distributioner i regioner med Tillgänglighetszoner, stöder den inte följande användnings fall:

* Anslutningar till distributioner i Azure via en icke-AZ ExpressRoute Gateway-SKU
* Anslutningar till distributioner i icke-AZ regioner
* Global Reach anslutningar mellan ExpressRoute-kretsar
* Användning av ExpressRoute med virtuellt WAN

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker ExpressRoute-problem finns i följande artiklar:

* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverks prestanda](expressroute-troubleshooting-network-performance.md)
