---
title: Skapa en virtuell dator från en uppladdad generaliserad virtuell hård disk
description: Överför en generaliserad virtuell hård disk till Azure och Använd den för att skapa nya virtuella datorer i distributions modellen för Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 6e3333ac780cfca02a6ce4f28d2b0e312016f713
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131516"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa nya virtuella datorer i Azure

Den här artikeln vägleder dig igenom hur du använder PowerShell för att ladda upp en virtuell hård disk för en generaliserad virtuell dator till Azure, skapa en avbildning från den virtuella hård disken och skapa en ny virtuell dator från avbildningen. Du kan ladda upp en virtuell hård disk som exporter ATS från ett lokalt Virtualization-verktyg eller från ett annat moln. Att använda [Managed disks](../managed-disks-overview.md) för den nya virtuella datorn FÖREN klar VM-hanteringen och ger bättre tillgänglighet när den virtuella datorn placeras i en tillgänglighets uppsättning. 

Ett exempel skript finns i [exempel skript för att ladda upp en virtuell hård disk till Azure och skapa en ny virtuell dator](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Innan du börjar

- Innan du laddar upp en virtuell hård disk till Azure bör du följa [förbereda en virtuell Windows-VHD eller VHDX för uppladdning till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Granska [planen för migreringen till Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) innan du påbörjar migreringen till [Managed disks](../managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisera den virtuella käll datorn med hjälp av Sysprep

Om du inte redan har gjort det måste du köra Sysprep på den virtuella datorn innan du laddar upp den virtuella hård disken till Azure. Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [Översikt över Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Kontrol lera att de Server roller som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för Server roller](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Om du planerar att köra Sysprep innan du laddar upp din virtuella hård disk till Azure för första gången, måste du se till att du har för [berett den virtuella](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)datorn. 
> 
> 

1. Logga in på den virtuella Windows-datorn.
1. Öppna Kommandotolken som administratör. 
1. Ta bort Panther-katalogen (C:\Windows\Panther).
1. Ändra katalogen till%WINDIR%\system32\sysprep och kör sedan `sysprep.exe` .
1. I dialog rutan **system förberedelse verktyg** väljer du **Använd OOBE (system out-of-Box Experience)** och kontrollerar att kryss rutan **generalize** är aktive rad.
1. För **avslutnings alternativ** väljer du **Stäng** av.
1. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
1. När Sysprep har slutförts stängs den virtuella datorn av. Starta inte om den virtuella datorn.


## <a name="upload-the-vhd"></a>Ladda upp den virtuella hård disken 

Nu kan du ladda upp en virtuell hård disk direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hård disk till Azure med Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



När den virtuella hård disken har laddats upp till den hanterade disken måste du använda [Get-AzDisk](/powershell/module/az.compute/get-azdisk) för att hämta den hanterade disken.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Skapa avbildningen
Skapa en hanterad avbildning från den generaliserade OS-hanterade disken. Ersätt följande värden med din egen information.

Ange först några variabler:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Skapa avbildningen med hjälp av den hanterade disken.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Skapa avbildningen.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. I det här exemplet skapas en virtuell dator med namnet *myVM* från *avbildningen* i *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Nästa steg

Logga in på den nya virtuella datorn. Mer information finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
