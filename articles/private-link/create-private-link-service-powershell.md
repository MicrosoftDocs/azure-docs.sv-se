---
title: 'Snabb start: skapa en Azure Private Link-tjänst med Azure PowerShell'
description: Lär dig hur du skapar en Azure Private Link-tjänst med Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: d48903a05a4e9b530dcd3e83e0c14c37dcc74797
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757533"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Skapa en privat länk-tjänst med hjälp av Azure PowerShell

Kom igång med att skapa en privat länk-tjänst som refererar till din tjänst.  Ge privat länk åtkomst till din tjänst eller resurs som distribueras bakom en Azure-Standard Load Balancer.  Användare av tjänsten har privat åtkomst från det virtuella nätverket.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

I det här avsnittet ska du skapa ett virtuellt nätverk och ett internt Azure Load Balancer.

### <a name="virtual-network"></a>Virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk och ett undernät som är värd för belastningsutjämnaren som har åtkomst till din privata länk tjänst.

* Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

* Skapa en frontend IP-adress med [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) för klient DELENS IP-pool. Den här IP-adressen tar emot inkommande trafik i belastningsutjämnaren

* Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) för trafik som skickas från belastningsutjämnarens klient del. Den här poolen är den plats där dina virtuella backend-datorer distribueras.

* Skapa en hälso avsökning med [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) som avgör hälso tillståndet för VM-instanser i Server delen.

* Skapa en belastnings Utjämnings regel med [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) som definierar hur trafiken distribueras till de virtuella datorerna.

* Skapa en offentlig belastningsutjämnare med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst

I det här avsnittet skapar du en privat länk-tjänst som använder standard Azure Load Balancer som skapades i föregående steg.

* Skapa IP-konfigurationen för den privata länk tjänsten med [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig).

* Skapa en privat länk-tjänst med [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice).

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

Din privata länk tjänst skapas och kan ta emot trafik. Om du vill se trafikflöden konfigurerar du ditt program bakom din standard belastningsutjämnare.

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt. Ett virtuellt nätverk innehåller den privata slut punkten för den privata länk tjänsten. Det här virtuella nätverket innehåller de resurser som kommer att ha åtkomst till din privata länk tjänst.

### <a name="create-private-endpoint-virtual-network"></a>Skapa privat slut punkt för virtuellt nätverk

* Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>Skapa slut punkt och anslutning

* Använd [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) för att placera konfigurationen av den privata länk tjänsten som du skapade tidigt i en variabel för senare användning.

* Använd [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) för att skapa anslutnings konfigurationen.

* Använd [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) för att skapa slut punkten.



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>Godkänn anslutningen till den privata slut punkten

I det här avsnittet godkänner du den anslutning som du skapade i föregående steg.

* Använd [Godkänn-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) för att godkänna anslutningen.

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>IP-adress för privat slut punkt

I det här avsnittet hittar du IP-adressen för den privata slut punkt som motsvarar belastningsutjämnaren och tjänsten privata länkar.

* Använd [Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) för att hämta IP-adressen.

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen, belastningsutjämnaren och återstående resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapade ett virtuellt nätverk och internt Azure Load Balancer.
* En privat länk tjänst har skapats

Om du vill veta mer om den privata Azure-slutpunkten fortsätter du till:
> [!div class="nextstepaction"]
> [Snabb start: skapa en privat slut punkt med hjälp av Azure PowerShell](create-private-endpoint-powershell.md)

