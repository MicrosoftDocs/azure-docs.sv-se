---
title: Skapa en virtuell Windows-dator från en specialiserad virtuell hård disk i Azure
description: Skapa en ny virtuell Windows-dator genom att koppla en specialiserad hanterad disk som OS-disk med hjälp av distributions modellen för Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 642a28d5eea6a89de31247ed715c0b352b9ff7d1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552226"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Skapa en virtuell Windows-dator från en specialiserad disk med PowerShell

Skapa en ny virtuell dator genom att koppla en specialiserad hanterad disk som operativ system disk. En specialiserad disk är en kopia av en virtuell hård disk (VHD) från en befintlig virtuell dator som innehåller användar konton, program och andra tillstånds data från den ursprungliga virtuella datorn. 

I det syftet har du flera alternativ:
* [Använd en befintlig hanterad disk](#option-1-use-an-existing-disk). Det här alternativet är användbart om du har en virtuell dator som inte fungerar som den ska. Du kan ta bort den virtuella datorn och sedan återanvända den hanterade disken för att skapa en ny virtuell dator. 
* [Ladda upp en virtuell hårddisk](#option-2-upload-a-specialized-vhd) 
* [Kopiera en befintlig virtuell Azure-dator med hjälp av ögonblicks bilder](#option-3-copy-an-existing-azure-vm)

Du kan också använda Azure Portal för att [skapa en ny virtuell dator från en specialiserad virtuell hård disk](create-vm-specialized-portal.md).

Den här artikeln visar hur du använder hanterade diskar. Om du har en äldre distribution som kräver att ett lagrings konto används, se [skapa en virtuell dator från en specialiserad virtuell hård disk i ett lagrings konto](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized).

> [!IMPORTANT]
> 
> När du använder en specialiserad disk för att skapa en ny virtuell dator behåller den nya virtuella datorn dator namnet för den ursprungliga virtuella datorn. Annan datorspecifik information (t. ex. CMID) hålls även och, i vissa fall kan denna Duplicerad information orsaka problem. När du kopierar en virtuell dator bör du vara medveten om vilken typ av datorspecifik information som dina program förlitar sig på.  
> Använd därför inte en specialiserad disk om du vill skapa flera virtuella datorer. I stället [skapar du en avbildning](capture-image-resource.md) och [använder avbildningen för att skapa flera virtuella datorer](create-vm-generalized-managed.md)i stället för större distributioner.

Vi rekommenderar att du begränsar antalet samtidiga distributioner till 20 virtuella datorer från en enda virtuell hård disk eller en ögonblicks bild. 

## <a name="option-1-use-an-existing-disk"></a>Alternativ 1: Använd en befintlig disk

Om du har en virtuell dator som du har tagit bort och du vill återanvända OS-disken för att skapa en ny virtuell dator, använder du [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Nu kan du ansluta den här disken som OS-disk till en [ny virtuell dator](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Alternativ 2: Ladda upp en specialiserad virtuell hård disk

Du kan ladda upp den virtuella hård disken från en specialiserad virtuell dator som skapats med ett lokalt virtualiseringslösningar, till exempel Hyper-V eller en virtuell dator som exporter ATS från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Använd den virtuella hård disken som-är för att skapa en ny virtuell dator. 
  
  * [Förbered en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md). Generalisera **inte** den virtuella datorn med hjälp av Sysprep.
  * Ta bort eventuella verktyg och agenter för gästautentisering som är installerade på den virtuella datorn (till exempel VMware-verktyg).
  * Se till att den virtuella datorn är konfigurerad för att hämta IP-adressen och DNS-inställningarna från DHCP. Detta säkerställer att servern får en IP-adress i det virtuella nätverket när den startas. 


### <a name="upload-the-vhd"></a>Ladda upp den virtuella hård disken

Nu kan du ladda upp en virtuell hård disk direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hård disk till Azure med Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Alternativ 3: kopiera en befintlig virtuell Azure-dator

Du kan skapa en kopia av en virtuell dator som använder hanterade diskar genom att ta en ögonblicks bild av den virtuella datorn och sedan använda ögonblicks bilden för att skapa en ny hanterad disk och en ny virtuell dator.

Om du vill kopiera en befintlig virtuell dator till en annan region kanske du vill använda AzCopy för att [skapa en kopia av en disk i en annan region](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicks bild av OS-disken

Du kan ta en ögonblicks bild av en hel virtuell dator (inklusive alla diskar) eller bara en enskild disk. Följande steg visar hur du tar en ögonblicks bild av bara OS-disken för den virtuella datorn med cmdleten [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

Ange först några parametrar. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Hämta det virtuella datorobjektet.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Hämta OS-diskens namn.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Skapa ögonblicks bilds konfigurationen. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Ta ögonblicks bilden.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Om du vill använda den här ögonblicks bilden för att skapa en virtuell dator som måste vara hög, lägger du till parametern `-AccountType Premium_LRS` i New-AzSnapshotConfig kommandot. Den här parametern skapar ögonblicks bilden så att den lagras som en Premium-hanterad disk. Premium Managed Disks är dyrare än standard, så se till att du behöver Premium innan du använder den här parametern.

### <a name="create-a-new-disk-from-the-snapshot"></a>Skapa en ny disk från ögonblicks bilden

Skapa en hanterad disk från ögonblicks bilden med hjälp av [New-AzDisk](/powershell/module/az.compute/new-azdisk). I det här exemplet används *myOSDisk* som disk namn.

Skapa en ny resurs grupp för den nya virtuella datorn.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ange OS-diskens namn. 

```powershell
$osDiskName = 'myOsDisk'
```

Skapa den hanterade disken.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Skapa den nya virtuella datorn 

Skapa nätverk och andra virtuella dator resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-virtual-network"></a>Skapa undernät och virtuellt nätverk

Skapa det [virtuella nätverket](../../virtual-network/virtual-networks-overview.md) och under nätet för den virtuella datorn.

1. Skapa under nätet. I det här exemplet skapas ett undernät med namnet *mitt undernät*, i resurs gruppens *myDestinationResourceGroup*, och anger under nätets adressprefix till *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Skapa det virtuella nätverket. I det här exemplet anges det virtuella nätverks namnet till *myVnetName*, platsen för *västra USA* och adressprefixet för det virtuella nätverket till *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverks säkerhets gruppen och en RDP-regel
Om du vill kunna logga in på den virtuella datorn med Remote Desktop Protocol (RDP) måste du ha en säkerhets regel som tillåter RDP-åtkomst på port 3389. I vårt exempel skapades den virtuella hård disken för den nya virtuella datorn från en befintlig specialiserad virtuell dator, så du kan använda ett konto som fanns på den virtuella käll datorn för RDP.

I det här exemplet anges namnet på nätverks säkerhets gruppen (NSG) till *myNsg* och RDP-regelns namn till *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slut punkter och NSG-regler finns i [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
Om du vill aktivera kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/public-ip-addresses.md) och ett nätverks gränssnitt.

1. Skapa den offentliga IP-adressen. I det här exemplet anges namnet på den offentliga IP-adressen till *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Skapa NÄTVERKSKORTet. I det här exemplet är NÄTVERKSKORTets namn inställt på *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Ange namn och storlek för den virtuella datorn

I det här exemplet anges namnet på den virtuella datorn till *myVM* och storleken på den virtuella datorn *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till NÄTVERKSKORTet
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Lägg till OS-disken 

Lägg till OS-disken i konfigurationen genom att använda [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). I det här exemplet anges storleken på disken till *128 GB* och den hanterade disken kopplas som en *Windows* OS-disk.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm) med de konfigurationer som vi nyss skapade.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Om det här kommandot lyckas visas utdata som liknar följande:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifiera att den virtuella datorn har skapats
Du bör se den nyligen skapade virtuella datorn antingen i [Azure Portal](https://portal.azure.com) under **Bläddra** i  >  **virtuella datorer** eller med hjälp av följande PowerShell-kommandon.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på den nya virtuella datorn. Mer information finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](connect-logon.md).