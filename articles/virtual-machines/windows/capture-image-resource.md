---
title: Skapa en hanterad avbildning i Azure
description: Skapa en hanterad avbildning av en generaliserad virtuell dator eller virtuell hård disk i Azure. Avbildningar kan användas för att skapa flera virtuella datorer som använder hanterade diskar.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9b63ec5b8a5d0684a0e144de7dfe4114af9777e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881897"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure

Du kan skapa en hanterad avbildningsresurs från en generaliserad virtuell dator (VM) som lagras antingen som en hanterad disk eller en ohanterad disk i ett lagringskonto. Avbildningen kan sedan användas till att skapa flera virtuella datorer. Information om hur hanterade avbildningar faktureras finns i [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks/). 

En hanterad avbildning har stöd för upp till 20 samtidiga distributioner. Om du försöker skapa fler än 20 virtuella datorer samtidigt, från samma hanterade avbildning, kan timeout uppstå för etablerings tids gränsen på grund av lagrings prestanda begränsningarna för en enda virtuell hård disk. Om du vill skapa fler än 20 virtuella datorer samtidigt använder du en avbildning av [delade avbildnings gallerier](../shared-image-galleries.md) som kon figurer ATS med 1 replik för varje 20 samtidiga VM-distributioner.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera den virtuella Windows-datorn med hjälp av Sysprep

Sysprep tar bort allt ditt personliga konto och din säkerhets information och förbereder sedan datorn som ska användas som en avbildning. Information om Sysprep finns i [Översikt över Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Kontrol lera att de Server roller som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för Server roller](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) och [scenarier som inte stöds](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> När du har kört Sysprep på en virtuell dator betraktas den virtuella datorn som *generaliserad* och kan inte startas om. Det går inte att ångra processen för att generalisera en virtuell dator. Om du behöver behålla den ursprungliga virtuella datorn bör du skapa en [kopia av den virtuella datorn](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) och generalisera dess kopia. 
>
>För Sysprep krävs att enheterna är helt dekrypterade. Om du har aktiverat kryptering på den virtuella datorn inaktiverar du kryptering innan du kör Sysprep.
>
> Om du planerar att köra Sysprep innan du laddar upp den virtuella hård disken (VHD) till Azure för första gången ser du till att du har för [berett den virtuella](prepare-for-upload-vhd-image.md)datorn.  
> 
> 

Följ dessa steg om du vill generalisera din virtuella Windows-dator:

1. Logga in på din virtuella Windows-dator.
   
2. Öppna ett kommando tolks fönster som administratör. 

3. Ta bort Panther-katalogen (C:\Windows\Panther). Ändra katalogen till%WINDIR%\system32\sysprep och kör sedan `sysprep.exe` .
   
4. I dialog rutan **system förberedelse verktyg** väljer du **Skriv system out-of-Box Experience (OOBE)** och markerar kryss rutan **generalisera** .
   
5. För **avslutnings alternativ** väljer du **Stäng** av.
   
6. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. När Sysprep har slutförts stängs den virtuella datorn av. Starta inte om den virtuella datorn.

> [!TIP]
> **Valfritt** Använd [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) för att optimera din avbildning och minska den första start tiden för den virtuella datorn.
>
> För att optimera din avbildning, montera din virtuella hård disk genom att dubbelklicka på den i Utforskaren och kör sedan DISM med `/optimize-image` parametern.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Där D: är den monterade virtuella hård diskens sökväg.
>
> Körningen `DISM /optimize-image` bör vara den senaste ändringen du gör till din virtuella hård disk. Om du gör några ändringar i din virtuella hård disk innan du distribuerar måste du köra `DISM /optimize-image` igen.

## <a name="create-a-managed-image-in-the-portal"></a>Skapa en hanterad avbildning i portalen 

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera avbildningen av den virtuella datorn. Sök efter och välj **virtuella datorer**.

2. Välj den virtuella datorn i listan.

3. På sidan **virtuell dator** för den virtuella datorn väljer du **avbilda** på den övre menyn.

   Sidan **Skapa avbildning** visas.

4. För **namn**, Godkänn antingen det ifyllda namnet eller ange ett namn som du vill använda för avbildningen.

5. För **resurs grupp** väljer du **Skapa ny** och anger ett namn eller väljer en resurs grupp som du vill använda i list rutan.

6. Om du vill ta bort den virtuella käll datorn när avbildningen har skapats väljer du **ta bort den här virtuella datorn automatiskt när du har skapat avbildningen**.

7. Om du vill kunna använda avbildningen i valfri [tillgänglighets zon](../../availability-zones/az-overview.md)väljer du **på** för **zon återhämtning**.

8. Välj **Skapa** för att skapa avbildningen.

När avbildningen har skapats kan du hitta den som en **avbildnings** resurs i listan över resurser i resurs gruppen.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Skapa en avbildning av en virtuell dator med hjälp av PowerShell

 

Genom att skapa en avbildning direkt från den virtuella datorn kan du se till att avbildningen innehåller alla diskar som är associerade med den virtuella datorn, inklusive OS-disken och eventuella data diskar. Det här exemplet visar hur du skapar en hanterad avbildning från en virtuell dator som använder hanterade diskar.

Kontrol lera att du har den senaste versionen av Azure PowerShell-modulen innan du börjar. Du hittar versionen genom att köra `Get-Module -ListAvailable Az` i PowerShell. Om du behöver uppgradera kan du läsa [installera Azure PowerShell på Windows med PowerShellGet](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt kör `Connect-AzAccount` du för att skapa en anslutning till Azure.


> [!NOTE]
> Om du vill lagra avbildningen i zoner – redundant lagring måste du skapa den i en region som har stöd för [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkludera `-ZoneResilient` parametern i avbildnings konfigurationen ( `New-AzImageConfig` kommandot).

Följ dessa steg om du vill skapa en avbildning av en virtuell dator:

1. Skapa vissa variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Se till att den virtuella datorn har frigjorts.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ange statusen för den virtuella datorn som **generaliserad**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Hämta den virtuella datorn. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Skapa en avbildning från en hanterad disk med hjälp av PowerShell

Om du bara vill skapa en avbildning av operativ system disken anger du det hanterade disk-ID: t som OS-disk:

    
1. Skapa vissa variabler. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Hämta den virtuella datorn.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Hämta ID: t för den hanterade disken.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Skapa en bild från en ögonblicks bild med PowerShell

Du kan skapa en hanterad avbildning från en ögonblicks bild av en generaliserad virtuell dator genom att följa dessa steg:

    
1. Skapa vissa variabler. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Hämta ögonblicks bilden.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Skapa en avbildning från en virtuell dator som använder ett lagrings konto

Om du vill skapa en hanterad avbildning från en virtuell dator som inte använder hanterade diskar behöver du URI för OS-VHD: n i lagrings kontot i följande format: https://*mystorageaccount*. blob.Core.Windows.net/*vhdcontainer* / *vhdfilename. VHD*. I det här exemplet är den virtuella hård disken i *mystorageaccount*, i en behållare med namnet *vhdcontainer* och VHD-filnamnet är *vhdfilename. VHD*.


1.  Skapa vissa variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Stoppa/frigör den virtuella datorn.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markera den virtuella datorn som generaliserad.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Skapa avbildningen med hjälp av den generaliserade OS-VHD: n.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nästa steg
- [Skapa en virtuell dator från en hanterad avbildning](create-vm-generalized-managed.md). 