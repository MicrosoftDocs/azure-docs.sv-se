---
title: Distribuera Azure-brandväggen med flera offentliga IP-adresser med hjälp av Azure PowerShell
description: Distribuera en brand vägg med flera offentliga IP-adresser för att skydda en virtuell hubb
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87007018"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Distribuera en Azure-brandvägg med flera offentliga IP-adresser

Om du vill skydda en virtuell hubb med Azure-brandväggen kan du distribuera brand väggen med flera offentliga IP-adresser med hjälp av Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Använd följande Azure PowerShell exempel för att distribuera en Azure-brandvägg med flera offentliga IP-adresser för att skydda en virtuell hubb.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>Uppdatera en offentlig IP-adress

Du kan använda Azure PowerShell för att uppdatera en offentlig IP-adress för en Azure-brandvägg. I följande exempel tar bort en offentlig IP-adress från en brand vägg. Den börjar med tre offentliga IP-adresser.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Nästa steg

[Vad är en skyddad virtuell hubb?](secured-virtual-hub.md)