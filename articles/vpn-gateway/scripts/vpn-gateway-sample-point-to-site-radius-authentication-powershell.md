---
title: Azure PowerShell skript exempel – konfigurera P2S VPN med RADIUS-autentisering
titleSuffix: Azure VPN Gateway
description: PowerShell-exempel för att konfigurera punkt-till-plats-VPN med RADIUS username/Password Authentication
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: sample
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 0a89ce2b73ca9da64f86c81ea00b1f46ea18f996
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385820"
---
# <a name="create-a-vpn-gateway-with-p2s-radius-authentication---powershell-script-sample"></a>Skapa en VPN-gateway med P2S RADIUS-autentisering – PowerShell-skript exempel

Det här skript exemplet skapar en Route-baserad VPN-gateway och lägger till punkt-till-plats-konfiguration med RADIUS username/Password Authentication.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $VNetPrefix1 = "10.1.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $RSAddress = "10.51.0.15"

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
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Create a secure string for the RADIUS secret
$Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"

# Add the VPN client address pool and the RADIUS server information
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
 -VpnClientAddressPool $VPNClientAddressPool -VpnClientProtocol @( "SSTP", "IkeV2" ) `
 -RadiusServerAddress $RSAddress -RadiusServerSecret $Secure_Secret
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
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar information om undernätskonfiguration för virtuellt nätverk. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en ny IP-gatewaykonfiguration. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en VPN-gateway. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Anger undernätskonfiguration för det virtuella nätverket. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Anger konfiguration för VPN-gatewayen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).
