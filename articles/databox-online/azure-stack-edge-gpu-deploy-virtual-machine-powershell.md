---
title: Distribuera virtuella datorer på din Azure Stack Edge-enhet via Azure PowerShell
description: Beskriver hur du skapar och hanterar virtuella datorer på en Azure Stack Edge-enhet med hjälp av Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 28988af0c1b3b5e4e5ce359abb617a66af816d69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439824"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Distribuera virtuella datorer på din Azure Stack Edge-enhet via Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du skapar och hanterar en virtuell dator (VM) på din Azure Stack Edge-enhet med hjälp av Azure PowerShell. Informationen gäller för Azure Stack Edge Pro med GPU (grafisk bearbetnings enhet), Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.

## <a name="vm-deployment-workflow"></a>Arbets flöde för distribution av virtuell dator

Arbets flödet för distribution visas i följande diagram:

![Diagram över arbets flödet för distribution av virtuella datorer.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Fråga efter en inbyggd prenumeration på enheten

För Azure Resource Manager stöds endast en enda fast prenumeration som är synlig för användaren. Den här prenumerationen är unik för varje enhet och prenumerations namnet och prenumerations-ID: t kan inte ändras.

Prenumerationen innehåller alla resurser som krävs för att skapa virtuella datorer. 

> [!IMPORTANT]
> Prenumerationen skapas när du aktiverar virtuella datorer från Azure Portal och den finns lokalt på enheten.

Prenumerationen används för att distribuera de virtuella datorerna.

1.  Om du vill visa en lista över prenumerationen kör du följande kommando:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Här är några exempel på utdata:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Hämta en lista över de registrerade resurs leverantörer som körs på enheten. Listan innehåller vanligt vis beräkning, nätverk och lagring.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Resurs leverantörerna är förregistrerade och kan inte ändras eller ändras.
    
    Här är några exempel på utdata:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resurs grupp är en logisk behållare där Azure-resurser, till exempel ett lagrings konto, en disk och en hanterad disk, distribueras och hanteras.

> [!IMPORTANT]
> Alla resurser skapas på samma plats som enheten och platsen anges till **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Här är några exempel på utdata:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett nytt lagrings konto med hjälp av resurs gruppen som du skapade i föregående steg. Det här är ett lokalt lagrings konto som du använder för att ladda upp den virtuella disk avbildningen för den virtuella datorn.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Genom att använda Azure Resource Manager kan du bara skapa lokala lagrings konton som lokalt redundant lagring (standard eller Premium). Information om hur du skapar skiktade lagrings konton finns i [Självstudier: överföra data via lagrings konton med Azure Stack Edge Pro med GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Här är några exempel på utdata:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Kör kommandot för att hämta lagrings konto nyckeln `Get-AzureRmStorageAccountKey` . Här är några exempel på utdata:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Lägg till BLOB-URI: n i värd filen

Du har redan lagt till BLOB-URI: n i värd filen för den klient som du använder för att ansluta till Azure Blob Storage i "steg 5: ändra värd filen för slut punkts namn matchning" för att [distribuera virtuella datorer på din Azure Stack Edge-enhet via Azure PowerShell](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Den här posten användes för att lägga till BLOB-URI: n:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>

## <a name="install-certificates"></a>Installera certifikat

Om du använder HTTPS måste du installera lämpliga certifikat på enheten. Här installerar du Blob-slutpunktens certifikat. Mer information finns i [använda certifikat med Azure Stack Edge Pro med GPU-enhet](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk

Kopiera eventuella disk avbildningar som ska användas i sid-blobar i det lokala lagrings konto som du skapade tidigare. Du kan använda ett verktyg som [AzCopy](../storage/common/storage-use-azcopy-v10.md) för att ladda upp den virtuella hård disken (VHD) till lagrings kontot. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Använd följande kommandon med AzCopy 10:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Här är några exempel på utdata: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>Skapa en hanterad disk från den virtuella hård disken

Kör följande kommando för att skapa en hanterad disk från den överförda virtuella hård disken:

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Här är några exempel på utdata: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Här är några exempel på utdata. Mer information om den här cmdleten finns i [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Skapa en avbildning av en virtuell dator från den hanterade avbildnings disken

Kör följande kommando för att skapa en avbildning av en virtuell dator från den hanterade disken. Ersätt *\<Disk name>* , *\<OS type>* , och *\<Disk size>* med verkliga värden.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Här är några exempel på utdata. Mer information om den här cmdleten finns i [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>Skapa din virtuella dator med tidigare skapade resurser

Innan du skapar och distribuerar den virtuella datorn måste du skapa ett virtuellt nätverk och associera ett virtuellt nätverks gränssnitt med det.

> [!IMPORTANT]
> Följande regler gäller:
> - Du kan bara skapa ett virtuellt nätverk, även över resurs grupper. Det virtuella nätverket måste ha exakt samma adress utrymme som det logiska nätverket.
> - Det virtuella nätverket kan bara ha ett undernät. Under nätet måste ha exakt samma adress utrymme som det virtuella nätverket.
> - När du skapar kortet för det virtuella nätverkskortet kan du bara använda metoden för statisk allokering. Användaren måste ange en privat IP-adress.

### <a name="query-the-automatically-created-virtual-network"></a>Fråga det automatiskt skapade virtuella nätverket

När du aktiverar beräkning från det lokala användar gränssnittet på din enhet skapas ett virtuellt nätverk `ASEVNET` automatiskt under `ASERG` resurs gruppen. 

Använd följande kommando för att fråga det befintliga virtuella nätverket:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Skapa ett kort för virtuellt nätverksgränssnitt

Om du vill skapa ett virtuellt nätverks gränssnitts kort med hjälp av det virtuella nätverkets undernät-ID kör du följande kommando:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Här är några exempel på utdata:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Om du vill skapa ett virtuellt nätverkskort för en virtuell dator kan du också skicka den offentliga IP-adressen. I den här instansen returnerar den offentliga IP-adressen den privata IP-adressen. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Nu kan du använda den virtuella dator avbildningen för att skapa en virtuell dator och koppla den till det virtuella nätverk som du skapade tidigare.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

När du har skapat och skapat den virtuella datorn använder du följande användar namn och lösen ord för att logga in på den.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Anslutnings anvisningarna kan vara olika beroende på om du har skapat en virtuell Windows-dator eller en virtuell Linux-dator.

### <a name="connect-to-a-linux-vm"></a>Ansluta till en virtuell Linux-dator

Gör följande för att ansluta till en virtuell Linux-dator:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Ansluta till en virtuell Windows-dator

Gör följande för att ansluta till en virtuell Windows-dator:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>Hantera den virtuella datorn

I följande avsnitt beskrivs några av de vanliga åtgärder som du kan skapa på din Azure Stack Edge Pro-enhet.

### <a name="list-vms-that-are-running-on-the-device"></a>Lista de virtuella datorer som körs på enheten

Kör följande kommando för att returnera en lista över alla virtuella datorer som körs på din Azure Stack Edge-enhet:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Aktivera den virtuella datorn

Kör följande cmdlet för att aktivera en virtuell dator som körs på enheten:

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Mer information om denna cmdlet finns i [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Pausa eller stänga av den virtuella datorn

Kör följande cmdlet för att stoppa eller stänga av en virtuell dator som körs på enheten:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Mer information om denna cmdlet finns i [cmdleten Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Lägg till en datadisk

Om arbets belastnings kraven på den virtuella datorn ökar kan du behöva lägga till en datadisk. Detta gör du genom att köra följande kommando:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Om du vill ta bort en virtuell dator från enheten kör du följande cmdlet:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Mer information om denna cmdlet finns i [cmdleten Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-cmdletar](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
