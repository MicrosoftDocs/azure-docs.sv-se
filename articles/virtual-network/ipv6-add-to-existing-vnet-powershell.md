---
title: Uppgradera ett IPv4-program till IPv6 i Azure Virtual Network – PowerShell
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar IPv6-adresser till ett befintligt program i Azure Virtual Network med Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9d2ff583b032ff1f9aa5dbc9706ea6c981fc7265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009984"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Uppgradera ett IPv4-program till IPv6 i Azure Virtual Network – PowerShell

Den här artikeln visar hur du lägger till IPv6-anslutning till ett befintligt IPv4-program i ett virtuellt Azure-nätverk med en Standard Load Balancer och en offentlig IP-adress. Uppgradering på plats innehåller:
- IPv6-adressutrymme för det virtuella nätverket och under nätet
- en Standard Load Balancer med konfigurationer för både IPv4-och IPV6-frontend
- Virtuella datorer med nätverkskort som har både en IPv4 + IPv6-konfiguration
- IPv6 offentlig IP-adress så att belastningsutjämnaren har Internet-riktad IPv6-anslutning

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 6.9.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="prerequisites"></a>Förutsättningar

I den här artikeln förutsätter vi att du har distribuerat en Standard Load Balancer enligt beskrivningen i [snabb start: skapa en standard Load Balancer-Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md).

## <a name="retrieve-the-resource-group"></a>Hämta resurs gruppen

Innan du kan skapa ett virtuellt nätverk med dubbla staplar måste du hämta resurs gruppen med [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Skapa en IPv6-IP-adress

Skapa en offentlig IPv6-adress med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för din standard Load Balancer. I följande exempel skapas en offentlig IPv6-IP-adress med namnet *PublicIP_v6* i resurs gruppen *myResourceGroupSLB* :

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurera frontend-frontend för belastnings utjämning

Hämta den befintliga belastnings Utjämnings konfigurationen och Lägg sedan till den nya IPv6-IP-adressen med hjälp av [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) enligt följande:

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurera backend-poolen för belastningsutjämnare

Skapa backend-poolen på den lokala kopian av belastnings Utjämnings konfigurationen och uppdatera den aktiva belastningsutjämnaren med den nya konfigurationen för konfiguration av backend-poolen enligt följande:

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Konfigurera regler för lastbalanserare
Hämta den befintliga klient delen för belastningsutjämnare och konfiguration av backend-poolen och Lägg sedan till nya regler för belastnings utjämning med [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Lägg till IPv6-adressintervall

Lägg till IPv6-adressintervall till det virtuella nätverket och under nätet som är värd för de virtuella datorerna enligt följande:

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>Lägg till IPv6-konfiguration till NIC

Konfigurera alla nätverkskort för virtuella datorer med en IPv6-adress med hjälp av [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) enligt följande:

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa ett virtuellt IPv6-nätverk med dubbla stackar i Azure Portal

Du kan visa det virtuella IPv6-nätverket med dubbla stackar i Azure Portal på följande sätt:
1. Skriv *myVnet* i portalens Sök fält.
2. När **myVnet** visas i Sök resultaten väljer du det. Då startas **översikts** sidan för det virtuella nätverket med dubbla stackar med namnet *myVNet*. Det virtuella nätverket med dubbla stackar visar de tre nätverkskorten med både IPv4-och IPv6-konfigurationer som finns i det dubbla stack-undernätet med namnet *mitt undernät*.

  ![IPv6-virtuellt nätverk med dubbla stackar i Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du uppdaterat en befintlig Standard Load Balancer med en IP-konfiguration för IPv4-frontend till en konfiguration med dubbla stackar (IPv4 och IPv6). Du har också lagt till IPv6-konfigurationer till nätverkskorten för de virtuella datorerna i backend-poolen och till de Virtual Network som är värdar för dem. Mer information om IPv6-stöd i Azure Virtual Networks finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
