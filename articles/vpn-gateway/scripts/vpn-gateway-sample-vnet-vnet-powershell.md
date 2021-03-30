---
title: Azure PowerShell-skriptexempel – Konfigurera en vnet-till-vnet-anslutning via VPN | Microsoft Docs
description: Lär dig hur du använder ett PowerShell-skript för att konfigurera en plats-till-plats-anslutning för VNet-till-VNet VPN gateway.
services: vpn-gateway
documentationcenter: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: alzam
ms.openlocfilehash: 949c2b7810bc6b4f5a80b9932427f4aa3565794e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88030375"
---
# <a name="use-powershell-to-configure-a-vnet-to-vnet-vpn-gateway-connection"></a>Använd PowerShell för att konfigurera en VPN gateway-anslutning mellan virtuella nätverk

Det här skriptet ansluter två virtuella nätverk till varandra med anslutningstypen VNet-till-VNet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables for VNET 1
  $RG1 = "TestRG1"
  $VNetName1  = "VNet1"
  $FESubName1 = "FrontEnd"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.1.0.0/16"
  $FESubPrefix1 = "10.1.0.0/24"
  $GWSubPrefix1 = "10.1.255.0/27"
  $Location1 = "EastUS"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconfig1"
  $Connection12 = "VNet1toVNet2"

# Declare variables for VNET 2  
  $RG2 = "TestRG2"
  $VNetName2  = "VNet2"
  $FESubName2 = "FrontEnd"
  $GWSubName2 = "GatewaySubnet"
  $VNetPrefix21 = "10.2.0.0/16"
  $FESubPrefix2 = "10.2.0.0/24"
  $GWSubPrefix2 = "10.2.255.0/27"
  $Location2 = "EastUS"
  $GWName2 = "VNet2GW"
  $GWIPName2 = "VNet2GWIP"
  $GWIPconfName2 = "gwipconfig2"
  $Connection21 = "VNet2toVNet1"

# Create first resource group
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create a virtual network 1
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName $RG1 `
  -Location $Location1 `
  -Name $VNetName1 `
  -AddressPrefix $VNetPrefix11

# Create a subnet configuration
Add-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for virtual network 1
$virtualNetwork1 | Set-AzVirtualNetwork

# Add a gateway subnet
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for the virtual network
$virtualNetwork1 | Set-AzVirtualNetwork

# Request a public IP address
$gwpip1= New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -VirtualNetwork $vnet1
$gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
 -Location $Location1 -IpConfigurations $gwipconfig1 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 

# Create the second resource group
New-AzResourceGroup -Name $RG2 -Location $Location2

# Create a virtual network 2
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName $RG2 `
  -Location $Location2 `
  -Name $VNetName2 `
  -AddressPrefix $VNetPrefix21

# Create a subnet configuration
Add-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for virtual network 2
$virtualNetwork2 | Set-AzVirtualNetwork

# Add a gateway subnet
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for the virtual network
$virtualNetwork2 | Set-AzVirtualNetwork

# Request a public IP address
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -VirtualNetwork $vnet2
$gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 `
 -Location $Location2 -IpConfigurations $gwipconfig2 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1

# Create the connections
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 `
-VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
 ```
 
## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat kan du ta bort resursgruppen med kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Då tas resursgrupperna bort. Det gäller även alla resurser de innehåller.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
Remove-AzResourceGroup -Name TestRG2
```
 
 
## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar information för virtuellt nätverk. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Hämtar information för virtuell nätverksgateway. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar information om undernätskonfiguration för virtuellt nätverk. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en ny IP-gatewaykonfiguration. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en VPN-gateway. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en vnet-till-vnet-anslutning. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Anger undernätskonfiguration för det virtuella nätverket. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Anger konfiguration för VPN-gatewayen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).
