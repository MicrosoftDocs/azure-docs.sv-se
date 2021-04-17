---
title: Skapa VM-avbildningar från en specialiserad avbildning av en virtuell Windows-hårddisk för Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du skapar VM-avbildningar från specialiserade avbildningar från en Windows-VHD eller en VHDX. Använd den här specialiserade avbildningen för att skapa VM-avbildningar som ska användas med virtuella datorer som distribueras Azure Stack Edge Pro GPU-enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575959"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Distribuera en virtuell dator från en specialiserad avbildning Azure Stack Edge Pro GPU-enheten via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs de steg som krävs för att distribuera en virtuell dator (VM) på din Azure Stack Edge Pro GPU-enhet från en specialiserad avbildning. 

Information om hur du förbereder en generaliserad avbildning för att distribuera virtuella datorer i Azure Stack Edge Pro GPU finns i [Förbereda generaliserad](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) avbildning från Windows VHD eller Förbereda generaliserad avbildning från en [ISO.](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)

## <a name="about-vm-images"></a>Om VM-avbildningar

En Windows VHD eller VHDX kan användas för att skapa en *specialiserad avbildning* eller en *generaliserad avbildning.* I följande tabell sammanfattas viktiga skillnader mellan de *specialiserade och* *generaliserade avbildningarna.*

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Arbetsflöde

Det avancerade arbetsflödet för att distribuera en virtuell dator från en specialiserad avbildning är:

1. Kopiera den virtuella hårddisken till ett lokalt lagringskonto på din GPU Azure Stack Edge Pro enhet.
1. Skapa en ny hanterad disk från den virtuella hårddisken.
1. Skapa en ny virtuell dator från den hanterade disken och anslut den hanterade disken.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan distribuera en virtuell dator på enheten via PowerShell kontrollerar du att:

- Du har åtkomst till en klient som du använder för att ansluta till din enhet.
    - Klienten kör ett operativsystem [som stöds.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - Klienten är konfigurerad för att ansluta till den lokala Azure Resource Manager på enheten enligt instruktionerna i [Anslut till Azure Resource Manager för din enhet.](azure-stack-edge-gpu-connect-resource-manager.md)

## <a name="verify-the-local-azure-resource-manager-connection"></a>Verifiera den lokala Azure Resource Manager anslutningen

Kontrollera att klienten kan ansluta till den lokala Azure Resource Manager. 

1. Anropa API:er för lokala enheter för att autentisera:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Ange användarnamnet `EdgeArmUser` och lösenordet för att ansluta via Azure Resource Manager. Om du inte återkallar lösenordet återställer du [lösenordet för Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) använder det här lösenordet för att logga in.

## <a name="deploy-vm-from-specialized-image"></a>Distribuera virtuell dator från specialiserad avbildning

Följande avsnitt innehåller stegvisa instruktioner för att distribuera en virtuell dator från en specialiserad avbildning.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Kopiera VHD till ett lokalt lagringskonto på enheten

Följ dessa steg om du vill kopiera en virtuell hårddisk till ett lokalt lagringskonto:

1. Kopiera den virtuella käll-VHD:en till ett lokalt bloblagringskonto på Azure Stack Edge.

1. Anteckna den resulterande URI:en. Du kommer att använda den här URI:en i ett senare steg.

    Information om hur du skapar och [](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) får åtkomst till ett lokalt lagringskonto finns i avsnitten Skapa ett lagringskonto via Ladda upp en [virtuell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) hårddisk i artikeln: Distribuera virtuella datorer på din [Azure Stack Edge-enhet via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Skapa en hanterad disk från en virtuell hårddisk

Följ dessa steg för att skapa en hanterad disk från en virtuell hårddisk som du laddade upp till lagringskontot tidigare:

1. Ange några parametrar.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Här är ett exempel på utdata.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Skapa en ny hanterad disk.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Här är ett exempel på utdata. Platsen här är inställd på platsen för det lokala lagringskontot och är alltid för alla lokala lagringskonton på `DBELocal` din Azure Stack Edge Pro GPU-enhet. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Skapa en virtuell dator från en hanterad disk

Följ dessa steg för att skapa en virtuell dator från en hanterad disk:

1. Ange några parametrar.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > Parametern `PrivateIP` är valfri. Använd den här parametern för att tilldela en statisk IP- annars är standardvärdet en dynamisk IP-adress med DHCP.

    Här är ett exempel på utdata. I det här exemplet anges samma resursgrupp för alla VM-resurser, men du kan skapa och ange separata resursgrupper för resurserna om det behövs.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Hämta informationen om det virtuella nätverket och skapa ett nytt nätverksgränssnitt.

    Det här exemplet förutsätter att du skapar ett enda nätverksgränssnitt i det virtuella standardnätverk `ASEVNET` som är associerat med standardresursgruppen `ASERG` . Om det behövs kan du ange ett alternativt virtuellt nätverk eller skapa flera nätverksgränssnitt. Mer information finns i Lägga [till ett nätverksgränssnitt till en virtuell dator via Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Här är ett exempel på utdata.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Skapa ett nytt vm-konfigurationsobjekt.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Lägg till nätverksgränssnittet till den virtuella datorn.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Ange egenskaperna för OS-disken på den virtuella datorn.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Den sista flaggan i det här kommandot är antingen `-Windows` eller beroende på vilket operativsystem du använder för den virtuella `-Linux` datorn.

1. Skapa den virtuella datorn.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Här är ett exempel på utdata. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Ta bort virtuell dator och resurser

I den här artikeln användes bara en resursgrupp för att skapa alla VM-resurser. Om du tar bort resursgruppen tas den virtuella datorn och alla associerade resurser bort. 

1. Visa först alla resurser som skapats under resursgruppen.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Här är ett exempel på utdata.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Ta bort resursgruppen och alla associerade resurser.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Här är ett exempel på utdata.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Kontrollera att resursgruppen har tagits bort. Hämta alla resursgrupper som finns på enheten. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Här är ett exempel på utdata.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Nästa steg

- [Förbereda en generaliserad avbildning från en virtuell Windows-hårddisk för att distribuera virtuella datorer Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Förbereda en generaliserad avbildning från en ISO för att distribuera virtuella datorer Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md) d