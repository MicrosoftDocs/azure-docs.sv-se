---
title: Skapa en Azure Private Link-tjänst med Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Azure Private Link-tjänst med Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505662"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Skapa en privat länk-tjänst med hjälp av Azure PowerShell
Den här artikeln visar hur du skapar en privat länk-tjänst i Azure med hjälp av Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln den senaste versionen av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en privat länk måste du skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på *WestCentralUS* -platsen:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk för din privata länk med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myvnet* med undernät för frontend (*frontendSubnet*), Server del (*backendSubnet*), privat länk (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>Skapa interna Load Balancer
Skapa en intern Standard Load Balancer med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). I följande exempel skapas en intern Standard Load Balancer med klient delens IP-konfiguration, avsökning, regel och backend-pool som du skapade i föregående steg:

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst
Skapa en privat länk-tjänst med [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  I det här exemplet skapas en privat länk tjänst med namnet *myPLS* med standard Load Balancer i resurs gruppen med namnet *myResourceGroup*.
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>Hämta privat länk-tjänst
Hämta information om din privata länk tjänst med [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) enligt följande:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

I det här skedet har din privata länk tjänst skapats och är redo att ta emot trafiken. Observera att exemplet ovan bara visar hur du skapar en privat länk tjänst med hjälp av PowerShell.  Vi har inte konfigurerat backend-poolerna för belastningsutjämnare eller något program på backend-poolerna för att lyssna på trafiken. Om du vill se trafik flöden från slut punkt till slut punkt, rekommenderar vi starkt att du konfigurerar ditt program bakom din standard belastnings utjämning.

Härnäst ska vi visa hur tjänsten mappas till en privat slut punkt i ett annat virtuellt nätverk med hjälp av PowerShell. Återigen är exemplet begränsat till att skapa den privata slut punkten och ansluta till en privat länk-tjänst som skapats ovan. Du kan skapa Virtual Machines i Virtual Network för att skicka/ta emot trafik till den privata slut punkten för att skapa ditt scenario.

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk för din privata slut punkt med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I det här exemplet skapas ett virtuellt nätverk med namnet *vnetPE*   i resurs gruppen med namnet *myResourceGroup*:

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Skapa en privat slut punkt för att konsumera privat länk tjänst som skapats ovan i det virtuella nätverket:

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>Hämta privat slut punkt
Hämta IP-adressen för den privata slut punkten med följande `Get-AzPrivateEndpoint` :

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>Godkänn anslutningen till den privata slut punkten
Godkänn den privata slut punkts anslutningen till den privata länk tjänsten med "Godkänn-AzPrivateEndpointConnection".

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azures privata länk](private-link-overview.md)

