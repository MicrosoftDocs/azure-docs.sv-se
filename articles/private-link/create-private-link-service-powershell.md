---
title: 'Snabb start: skapa en Azure Private Link-tjänst med Azure PowerShell'
description: Lär dig hur du skapar en Azure Private Link-tjänst med Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/20/2021
ms.author: allensu
ms.openlocfilehash: 66ad5aae9f8175d154bb07a8b112dada175a205a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610071"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Skapa en privat länk-tjänst med hjälp av Azure PowerShell

Kom igång med att skapa en privat länk-tjänst som refererar till din tjänst.  Ge privat länk åtkomst till din tjänst eller resurs som distribueras bakom en Azure-Standard Load Balancer.  Användare av tjänsten har privat åtkomst från det virtuella nätverket.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```
---
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
$fe = Get-AzLoadBalancer -Name 'myLoadBalancer' | Get-AzLoadBalancerFrontendIpConfig

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

