---
title: 'Självstudie: skapa en belastningsutjämnare för flera regioner med hjälp av Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Kom igång med den här självstudien för att distribuera en Azure Load Balancer över flera regioner med Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721352"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Självstudie: skapa en Azure Load Balancer över flera regioner med Azure PowerShell

En belastningsutjämnare för flera regioner säkerställer att en tjänst är tillgänglig globalt över flera Azure-regioner. Om en region Miss lyckas dirigeras trafiken till nästa närmaste felfria regionala belastnings utjämning.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en belastningsutjämnare för flera regioner.
> * Skapa en lastbalanseringsregel.
> * Skapa en backend-pool som innehåller två regionala belastningsutjämnare.
> * Testa belastningsutjämnaren.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- Två **standardiserade** SKU: er i Azure med backend-pooler distribuerade i två olika Azure-regioner.
    - Information om hur du skapar en regional standard belastningsutjämnare och virtuella datorer för backend-pooler finns i [snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Lägg till namnet på belastningsutjämnaren och virtuella datorer i varje region med a **-R1** och **-R2**. 
- Azure PowerShell installerats lokalt eller Azure Cloud Shell.


Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-cross-region-load-balancer"></a>Skapa en belastningsutjämnare för flera regioner


### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Skapa resurser för belastnings utjämning för flera regioner

I det här avsnittet ska du skapa de resurser som behövs för belastningsutjämnaren i flera regioner.

En offentlig IP-adress för standard-SKU: n används för klient delen för belastningsutjämnaren i flera regioner.

* Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa den offentliga IP-adressen.

* Skapa en IP-konfiguration på klient sidan med [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Skapa en lastbalanseringsregel med hjälp av [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Skapa en belastningsutjämnare för flera regioner med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Konfigurera backend-pool

I det här avsnittet ska du lägga till två regionala standard belastningsutjämnare till backend-poolen för belastningsutjämnaren över flera regioner.

> [!IMPORTANT]
> För att slutföra de här stegen ser du till att två regionala belastningsutjämnare med backend-pooler har distribuerats i din prenumeration.  Mer information finns i, **[snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Använd [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) och [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) för att lagra regional belastnings Utjämnings information i variabler.

* Använd [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) för att skapa konfigurationen för Server delens adresspool för belastningsutjämnaren.

* Använd [set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) för att lägga till den regionala belastnings Utjämnings-frontend: en i en backend-pool för flera regioner.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet ska du testa belastningsutjämnaren för flera regioner. Du ansluter till den offentliga IP-adressen i en webbläsare.  Du kommer att stoppa de virtuella datorerna i en av de regionala Server dels poolerna för belastnings utjämning och kontrol lera redundansväxlingen.

1. Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att hämta belastnings utjämningens offentliga IP-adress:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

3. Stoppa de virtuella datorerna i backend-poolen för en av de regionala belastnings utjämningen.

4. Uppdatera webbläsaren och kontrol lera att anslutningen till den andra regionala belastningsutjämnaren är redundansväxling.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen, belastningsutjämnaren och återstående resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* En global IP-adress har skapats.
* En belastningsutjämnare för flera regioner har skapats.
* En regel för belastnings utjämning har skapats.
* Regionala belastningsutjämnare har lagts till i backend-poolen för belastningsutjämnaren i flera regioner.
* Belastnings utjämning har testats.


Gå vidare till nästa artikel om du vill lära dig hur du...
> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer i tillgänglighets zoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)