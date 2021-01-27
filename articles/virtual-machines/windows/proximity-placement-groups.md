---
title: 'PowerShell: Använd närhets placerings grupper'
description: Lär dig mer om att skapa och använda närhets placerings grupper med hjälp av Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4de71be8c88264d2cfb513a7f0214515058b5185
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878332"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuera virtuella datorer till närhets placerings grupper med hjälp av PowerShell


För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](../co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


## <a name="create-a-proximity-placement-group"></a>Skapa en närhetsplaceringsgrupp
Skapa en närhets placerings grupp med cmdleten [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) . 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Lista närhets placerings grupper

Du kan visa alla närhets placerings grupper med hjälp av cmdleten [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i närhets placerings gruppen med hjälp av `-ProximityPlacementGroup $ppg.Id` för att referera till närhets grupp-ID: t när du använder [New-AzVM](/powershell/module/az.compute/new-azvm) för att skapa den virtuella datorn.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Du kan se den virtuella datorn i placerings gruppen med hjälp av [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Flytta en befintlig virtuell dator till en närhets placerings grupp

Du kan också lägga till en befintlig virtuell dator till en närhets placerings grupp. Du måste först stop\deallocate den virtuella datorn och sedan uppdatera den virtuella datorn och starta om.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Flytta en befintlig virtuell dator från en närhets placerings grupp

Om du vill ta bort en virtuell dator från en närhets placerings grupp måste du först stop\deallocate den virtuella datorn och sedan uppdatera den virtuella datorn och starta om.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighets uppsättning i närheten av placerings gruppen. Använd samma `-ProximityPlacementGroup` parameter med cmdleten [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) för att skapa en tillgänglighets uppsättning och alla de virtuella datorer som skapats i tillgänglighets uppsättningen skapas också i samma närhets placerings grupp.

Om du vill lägga till eller ta bort en befintlig tillgänglighets uppsättning till en närhets placerings grupp måste du först stoppa alla virtuella datorer i tillgänglighets uppsättningen. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Flytta en befintlig tillgänglighets uppsättning till en närhets placerings grupp

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Flytta en befintlig tillgänglighets uppsättning från en närhets placerings grupp

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalnings uppsättning i närheten av placerings gruppen. Använd samma `-ProximityPlacementGroup` parameter med [New-AzVmss](/powershell/module/az.compute/new-azvmss) för att skapa en skalnings uppsättning och alla instanser kommer att skapas i samma närhets placerings grupp.


Om du vill lägga till eller ta bort en befintlig skalnings uppsättning till en närhets placerings grupp måste du först stoppa skalnings uppsättningen. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Flytta en befintlig skalnings uppsättning till en närhets placerings grupp

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Flytta en befintlig skalnings uppsättning av en närhets placerings grupp

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure CLI](../linux/proximity-placement-groups.md) för att skapa närhets grupper.