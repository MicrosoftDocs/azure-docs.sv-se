---
title: 'PowerShell: Distribuera privat slut punkt för webb program med PowerShell'
description: Lär dig hur du använder PowerShell för att distribuera privat slut punkt för din webbapp
author: ericgre
ms.assetid: e1cc08d5-91cf-49d7-8d0a-c0e7bd2046ac
ms.topic: sample
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 97ded67b9f9daa2652b2740a21a4b7d6a0ac2bdd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87068266"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-powershell"></a>Skapa en App Service-app och distribuera privat slut punkt med PowerShell

Det här exempel skriptet skapar en app i App Service med dess relaterade resurser och distribuerar sedan en privat slut punkt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]


## <a name="sample-script"></a>Exempelskript

```azurepowershell-interactive
# Parameters
$sitename="mywebapp-$(Get-Random)"
$appserviceplanname="ASP-"+$sitename
$resourcegroupname="RG-"+$sitename
$VNetname="VNet-$(Get-Random)"
$location="francecentral"
$privateendpointsubnetname = "privateEndpointSubnet"

# Create a resource group.
New-AzResourceGroup -Name $resourcegroupname -Location $location

# Create an App Service plan in PremiumV2 tier.
$asp = New-AzAppServicePlan -Name $appserviceplanname `
-Location $location `
-ResourceGroupName $resourcegroupname `
-Tier PremiumV2 `
-NumberofWorkers 1 `
-WorkerSize Small

# Create a web app.
$webApp = New-AzWebApp -Name $sitename `
-Location $location `
-AppServicePlan $appserviceplanname `
-ResourceGroupName $resourcegroupname

# Create a Virtual Network with two subnets
$integrationsubnet = New-AzVirtualNetworkSubnetConfig -Name "integrationSubnet" `
-AddressPrefix "10.8.1.0/24"

$privateendpointsubnet = New-AzVirtualNetworkSubnetConfig -Name $privateendpointsubnetname `
-AddressPrefix "10.8.2.0/24" `
-PrivateEndpointNetworkPoliciesFlag Disabled

$virtualNetwork = New-AzVirtualNetwork -Name $VNetname `
-ResourceGroupName $resourcegroupname `
-Location $location -AddressPrefix "10.8.0.0/16" `
-Subnet $integrationsubnet,$privateendpointsubnet

# Configure the Private Endpoint
$privateEndPointConnection = New-AzPrivateLinkServiceConnection -Name "myPrivateEndpointconnection" `
-PrivateLinkServiceID $webApp.Id `
-GroupId sites

$subnet = $virtualNetwork | select -ExpandProperty subnets | Where-Object {$_.Name -eq $privateendpointsubnetname}

$privateEndpoint = New-AzPrivateEndpoint -Name "myPrivateEndpoint" `
-ResourceGroupName $resourcegroupname `
-Location $location `
-Subnet $subnet `
-PrivateLinkServiceConnection $privateEndPointConnection

# Configure the Private DNS zone
$dnsZone = New-AzPrivateDnsZone -Name "privatelink.azurewebsites.net" `
-ResourceGroupName $resourcegroupname

$dnsLink = New-AzPrivateDnsVirtualNetworkLink -Name "myDNSLink" `
-ResourceGroupName $resourcegroupname `
-ZoneName "privatelink.azurewebsites.net" `
-VirtualNetworkId $virtualNetwork.Id

$dnsConfig = New-AzPrivateDnsZoneConfig -Name "privatelink.azurewebsites.net" `
-PrivateDnsZoneId $dnsZone.ResourceId

$dnsZoneGroup = New-AzPrivateDnsZoneGroup -Name "myZoneGroup" `
-ResourceGroupName $resourcegroupname `
-PrivateEndpointName $privateEndpoint.Name `
-PrivateDnsZoneConfig $dnsConfig
```


## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en under näts konfiguration för virtuellt nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) | Skapar en konfiguration för anslutning till privat länk tjänst. |
| [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) | Skapar en privat slut punkt. |
| [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone) | Skapar en ny privat DNS-zon. |
| [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink) | Skapar en ny privat virtuell DNS-nätverks länk. |
| [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig) | Skapar konfiguration av DNS-zonen för den privata DNS-zonen. |
| [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup) | Skapar en privat DNS-zon grupp i den angivna privata slut punkten. |

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).
- Ytterligare Azure PowerShell exempel för Azure App Service Web Apps finns i [Azure PowerShells exemplen](../samples-powershell.md).
