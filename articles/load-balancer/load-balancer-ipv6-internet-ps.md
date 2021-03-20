---
title: Skapa en belastningsutjämnare mot Internet med IPv6-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Lär dig hur du skapar en belastningsutjämnare mot Internet med IPv6 med hjälp av PowerShell för Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dubbel stack, offentlig IP, inbyggd IPv6, mobil, IoT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 43203a756bcb42c7d00de9c11e9223f1d8b9e2a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001595"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Kom igång med att skapa en belastningsutjämnare mot Internet med IPv6 med hjälp av PowerShell för Resource Manager

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)

>[!NOTE] 
>I den här artikeln beskrivs en inledande IPv6-funktion för att tillåta grundläggande belastningsutjämnare att tillhandahålla både IPv4-och IPv6-anslutning. En omfattande IPv6-anslutning är nu tillgänglig med [IPv6 för Azure-virtuella nätverk](../virtual-network/ipv6-overview.md) som integrerar IPv6-anslutningar med dina virtuella nätverk och innehåller viktiga funktioner som IPv6-regler för nätverks säkerhets grupper, IPv6-användardefinierad routning, IPv6 Basic och standard belastnings utjämning med mera.  IPv6 för Azure virtuella nätverk är den rekommenderade standarden för IPv6-program i Azure. Se [IPv6 för Azure VNet PowerShell-distribution](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Lastbalanseraren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en lastbalanseringsuppsättning. Azure Load Balancer kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Exempel på distributions scenario

Följande diagram illustrerar belastnings Utjämnings lösningen som distribueras i den här artikeln.

![Lastbalanseringsscenario](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

I det här scenariot kommer du att skapa följande Azure-resurser:

* en Internetbaserad Load Balancer med en IPv4-och en offentlig IP-adress för IPv6
* två belastnings Utjämnings regler för att mappa offentliga VIP-adresser till privata slut punkter
* en tillgänglighets uppsättning som innehåller de två virtuella datorerna
* två virtuella datorer (VM)
* ett virtuellt nätverks gränssnitt för varje virtuell dator med både IPv4-och IPv6-adresser tilldelade

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Distribuera lösningen med hjälp av Azure PowerShell

Följande steg visar hur du skapar en belastningsutjämnare mot Internet med hjälp av Azure Resource Manager med PowerShell. Med Azure Resource Manager skapas och konfigureras varje resurs individuellt för att skapa en resurs.

Om du vill distribuera en belastningsutjämnare skapar och konfigurerar du följande objekt:

* IP-konfiguration för klient del – innehåller offentliga IP-adresser för inkommande nätverks trafik.
* Backend-adresspool – innehåller nätverks gränssnitt (NIC) för de virtuella datorerna för att ta emot nätverks trafik från belastningsutjämnaren.
* Lastbalanseringsregler – innehåller regler för mappning av en offentlig port på lastbalanseraren till en port i backend-adresspoolen.
* NAT-regler för inkommande trafik – innehåller regler för mappning av en offentlig port i lastbalanseraren till en port för en specifik virtuell dator i backend-adresspoolen.
* Avsökningar – innehåller hälsoavsökningar som används för att kontrollera tillgängligheten av instanser av virtuella datorer i backend-adresspoolen.

Mer information finns i [Azure Load Balancer-komponenter](./components.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurera PowerShell för användning med Resource Manager

Kontrol lera att du har den senaste produktions versionen av Azure Resource Manager-modulen för PowerShell.

1. Logga in på Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Ange dina autentiseringsuppgifter när du uppmanas att göra det.

2. Kontrollera kontots prenumerationer

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Välj vilka av dina Azure-prenumerationer som du vill använda.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Skapa en resurs grupp (hoppa över det här steget om du använder en befintlig resurs grupp)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en offentlig IP-adress för IP-adresspoolen på klientsidan

1. Skapa ett virtuellt nätverk med ett undernät.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Skapa resurser för den offentliga IP-adressen (PIP) för klient delens IP-adresspool. Se till att ändra värdet för `-DomainNameLabel` innan du kör följande kommandon. Värdet måste vara unikt inom Azure-regionen.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Belastningsutjämnaren använder domän etiketten för den offentliga IP-adressen som prefix för dess fullständiga domän namn. I det här exemplet är FQDN-namnen *lbnrpipv4.westus.cloudapp.Azure.com* och *lbnrpipv6.westus.cloudapp.Azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Skapa en Front-End IP-konfigurationer och en Back-End adresspool

1. Skapa klient dels adress konfiguration som använder de offentliga IP-adresser som du har skapat.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Skapa backend-adresspooler.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Skapa LB-regler, NAT-regler, en avsökning och en belastningsutjämnare

I det här exemplet skapas följande objekt:

* en NAT-regel som översätter all inkommande trafik på port 443 till port 4443
* En lastbalanseringsregel som balanserar all inkommande trafik på port 80 till port 80 för adresserna i backend-poolen.
* en belastnings Utjämnings regel som tillåter RDP-anslutning till de virtuella datorerna på port 3389.
* en avsöknings regel för att kontrol lera hälso statusen på en sida med namnet *HealthProbe. aspx* eller en tjänst på Port 8080
* en belastningsutjämnare som använder alla dessa objekt

1. Skapa NAT-reglerna.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Skapa en hälsoavsökning. Du kan konfigurera en avsökning på två sätt:

    HTTP-avsökning

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    eller TCP-avsökning

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    I det här exemplet ska vi använda TCP-avsökningar.

3. Skapa en lastbalanseringsregel.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Skapa belastningsutjämnaren med hjälp av de tidigare skapade objekten.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Skapa nätverkskort för backend-VM: ar

1. Hämta Virtual Network-och Virtual Network-undernätet där nätverkskorten måste skapas.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Skapa IP-konfigurationer och nätverkskort för de virtuella datorerna.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Skapa virtuella datorer och tilldela de nyligen skapade nätverkskorten

Mer information om hur du skapar en virtuell dator finns i [skapa och förkonfigurera en virtuell Windows-dator med Resource Manager och Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Skapa en tillgänglighets uppsättning och ett lagrings konto

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Skapa varje virtuell dator och tilldela de tidigare skapade nätverkskorten

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


