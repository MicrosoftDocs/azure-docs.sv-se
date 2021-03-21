---
title: Expandera OS-enheten för en virtuell Windows-dator i en Azure
description: Expandera storleken på operativ system enheten för en virtuell dator med hjälp av Azure PowerShell i distributions modellen för Resource Manager.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199490"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Utöka operativsystemenheten för en virtuell dator

När du skapar en ny virtuell dator i en resurs grupp genom att distribuera en avbildning från [Azure Marketplace](https://azure.microsoft.com/marketplace/), är standard operativ system enheten ofta 127 GB (vissa avbildningar har mindre disk storlekar för operativ system som standard). Även om det är möjligt att lägga till data diskar i den virtuella datorn (antalet beror på vilken SKU du valde) och vi rekommenderar att du installerar program och processor intensiva arbets belastningar på dessa tillägg diskar, ofta måste kunderna expandera operativ system enheten för att stödja vissa scenarier:

- För att stödja äldre program som installerar komponenter på operativ system enheten.
- För att migrera en fysisk dator eller virtuell dator från en lokal dator till en större operativ system enhet.

> [!IMPORTANT]
> Att ändra storlek på ett operativ system eller en data disk på en virtuell Azure-dator kräver att den virtuella datorn frigörs.
>
> Det finns inte stöd för att krympa en befintlig disk, och det kan eventuellt leda till data förlust.
> 
> När du har expanderat diskarna måste du [expandera volymen i operativ systemet](#expand-the-volume-within-the-os) för att dra nytta av den större disken.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Ändra storlek på en hanterad disk i Azure Portal

1. I [Azure Portal](https://portal.azure.com)går du till den virtuella dator där du vill expandera disken. Välj **stoppa** för att frigöra den virtuella datorn.
2. När den virtuella datorn är stoppad väljer du **diskar** på den vänstra menyn under **Inställningar**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Skärm bild som visar alternativet diskar som marker ATS i avsnittet Inställningar på menyn.":::

 
3. Under **disk namn** väljer du den disk som du vill ändra storlek på.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Skärm bild som visar fönstret disks med ett disk namn valt.":::

4. I den vänstra menyn under **Inställningar** väljer du **storlek + prestanda**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Skärm bild som visar alternativet storlek och prestanda som marker ATS i avsnittet Inställningar på menyn.":::

5. I **storlek + prestanda** väljer du den disk storlek som du vill använda.
   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna antalet är 2 048 GB för OS-diskar. (Det är möjligt att expandera VHD-blobben utöver den storleken, men operativ systemet fungerar endast med de första 2 048 GB utrymme.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Skärm bild som visar fönstret storlek och prestanda med den valda disk storleken.":::

6. Välj **ändra storlek** längst ned på sidan.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Skärm bild som visar fönstret storlek och prestanda med knappen Ändra storlek markerad.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Ändra storlek på en hanterad disk med hjälp av PowerShell

Öppna PowerShell ISE eller PowerShell-fönstret i administrations läge och följ stegen nedan:

1. Logga in på ditt Microsoft Azure-konto i resurs hanterings läge och välj din prenumeration:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Ange resurs gruppens namn och namnet på den virtuella datorn:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Hämta en referens till den virtuella datorn:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Stoppa den virtuella datorn innan du ändrar storlek på disken:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Hämta en referens till den hanterade OS-disken. Ställ in storleken på den hanterade OS-disken på önskat värde och uppdatera disken:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna antalet är 2 048 GB för OS-diskar. (Det går att expandera VHD-blobben utöver den storleken, men operativ systemet fungerar bara med de första 2 048 GB utrymme.)
    > 
         
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

Och sedan är du klar! Anslut till den virtuella datorn via RDP, öppna Datorhantering (eller Diskhantering) och expandera enheten med det nya utrymmet.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Ändra storlek på en ohanterad disk med hjälp av PowerShell

Öppna PowerShell ISE eller PowerShell-fönstret i administrations läge och följ stegen nedan:

1. Logga in på ditt Microsoft Azure-konto i resurs hanterings läge och välj din prenumeration:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Ange resurs gruppens namn och namn på virtuella datorer:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Hämta en referens till den virtuella datorn:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Stoppa den virtuella datorn innan du ändrar storlek på disken:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Ange storleken på den ohanterade OS-disken till önskat värde och uppdatera den virtuella datorn:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna antalet är 2 048 GB för OS-diskar. (Det går att expandera VHD-blobben utöver den storleken, men operativ systemet kommer bara att kunna arbeta med de första 2 048 GB utrymme.)
    > 
    > 
   
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Skript för OS-disk

Nedan visas det fullständiga skriptet för din referens för både hanterade och ohanterade diskar:


**Hanterade diskar**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Ohanterade diskar**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Ändra storlek på data diskar

Den här artikeln fokuserar främst på att utöka den virtuella datorns OS-disk, men skriptet kan också användas för att utöka data diskarna som är anslutna till den virtuella datorn. Om du exempelvis vill expandera den första disken som är ansluten till den virtuella datorn, ersätter du objektet `OSDisk` i `StorageProfile` med matrisen `DataDisks` och använder ett numeriskt index för att hämta en referens till den första anslutna disken, på följande sätt:

**Hanterad disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Ohanterad disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

På samma sätt kan du referera till andra data diskar som är anslutna till den virtuella datorn, antingen genom att använda ett index som visas ovan eller på diskens **namn** egenskap:


**Hanterad disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Ohanterad disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandera volymen i operativ systemet

När du har expanderat disken för den virtuella datorn måste du gå till operativ systemet och expandera volymen för att omfatta det nya utrymmet. Det finns flera metoder för att expandera en partition. I det här avsnittet beskrivs hur du ansluter den virtuella datorn via en RDP-anslutning för att expandera partitionen med **DiskPart**.

1. Öppna en RDP-anslutning till den virtuella datorn.

2. Öppna en kommando tolk och skriv **DiskPart**.

3. Skriv i **DiskPart** -prompten `list volume` . Anteckna den volym som du vill utöka.

4. Skriv i **DiskPart** -prompten `select volume <volumenumber>` . Detta väljer den volym *volumenumber* som du vill utöka till ett sammanhängande, tomt utrymme på samma disk.

5. Skriv i **DiskPart** -prompten `extend [size=<size>]` . Detta utökar den valda volymen efter *storlek* i megabyte (MB).


## <a name="next-steps"></a>Nästa steg

Du kan också ansluta diskar med hjälp av [Azure Portal](attach-managed-disk-portal.md).
