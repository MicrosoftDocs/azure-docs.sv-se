---
title: Belastnings utjämning på flera IP-konfigurationer – Azure CLI
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig mer om belastnings utjämning för primära och sekundära IP-konfigurationer med Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a2916f28be0b45eec6e9c1a85c0b8db3fb611381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417881"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Belastnings utjämning på flera IP-konfigurationer med PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

Den här artikeln beskriver hur du använder Azure Load Balancer med flera IP-adresser i ett sekundärt nätverks gränssnitt (NIC). I det här scenariot har vi två virtuella datorer som kör Windows, var och en med ett primärt och ett sekundärt nätverkskort. Vart och ett av de sekundära nätverkskorten har två IP-konfigurationer. Varje virtuell dator är värd för både websites contoso.com och fabrikam.com. Varje webbplats är kopplad till en av IP-konfigurationerna på det sekundära NÄTVERKSKORTet. Vi använder Azure Load Balancer för att exponera två IP-adresser för klient delen, en för varje webbplats, för att distribuera trafik till respektive IP-konfiguration för webbplatsen. I det här scenariot används samma port nummer både i båda frontend-klienterna och i båda IP-adresserna för backend-poolen.

![Bild av LB-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för belastnings utjämning på flera IP-konfigurationer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följ stegen nedan för att uppnå det scenario som beskrivs i den här artikeln:

1. Installera Azure PowerShell. Se [Installera och konfigurera Azure PowerShell](/powershell/azure/) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.
2. Skapa en resurs grupp med följande inställningar:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Mer information finns i steg 2 i [skapa en resurs grupp](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Skapa en tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) som innehåller dina virtuella datorer. I det här scenariot använder du följande kommando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Följ anvisningarna steg 3 till 5 i [skapa en artikel i Windows VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) för att förbereda skapandet av en virtuell dator med ett enda nätverkskort. Kör steg 6,1 och Använd följande i stället för steg 6,2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Slutför sedan [skapa en Windows VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) -steg 6,3 till 6,8.

5. Lägg till en andra IP-konfiguration till var och en av de virtuella datorerna. Följ anvisningarna i artikeln [tilldela flera IP-adresser till virtuella datorer](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) . Använd följande konfigurations inställningar:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Du behöver inte associera sekundära IP-konfigurationer med offentliga IP-adresser för den här självstudien. Redigera kommandot för att ta bort den offentliga IP-associerings delen.

6. Slutför steg 4 till och med 6 i den här artikeln igen för VM2. Se till att ersätta det virtuella dator namnet till VM2 när du gör detta. Observera att du inte behöver skapa ett virtuellt nätverk för den andra virtuella datorn. Du kanske inte kan skapa ett nytt undernät baserat på ditt användnings fall.

7. Skapa två offentliga IP-adresser och lagra dem i lämpliga variabler som de visas:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Skapa två IP-konfigurationer för klient delen:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Skapa dina backend-adresspooler, en avsökning och dina belastnings Utjämnings regler:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. När du har skapat dessa resurser skapar du belastningsutjämnaren:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Lägg till den andra backend-adresspoolen och klient delens IP-konfiguration i den nyligen skapade belastningsutjämnaren:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Kommandona nedan hämtar nätverkskorten och lägger sedan till båda IP-konfigurationerna för varje sekundärt nätverkskort till backend-adresspoolen för belastningsutjämnaren:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Slutligen måste du konfigurera DNS-resursposter så att de pekar på Load Balancerens respektive frontend-IP-adress. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med Load Balancer finns i [använda Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du kombinerar tjänster för belastnings utjämning i Azure med [hjälp av belastnings Utjämnings tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [Azure Monitor loggar för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).