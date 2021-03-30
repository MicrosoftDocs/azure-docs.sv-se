---
title: Självstudie – Skapa och Använd diskar för skalnings uppsättningar med Azure PowerShell
description: Läs hur du använder Azure PowerShell för att skapa och använda hanterade diskar med VM-skalningsuppsättningar, inklusive hur du lägger till, förbereder, listar och kopplar från diskarna.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9e995e88b80bf14f9c7784f465bcd3d89d0bed65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367974"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Självstudie: skapa och använd diskar med VM-skalningsuppsättningar med Azure PowerShell

VM-skalningsuppsättningar använder diskar för att lagra den virtuella datorinstansens operativsystem, program och data. När du skapar och hanterar en skalningsuppsättning, är det viktigt att välja en diskstorlek och konfiguration som lämpar sig för den förväntade arbetsbelastningen. Den här självstudien beskriver hur du skapar och hanterar virtuella datordiskar. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Anslut och förbered datadiskar

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Azure-standarddiskar
När en skalningsuppsättning skapas eller skalas, ansluts två diskar automatiskt till varje virtuell datorinstans. 

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 2 TB stora och innehåller de virtuella datorernas operativsystem. Operativsystemdisken kallas som standard */dev/sda*. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. Därför bör OS-disken **inte** innehålla program eller data. För program och data använder du datadiskar som beskrivs senare i den här artikeln. 

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorinstansen. Det här är högpresterande diskar och kan användas för åtgärder som till exempel tillfällig databearbetning. Om den virtuella datorinstansen flyttas till en ny värddator tas dock alla data som lagras på den temporära disken bort. Storleken på den temporära disken bestäms av den virtuella datorinstansens storlek. Temporära diskar kallas */dev/sdb* och har monteringspunkten */mnt*.

### <a name="temporary-disk-sizes"></a>Storlekar för temporära diskar
| Typ | Normala storlekar | Maxstorlek för temporär disk (GiB) |
|----|----|----|
| [Generell användning](../virtual-machines/sizes-general.md) | A-, B- och D-serien | 1600 |
| [Beräkningsoptimerad](../virtual-machines/sizes-compute.md) | F-serien | 576 |
| [Minnesoptimerad](../virtual-machines/sizes-memory.md) | D-, E-, G- och M-serien | 6144 |
| [Lagringsoptimerad](../virtual-machines/sizes-storage.md) | L-serien | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-serien | 1440 |
| [Höga prestanda](../virtual-machines/sizes-hpc.md) | A- och H-serien | 2000 |


## <a name="azure-data-disks"></a>Azure-datadiskar
Du kan lägga till ytterligare datadiskar om du behöver installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Varje datadisk har en maxkapacitet på 4 TB. Storleken på den virtuella datorinstansen avgör hur många datadiskar som kan anslutas. Två datadiskar kan kopplas för varje VM vCPU.

### <a name="max-data-disks-per-vm"></a>Maximalt antal datadiskar per VM
| Typ | Normala storlekar | Maximalt antal datadiskar per VM |
|----|----|----|
| [Generell användning](../virtual-machines/sizes-general.md) | A-, B- och D-serien | 64 |
| [Beräkningsoptimerad](../virtual-machines/sizes-compute.md) | F-serien | 64 |
| [Minnesoptimerad](../virtual-machines/sizes-memory.md) | D-, E-, G- och M-serien | 64 |
| [Lagringsoptimerad](../virtual-machines/sizes-storage.md) | L-serien | 64 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-serien | 64 |
| [Höga prestanda](../virtual-machines/sizes-hpc.md) | A- och H-serien | 64 |


## <a name="vm-disk-types"></a>VM-disktyper
Azure tillhandahåller två disktyper.

### <a name="standard-disk"></a>Standarddiskar
Standardlagring stöds av hårddiskar och levererar kostnadseffektiv lagring och prestanda. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

### <a name="premium-disk"></a>Premiumdiskar
Premiumdiskar backas upp av SSD-baserade diskar med hög prestanda och låg latens. De här diskarna rekommenderas för virtuella datorer som kör produktionsarbetsbelastningar. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien GS-serien och FS-serien. När du väljer en diskstorlek, avrundas värdet uppåt till nästa typ. Om diskstorleken till exempel är mindre än 128 GB är disktypen P10. Om disktypen är mellan 129 GB och 512 GB är storleken P20. Över 512 GB är storleken en P30.

### <a name="premium-disk-performance"></a>Premiumdiskprestanda
|Premium Storage-disktyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Diskens storlek (avrundas uppåt) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Högsta IOPS per disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Dataflöde per disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. En Standard_GS5 virtuell dator kan till exempel uppnå maximalt 80 000 IOPS. Mer information om högsta IOPS per virtuell dator finns i [Storlekar för virtuella Windows-datorer](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Skapa och koppla diskar
Du kan skapa och ansluta diskar när du skapar en skalningsuppsättning eller med en befintlig skalningsuppsättning.

Från och med API `2019-07-01` -versionen kan du ange storleken på OS-disken i en skalnings uppsättning för virtuella datorer med egenskapen [StorageProfile. OsDisk. diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) . Efter etableringen kan du behöva expandera eller partitionera om disken för att kunna använda hela utrymmet. Lär dig mer om [att utöka disken här](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Anslut diskarna när skalningsuppsättningen skapas
Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). När du uppmanas, anger du ett användarnamn och lösenord för de virtuella datorinstanserna. För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985.

Två diskar skapas med parametern `-DataDiskSizeGb`. Den första disken är *64* GB stor och den andra disken är *128* GB. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorinstanser.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anslut en disk till en befintlig skalningsuppsättning
Du kan även ansluta diskar till en befintlig skalningsuppsättning. Använd skalningsuppsättningen som skapades i det föregående steget för att lägga till en annan disk med [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk). Följande exempel ansluter en ytterligare *128* GB disk till en befintlig skalningsuppsättning:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Förbered datadiskarna
Diskarna som skapas och ansluts till dina skalningsuppsättningar för virtuella datorinstanser är rådiskar. Innan du kan använda dem med dina data och program, måste de förberedas. För att förbereda diskarna, skapar du en partition, skapar ett filsystem och monterar dem.

Du kan använda det anpassade skripttillägget för Azure för att automatisera processen på flera virtuella datorinstanser i en skalningsuppsättning. Det här tillägget kan köra skript lokalt på varje virtuell datorinstans, till exempel för att förbereda anslutna datadiskar. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/extensions/custom-script-windows.md).


Följande exempel kör ett skript från ett GitHub-exempellager på varje virtuell datorinstans med [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) som förbereder alla anslutna rådatadiskar:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

För att bekräfta att diskarna har förberetts korrekt, RDP:ar du till en av de virtuella datorinstanserna. 

Först hämtar du lastbalanserarobjektet med [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Därefter visar du de inkommande NAT-reglerna med [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). NAT-reglerna listar *FrontendPort* för varje virtuell datorinstans som RDP lyssnar på. Slutligen hämtar du den offentliga IP-adressen för lastbalanseraren med [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Om du vill ansluta till din virtuella dator, anger du din egna offentliga IP-adress och portnummer för den nödvändiga virtuella datorinstansen som den visas i föregående kommandon. När du uppmanas, anger du de autentiseringsuppgifter som användes när du skapade skalningsuppsättningen. Om du använder Azure Cloud Shell, utför du den här åtgärden från en lokal PowerShell-kommandotolk eller klienten för fjärrskrivbord. Följande exempel ansluter till den virtuella datorinstansen *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Öppna en lokal PowerShell-session på den virtuella datorinstansen och titta på de anslutna diskarna med [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

Följande exempelutdata visar att de tre datadiskarna är anslutna till den virtuella datorinstansen.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Granska filsystemen och monteringspunkterna på den virtuella datorinstansen på följande sätt:

```powershell
Get-Partition
```

Följande exempelutdata visar att de tre datadiskarna är tilldelade enhetsbokstäver:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

Diskarna på varje virtuell datorinstans i din skala förbereds automatiskt på samma sätt. Allteftersom din skalningsuppsättning skalar upp, ansluts de nödvändiga datadiskarna till de nya virtuella datorinstanserna. Det anpassade skripttillägget körs också för att automatiskt förbereda diskarna.

Stäng fjärrskrivbordssessionen med den virtuella datorinstansen.


## <a name="list-attached-disks"></a>Lista de anslutna diskarna
Du kan visa information om diskar anslutna till en skalningsuppsättning med [Get-AzVmss](/powershell/module/az.compute/get-azvmss) på följande sätt:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Under egenskapen *VirtualMachineProfile.StorageProfile*, visas listan över *DataDisks*. Information om diskstorleken, lagringsnivå och LUN (Logical Unit Number) visas. Följande exempelutdata visar information om de tre datadiskarna som är kopplade till skalningsuppsättningen:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Koppla från en disk
När du inte längre behöver en angiven disk, kan du koppla från den från skalningsuppsättningen. Disken tas bort från alla virtuella datorinstanser i skalningsuppsättningen. Om du vill koppla bort en disk från en skalningsuppsättning, använder du [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) och anger LUN för disken. LUN visas i utdata från [Get-AzVmss](/powershell/module/az.compute/get-azvmss) i föregående avsnitt. Följande exempel kopplar från LUN *3* från skalningsuppsättningen:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och diskar, tar du bort resursgruppen och alla dess resurser med [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder diskar med skalningsuppsättningar med Azure PowerShell:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Anslut och förbered datadiskar

Gå vidare till nästa självstudie för att läsa hur du använder en anpassad avbildning för din skalningsuppsättning för virtuella datorinstanser.

> [!div class="nextstepaction"]
> [Använd en anpassad avbildning för skalningsuppsättningar för virtuella datorinstanser](tutorial-use-custom-image-powershell.md)