---
title: Azure PowerShell skript exempel – konfigurera en plats-till-plats-VPN
description: Använd PowerShell för att skapa en Route-baserad VPN-gateway och konfigurera VPN-enheten för att lägga till plats-till-plats-anslutning.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: sample
ms.date: 02/09/2021
ms.author: cherylmc
ms.openlocfilehash: 9778942dc24a81c839e14e095a755a280a17d9c9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379139"
---
# <a name="create-a-vpn-gateway-and-add-a-site-to-site-connection-using-powershell"></a>Skapa en VPN-gateway och lägga till en plats-till-plats-anslutning med hjälp av PowerShell

Det här skriptet skapar en vägbaserad VPN-gateway och lägger till konfiguration av plats-till-plats. För att skapa anslutningen måste du även konfigurera din VPN-enhet. Mer information finns i [Om VPN-enheter och IPsec-/IKE-parametrar för anslutningar för VPN Gateway från plats till plats](../vpn-gateway-about-vpn-devices.md).

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $RG = "TestRG1"
  $Location = "East US"
  $FESubName = "FrontEnd"
  $BESubName = "BackEnd"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.1.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
  $LNGName = "Site1"
# Create a resource group
New-AzResourceGroup -Name $RG -Location $Location
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName $RG `
  -Location $Location `
  -Name $VNetName `
  -AddressPrefix $VNetPrefix1
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name $FESubName `
  -AddressPrefix $FESubPrefix `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName $RG -Name $VNetName
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
# Create the local network gateway
New-AzLocalNetworkGateway -Name $LNGName -ResourceGroupName $RG `
 -Location $Location -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
# Configure your on-premises VPN device
# Create the VPN connection
$gateway1 = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$local = Get-AzLocalNetworkGateway -Name $LNGName -ResourceGroupName $RG
New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName $RG `
-Location $Location -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -ConnectionProtocol IKEv2 -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat kan du ta bort resursgruppen med kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Därmed tas resursgruppen och alla resurser den innehåller bort. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar information om virtuellt nätverk. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Hämtar information för virtuell nätverksgateway. |
| [Get-AzLocalNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Hämtar information för lokal nätverksgateway. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar information om undernätskonfiguration för virtuellt nätverk. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en ny IP-gatewaykonfiguration. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en VPN-gateway. |
| [New-AzLocalNetworkGateway](/powershell/module/az.network/new-azlocalnetworkgateway) | Skapar en lokal nätverksgateway. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en plats-till-plats-anslutning. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Anger undernätskonfiguration för det virtuella nätverket. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Anger konfiguration för VPN-gatewayen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).