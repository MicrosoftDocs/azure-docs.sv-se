---
title: Distribuera IPv6-program med dubbla stackar – grundläggande Load Balancer-PowerShell
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar ett program med dubbla stack-program i Azure Virtual Network med Azure PowerShell.
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
ms.openlocfilehash: 8921722fe36b1969c078c271011253d21f9a91c6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221382"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>Distribuera ett IPv6-program med dubbla stackar med Basic Load Balancer-PowerShell

Den här artikeln visar hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) med Basic Load Balancer att använda Azure PowerShell som innehåller ett virtuellt nätverk och undernät med dubbla stackar, en grundläggande Load Balancer med dubbla (IPv4 + IPv6) frontend-konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, nätverks säkerhets grupp och offentliga IP-adresser.

Om du vill distribuera ett program med dubbla stackar (IPV4 + IPv6) med hjälp av Standard Load Balancer, se [distribuera ett IPv6-program med dubbla stackar med standard Load Balancer använda Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 6.9.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk med dubbla staplar måste du skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myRGDualStack* på platsen *USA, östra* :

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Skapa offentliga IP-adresser för IPv4 och IPv6
För att få åtkomst till dina virtuella datorer från Internet behöver du IPv4-och IPv6 offentliga IP-adresser för belastningsutjämnaren. Skapa offentliga IP-adresser med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). I följande exempel skapas IPv4-och IPv6 offentlig IP-adress med namnet *dsPublicIP_v4* och *dsPublicIP_v6* i resurs gruppen *dsRG1* :

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
För att få åtkomst till dina virtuella datorer med en RDP-anslutning skapar du en offentlig IPV4-IP-adress för de virtuella datorerna med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet konfigurerar du IP-adresser för dubbel klient del (IPv4 och IPv6) och backend-adresspoolen för belastningsutjämnaren och sedan skapar du en grundläggande Load Balancer.

### <a name="create-front-end-ip"></a>Skapa klientdels-IP

Skapa en IP-adress på klientdelen med hjälp av [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). I följande exempel skapas IP-konfigurationer för IPv4-och IPv6-frontend med namnet *dsLbFrontEnd_v4* och *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurera en serverdelsadresspool

Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). De virtuella datorerna ansluter till den här serverdelspoolen i de återstående stegen. I följande exempel skapas backend-adresspooler med namnet *dsLbBackEndPool_v4* och *dsLbBackEndPool_v6* för att inkludera virtuella datorer med både IPv4-och IPv6 NIC-konfigurationer:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Använd [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) för att skapa en hälso avsökning för att övervaka hälso tillståndet för de virtuella datorerna.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Om du vill se till att endast friska virtuella datorer tar emot trafik kan du välja att definiera en hälso avsökning. Den grundläggande belastningsutjämnaren använder en IPv4-avsökning för att utvärdera hälso tillståndet för både IPv4-och IPv6-slutpunkter på de virtuella datorerna. Standard Load Balancer har stöd för explicita IPv6-hälsoavsökare.

Skapa en lastbalanseringsregel med hjälp av [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). I följande exempel skapas belastnings Utjämnings regler som heter *dsLBrule_v4* och *dsLBrule_v6* och balanserar trafik på *TCP* -port *80* till IP-konfigurationer för IPv4 och IPv6-klient:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa Basic-lastbalanseraren med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). I följande exempel skapas en offentlig grundläggande Load Balancer med namnet *myLoadBalancer* med hjälp av IP-konfigurationer för IPv4-och IPv6-frontend, Server dels pooler och regler för belastnings utjämning som du skapade i föregående steg:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar vissa virtuella datorer och kan testa din saldo måste du skapa stöd för nätverks resurser – tillgänglighets uppsättning, nätverks säkerhets grupp, virtuellt nätverk och virtuella nätverkskort. 
### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp för de regler som ska styra inkommande och utgående kommunikation i ditt VNET.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 3389

Skapa en regel för nätverkssäkerhetsgruppen som tillåter RDP-anslutningar via port 3389 med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Skapa nätverkskort

Skapa virtuella nätverkskort med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). I följande exempel skapas två virtuella nätverkskort både med IPv4-och IPv6-konfigurationer. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.)

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Nu kan du skapa de virtuella datorerna med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Fastställa IP-adresser för IPv4-och IPv6-slutpunkter
Hämta alla nätverks gränssnitts objekt i resurs gruppen för att sammanfatta de IP-adresser som används i den här distributionen med `get-AzNetworkInterface` . Hämta också Load Balancerens frontend-adresser för IPv4-och IPv6-slutpunkterna med `get-AzpublicIpAddress` .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Följande bild visar ett exempel på utdata som visar de privata IPv4-och IPv6-adresserna för de två virtuella datorerna och IP-adresserna för klient delens IPv4-och IPv6-adresser för Load Balancer.

![IP-Sammanfattning av program distribution med dubbla stackar (IPv4/IPv6) i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa ett virtuellt IPv6-nätverk med dubbla stackar i Azure Portal
Du kan visa det virtuella IPv6-nätverket med dubbla stackar i Azure Portal på följande sätt:
1. Skriv *dsVnet* i portalens Sök fält.
2. När **myVirtualNetwork** visas i sökresultatet väljer du det. Då startas **översikts** sidan för det virtuella nätverket med dubbla stackar med namnet *dsVnet*. Det virtuella nätverket med dubbla stackar visar de två nätverkskorten med både IPv4-och IPv6-konfigurationer som finns i det dubbla stack-undernätet med namnet *dsSubnet*.

  ![IPv6-virtuellt nätverk med dubbla stackar i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en grundläggande Load Balancer med en IP-konfiguration med dubbel klient del (IPv4 och IPv6). Du skapade också två virtuella datorer som omfattade nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som har lagts till i belastningsutjämnaren för belastningsutjämnaren. Mer information om IPv6-stöd i Azure Virtual Networks finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)