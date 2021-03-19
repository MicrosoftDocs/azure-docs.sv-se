---
title: 'Självstudie: skapa en skalnings uppsättning för virtuella Windows-datorer'
description: Lär dig hur du använder Azure PowerShell för att skapa och distribuera ett program med hög tillgänglighet på virtuella Windows-datorer med en skalnings uppsättning för virtuella datorer
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 11/30/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b3853ddc71d1a9be26b2492764a9b341446e0eeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078749"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Självstudiekurs: Skapa en VM-skalningsuppsättning och distribuera en app med hög tillgänglighet i Windows med Azure PowerShell
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt. Du kan även definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I självstudien distribuerar du en VM-skalningsuppsättning i Azure och lär dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en lastbalanserare för din skalningsuppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="scale-set-overview"></a>Översikt över skalningsuppsättning
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Virtuella datorer i en skalningsuppsättning är distribuerade över logiska fel- och uppdateringsdomäner i en eller flera *placeringsgrupper*. Placeringsgrupper är grupper med virtuella datorer som har en liknande konfiguration, vilket liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skalningsuppsättning. Du kan definiera regler för automatisk skalning om du vill styra hur och när virtuella datorer läggs till eller tas bort från skalningsuppsättningen. De här reglerna kan utlösas baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

Skalningsuppsättningar har stöd för upp till 1 000 virtuella datorer när du använder en avbildning i Azure-plattformen. För arbetsbelastningar med större installations- eller VM-anpassningskrav kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md). Du kan skapa upp till 600 virtuella datorer i en skalnings uppsättning när du använder en anpassad avbildning.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som använder *Windows Server 2016 Datacenter*-plattformsavbildningen. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och lastbalanserare skapas automatiskt. När du uppmanas kan du ange dina egna administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättningen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="deploy-sample-application"></a>Distribuera exempelprogram
Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. Det anpassade skripttillägget i Azure används för att hämta och köra ett skript som installerar IIS på de virtuella datorinstanserna. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Mer information finns i [översikten över tillägget för anpassat skript](../extensions/custom-script-windows.md).

Använd det anpassade skripttillägget för att installera en grundläggande IIS-webbserver. Tillämpa tillägget för anpassat skript som installerar IIS på följande sätt:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Tillåta trafik till program

Om du vill tillåta åtkomst till den grundläggande webbappen måste du skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) och [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Mer information finns i [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill testa din skalningsuppsättning, hämtar du den offentliga IP-adressen för din lastbalanserare med [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). I följande exempel visas IP-adressen för *myPublicIP* som skapas som en del av skalningsuppsättningen:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

Ange den offentliga IP-adressen i en webbläsare. Webbappen visas med värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till:

![Köra IIS-webbplats](./media/tutorial-create-vmss/running-iis-site.png)

Om du vill se när skalningsuppsättningen används kan du framtvinga en uppdatering av webbläsaren. Då visas hur lastbalanseraren distribuerar trafik över alla virtuella datorer som kör programmet.


## <a name="management-tasks"></a>Hanteringsuppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skalningsuppsättningen. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Med Azure PowerShell kan du snabbt utföra dessa uppgifter. Här följer några vanliga uppgifter.

### <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista med virtuella datorinstanser i en skalningsuppsättning, använder du [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) på följande sätt:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

Följande exempelutdata visar två virtuella datorinstanser i skalningsuppsättningen:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Om du vill visa mer information om en specifik VM-instans, lägger du till parametern `-InstanceId` till [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Följande exempel visar information om den virtuella datorinstansen *1*:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Öka eller minska VM-instanser
Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [Get-AzVmss](/powershell/module/az.compute/get-azvmss) och frågar efter *sku.capacity*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [Update-AzVmss](/powershell/module/az.compute/update-azvmss). I följande exempel anges antalet virtuella datorer i din skalningsuppsättning till *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Det tar några minuter att uppdatera det angivna antalet instanser i skalningsuppsättningen.


### <a name="configure-autoscale-rules"></a>Konfigurera regler för automatisk skalning
Du kan definiera regler för automatisk skalning i stället för att skala antalet instanser manuellt i skalningsuppsättningen. De här reglerna övervakar instanserna i skalningsuppsättningen och svarar därefter baserat på de mått och tröskelvärden som du definierar. I följande exempel skalas antalet instanser ut med en när den genomsnittliga CPU-belastningen är större än 60 % under en 5-minutersperiod. Om den genomsnittliga CPU-belastningen sedan sjunker under 30 % under en 5-minutersperiod, skalas instanserna in med en instans:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Mer information om användningen av autoskalning finns i [Metodtips för autoskalning](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du en VM-skalningsuppsättning. Du har lärt dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att definiera en IIS-webbplats för att skala
> * Skapa en lastbalanserare för din skalningsuppsättning
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning

Gå vidare till nästa självstudie för att lära dig mer om belastningsutjämning för virtuella datorer.

> [!div class="nextstepaction"]
> [Balansera belastningen mellan virtuella datorer](tutorial-load-balancer.md)
