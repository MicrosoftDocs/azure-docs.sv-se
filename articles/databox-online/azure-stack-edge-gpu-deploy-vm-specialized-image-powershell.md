---
title: Skapa VM-avbildningar från specialiserad avbildning av Windows VHD för din Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du skapar VM-avbildningar från specialiserade avbildningar som börjar från en Windows-VHD eller en VHDX. Använd den här specialiserade avbildningen för att skapa avbildningar av virtuella datorer som ska användas med virtuella datorer som distribuerats på Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556195"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Distribuera en virtuell dator från en specialiserad avbildning på din Azure Stack Edge Pro-enhet via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs de steg som krävs för att distribuera en virtuell dator (VM) på din Azure Stack Edge Pro-enhet från en specialiserad avbildning. 

## <a name="about-specialized-images"></a>Om specialiserade avbildningar

En Windows-VHD eller VHDX kan användas för att skapa en *specialiserad* avbildning eller en *generaliserad* avbildning. I följande tabell sammanfattas viktiga skillnader mellan de *specialiserade* och de *generaliserade* avbildningarna.


|Avbildningstyp  |Generaliserade  |Specialiserade  |
|---------|---------|---------|
|Mål     |Distribuerat i alla system         | Riktat mot ett speciellt system        |
|Konfigurera efter start     | Installations programmet krävs vid första starten av den virtuella datorn.          | Installationen behövs inte. <br> Plattformen aktiverar den virtuella datorn.        |
|Konfiguration     |Hostname, admin-user och andra VM-inställningar krävs.         |Förkonfigurerad.         |
|Används för att     |Skapa flera nya virtuella datorer från samma avbildning.         |Migrera en speciell dator eller Återställ en virtuell dator från en tidigare säkerhets kopia.         |


Den här artikeln beskriver de steg som krävs för att distribuera från en specialiserad avbildning. Information om hur du distribuerar från en generaliserad avbildning finns i [använda generaliserad Windows-VHD](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) för din enhet.


## <a name="vm-image-workflow"></a>Arbets flöde för VM-avbildning

Arbets flödet på hög nivå för att distribuera en virtuell dator från en specialiserad avbildning är:

1. Kopiera den virtuella hård disken till ett lokalt lagrings konto på din Azure Stack Edge Pro GPU-enhet.
1. Skapa en ny hanterad disk från den virtuella hård disken.
1. Skapa en ny virtuell dator från den hanterade disken och koppla den hanterade disken.


## <a name="prerequisites"></a>Förutsättningar

Innan du kan distribuera en virtuell dator på enheten via PowerShell kontrollerar du att:

- Du har åtkomst till en klient som du ska använda för att ansluta till din enhet.
    - Klienten kör ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Din klient är konfigurerad för att ansluta till den lokala Azure Resource Manager på enheten enligt anvisningarna i [Anslut till Azure Resource Manager för enheten](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Verifiera den lokala Azure Resource Manager anslutningen

Kontrol lera att klienten kan ansluta till den lokala Azure Resource Manager. 

1. Anropa lokala enhets-API: er för att autentisera:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Ange användar namn `EdgeArmUser` och lösen ord för att ansluta via Azure Resource Manager. Om du inte återkallar lösen ordet [återställer du lösen ordet för Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) och använder lösen ordet för att logga in.
 

## <a name="deploy-vm-from-specialized-image"></a>Distribuera virtuell dator från specialiserad avbildning

Följande avsnitt innehåller stegvisa instruktioner för att distribuera en virtuell dator från en specialiserad avbildning.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Kopiera VHD till lokalt lagrings konto på enhet

Följ dessa steg om du vill kopiera en virtuell hård disk till ett lokalt lagrings konto:

1. Kopiera den virtuella käll hård disken till ett lokalt Blob Storage-konto på din Azure Stack Edge. 

1. Anteckna den resulterande URI: n. Du kommer att använda denna URI i ett senare steg.
    
    Information om hur du skapar och använder ett lokalt lagrings konto finns i avsnitten [skapa ett lagrings konto](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) genom att [Ladda upp en virtuell hård disk](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) i artikeln: [distribuera virtuella datorer på Azure Stack Edge-enhet via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Skapa en hanterad disk från en virtuell hård disk

Följ de här stegen för att skapa en hanterad disk från en virtuell hård disk som du laddade upp till lagrings kontot tidigare:

1. Ange parametrar.

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

    Här är ett exempel på utdata. Platsen här anges till platsen för det lokala lagrings kontot och är alltid `DBELocal` för alla lokala lagrings konton på din Azure Stack Edge Pro GPU-enhet. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Skapa en virtuell dator från hanterad disk

Följ de här stegen för att skapa en virtuell dator från en hanterad disk:

1. Ange parametrar.

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
    > `PrivateIP`Parametern är valfri. Använd den här parametern för att tilldela en statisk IP-adress, annars är standardvärdet en dynamisk IP-adress med DHCP.

    Här är ett exempel på utdata. I det här exemplet anges samma resurs grupp för alla VM-resurser, men du kan skapa och ange separata resurs grupper för resurserna om det behövs.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Hämta information om det virtuella nätverket och skapa ett nytt nätverks gränssnitt.

    Det här exemplet förutsätter att du skapar ett enda nätverks gränssnitt på det virtuella standard nätverket `ASEVNET` som är associerat med standard resurs gruppen `ASERG` . Om det behövs kan du ange ett alternativt virtuellt nätverk eller skapa flera nätverks gränssnitt. Mer information finns i [lägga till ett nätverks gränssnitt till en virtuell dator via Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

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
1. Skapa ett nytt konfigurations objekt för virtuell dator.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Lägg till nätverks gränssnittet på den virtuella datorn.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Ange OS-diskens egenskaper på den virtuella datorn.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Den sista flaggan i det här kommandot är antingen `-Windows` eller `-Linux` beroende på vilket operativ system du använder för den virtuella datorn.

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

I den här artikeln används endast en resurs grupp för att skapa alla VM-resurser. Om du tar bort resurs gruppen tas den virtuella datorn och alla tillhör ande resurser bort. 

1. Först visar du alla resurser som skapats under resurs gruppen.

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

1. Ta bort resurs gruppen och alla tillhör ande resurser.

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

1. Kontrol lera att resurs gruppen har tagits bort. Hämta alla resurs grupper som finns på enheten. 

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

Beroende på vilken typ av distribution du har kan du välja någon av följande procedurer.

- [Distribuera en virtuell dator från en generaliserad avbildning via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Distribuera en virtuell dator via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
