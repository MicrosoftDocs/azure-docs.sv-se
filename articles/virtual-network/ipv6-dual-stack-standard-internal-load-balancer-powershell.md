---
title: Distribuera ett IPv6-program med dubbla stackar med interna standard Load Balancer i Azure – PowerShell
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar en IPv6-app med dubbla stackar med standard intern Load Balancer i Azure Virtual Network med Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220838"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Distribuera ett IPv6-program med dubbla stackar med interna standard Load Balancer i Azure – PowerShell (för hands version)

Den här artikeln visar hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) i Azure som innehåller ett virtuellt nätverk och undernät med dubbla stackar, en intern standard Load Balancer med dubbla (IPv4 + IPv6) frontend-konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, nätverks säkerhets grupp och offentliga IP-adresser.

Proceduren för att skapa en IPv6-kompatibel intern Load Balancer är nästan identisk med processen för att skapa en Internet-riktad IPv6-Load Balancer som beskrivs [här](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). De enda skillnaderna för att skapa en intern belastningsutjämnare finns i klient konfigurationen som illustreras i PowerShell-exemplet nedan:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

De ändringar som görs ovanför en intern belastnings Utjämnings klient konfiguration är:
- `PrivateIpAddressVersion`Anges som "IPv6"
- `-PublicIpAddress`Argumentet har antingen utelämnats eller ersatts av `-PrivateIpAddress` . Observera att den privata adressen måste ligga inom intervallet för under nätets IP-utrymme där den interna belastningsutjämnaren ska distribueras. Om ett statiskt `-PrivateIpAddress` utelämnas väljs nästa kostnads fria IPv6-adress från under nätet där den interna belastningsutjämnaren distribueras.
- Det dubbla stack-undernät där den interna belastningsutjämnaren ska distribueras anges med antingen ett- `-Subnet` eller- `-SubnetId` argument.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 6.9.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="prerequisites"></a>Förutsättningar
Innan du distribuerar ett program med dubbla stackar i Azure måste du konfigurera din prenumeration för den här förhands gransknings funktionen med följande Azure PowerShell:

Registrera på följande sätt:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Det tar upp till 30 minuter för funktions registrering att slutföras. Du kan kontrol lera din registrerings status genom att köra följande Azure PowerShell kommando: kontrol lera registreringen på följande sätt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
När registreringen är klar kör du följande kommando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk med dubbla staplar måste du skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *dsStd_ILB_RG* på platsen *USA, östra* :

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Skapa offentliga IP-adresser för IPv4 och IPv6
För att få åtkomst till dina virtuella datorer från Internet behöver du IPv4-och IPv6 offentliga IP-adresser för de virtuella datorerna. Skapa offentliga IP-adresser med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). I följande exempel skapas IPv4-och IPv6 offentlig IP-adress med namnet *RdpPublicIP_1* och *RdpPublicIP_2* i *dsStd_ILB_RG* resurs gruppen:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Skapa det virtuella nätverket och under nätet

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) med dubbla stackar med en under näts konfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel skapas ett virtuellt nätverk med namnet *dsVnet* med *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Skapa en Standard Load Balancer

I det här avsnittet konfigurerar du IP-adresser för dubbel klient del (IPv4 och IPv6) och backend-adresspoolen för belastningsutjämnaren och sedan skapar du en Standard Load Balancer.

### <a name="create-front-end-ip"></a>Skapa klientdels-IP

Skapa en IP-adress på klientdelen med hjälp av [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). I följande exempel skapas IP-konfigurationer för IPv4-och IPv6-frontend med namnet *dsLbFrontEnd_v4* och *dsLbFrontEnd_v6*:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Konfigurera en serverdelsadresspool

Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). De virtuella datorerna ansluter till den här serverdelspoolen i de återstående stegen. I följande exempel skapas backend-adresspooler med namnet *dsLbBackEndPool_v4* och *dsLbBackEndPool_v6* för att inkludera virtuella datorer med både IPv4-och IPv6 NIC-konfigurationer:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Om du vill se till att endast friska virtuella datorer tar emot trafik kan du välja att definiera en hälso avsökning. Den grundläggande belastningsutjämnaren använder en IPv4-avsökning för att utvärdera hälso tillståndet för både IPv4-och IPv6-slutpunkter på de virtuella datorerna. Standard Load Balancer har stöd för explicita IPv6-hälsoavsökare.

Skapa en lastbalanseringsregel med hjälp av [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). I följande exempel skapas belastnings Utjämnings regler som heter *dsLBrule_v4* och *dsLBrule_v6* och balanserar trafik på *TCP* -port *80* till IP-konfigurationer för IPv4 och IPv6-klient:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa en Standard Load Balancer med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). I följande exempel skapas en offentlig Standard Load Balancer med namnet *myInternalLoadBalancer* med hjälp av IP-konfigurationer för IPv4-och IPv6-klient, backend-pooler och regler för belastnings utjämning som du skapade i föregående steg:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar vissa virtuella datorer och kan testa din saldo måste du skapa stöd för nätverks resurser – tillgänglighets uppsättning, nätverks säkerhets grupp och virtuella nätverkskort. 

### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Om du vill förbättra programmets höga tillgänglighet placerar du dina virtuella datorer i en tillgänglighets uppsättning.

Skapa en tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). I följande exempel skapas en tillgänglighets uppsättning med namnet *dsAVset*:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp för de regler som ska styra inkommande och utgående kommunikation i ditt VNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 3389

Skapa en regel för nätverkssäkerhetsgruppen som tillåter RDP-anslutningar via port 3389 med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 80

Skapa en regel för nätverks säkerhets grupp för att tillåta Internet anslutningar via port 80 med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Skapa nätverkskort

Skapa virtuella nätverkskort med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). I följande exempel skapas två virtuella nätverkskort både med IPv4-och IPv6-konfigurationer. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.)

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Nu kan du skapa de virtuella datorerna med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa ett virtuellt IPv6-nätverk med dubbla stackar i Azure Portal
Du kan visa det virtuella IPv6-nätverket med dubbla stackar i Azure Portal på följande sätt:
1. Skriv *dsVnet* i portalens Sök fält.
2. När **dsVnet** visas i Sök resultaten väljer du det. Då startas **översikts** sidan för det virtuella nätverket med dubbla stackar med namnet *dsVnet*. Det virtuella nätverket med dubbla stackar visar de två nätverkskorten med både IPv4-och IPv6-konfigurationer som finns i det dubbla stack-undernätet med namnet *dsSubnet*.

![IPv6 dubbla stack-Virtual Network med interna standard Load Balancer](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> IPv6 för Azure Virtual Network är tillgängligt i Azure Portal i skrivskyddat läge för den här för hands versionen.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en Standard Load Balancer med en IP-konfiguration med dubbel klient del (IPv4 och IPv6). Du skapade också två virtuella datorer som omfattade nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som har lagts till i belastningsutjämnaren för belastningsutjämnaren. Mer information om IPv6-stöd i Azure Virtual Networks finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)