---
title: Ändra storlek på en virtuell Windows-dator i Azure
description: Ändra den virtuella dator storleken som används för en virtuell Azure-dator.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: f7f41c0fb2bf949dd47491761fde8025a2d35155
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560658"
---
# <a name="resize-a-windows-vm"></a>Ändra storlek på en virtuell Windows-dator

Den här artikeln visar hur du flyttar en virtuell dator till en annan [VM-storlek](../sizes.md).

När du har skapat en virtuell dator (VM) kan du skala upp eller ned den virtuella datorn genom att ändra storleken på den virtuella datorn. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig i det maskin varu kluster som för närvarande är värd för den virtuella datorn.

Om din virtuella dator använder Premium Storage, se till att du väljer en **s** -version av storleken för att få Premium Storage support. Välj till exempel Standard_E4 **s** _v3 i stället för Standard_E4_v3.

## <a name="use-the-portal"></a>Använda portalen

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Öppna sidan för den virtuella datorn.
1. I den vänstra menyn väljer du **storlek**.
1. Välj en ny storlek i listan över tillgängliga storlekar och välj sedan **ändra storlek**.


Om den virtuella datorn körs för tillfället kommer den att startas om om du ändrar storlek på den. Att stoppa den virtuella datorn kan visa fler storlekar.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Använd PowerShell för att ändra storlek på en virtuell dator som inte finns i en tillgänglighets uppsättning

Ange några variabler. Ersätt värdena med din egen information.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de VM-storlekar som är tillgängliga på det maskin varu kluster där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om den storlek som du vill ha visas i listan kör du följande kommandon för att ändra storlek på den virtuella datorn. Om önskad storlek inte visas i listan går du vidare till steg 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Om den storlek som du vill ha inte visas i listan kör du följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och starta om den virtuella datorn. Ersätt **\<newVMsize>** med den storlek som du vill använda.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Om du avallokerar VM frigör det dynamiska IP-adresser som tilldelats den virtuella datorn. Operativ system-och data diskar påverkas inte. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Använd PowerShell för att ändra storlek på en virtuell dator i en tillgänglighets uppsättning

Om den nya storleken för en virtuell dator i en tillgänglighets uppsättning inte är tillgänglig på det maskin varu kluster som är värd för den virtuella datorn, måste alla virtuella datorer i tillgänglighets uppsättningen frigöras för att ändra storlek på den virtuella datorn. Du kan också behöva uppdatera storleken på andra virtuella datorer i tillgänglighets uppsättningen när storleken på en virtuell dator har ändrats. Utför följande steg för att ändra storlek på en virtuell dator i en tillgänglighets uppsättning.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Lista de VM-storlekar som är tillgängliga på det maskin varu kluster där den virtuella datorn finns. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Om önskad storlek visas kör du följande kommandon för att ändra storlek på den virtuella datorn. Om den inte visas går du till nästa avsnitt.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Om den storlek som du vill ha inte visas i listan fortsätter du med följande steg för att frigöra alla virtuella datorer i tillgänglighets uppsättningen, ändrar storlek på virtuella datorer och startar om dem.

Stoppa alla virtuella datorer i tillgänglighets uppsättningen.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Ändra storlek och starta om de virtuella datorerna i tillgänglighets uppsättningen.
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>Nästa steg

Om du vill ha ytterligare skalbarhet kan du köra flera virtuella dator instanser och skala ut. Mer information finns i skala [Windows-datorer automatiskt i en skalnings uppsättning för virtuella datorer](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).