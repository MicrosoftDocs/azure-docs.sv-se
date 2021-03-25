---
title: Azure PowerShell – aktivera Kundhanterade nycklar med SSE-hanterade diskar
description: Aktivera kryptering på Server sidan med Kundhanterade nycklar på dina hanterade diskar med Azure PowerShell.
author: roygara
ms.date: 03/02/2021
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 37d248fd61cd8fb99259e3776447a719ae365ab9
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042891"
---
# <a name="azure-powershell---enable-customer-managed-keys-with-server-side-encryption---managed-disks"></a>Azure PowerShell – aktivera Kundhanterade nycklar med krypterings hanterade diskar på Server Sidan

Med Azure-disklagring kan du hantera dina egna nycklar när du använder SSE (Server Side Encryption) för hanterade diskar, om du väljer. Grundläggande information om SSE med Kundhanterade nycklar och andra typer av hanterade disk krypteringar finns i avsnittet [Kundhanterade nycklar](../disk-encryption.md#customer-managed-keys) i artikeln om disk kryptering.

## <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktive rad för disken kan du inte inaktivera den.
    Om du behöver kringgå detta måste du [Kopiera alla data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) till en helt annan hanterad disk som inte använder Kundhanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation"></a>Konfigurera en Azure Key Vault och DiskEncryptionSet utan automatisk nyckel rotation

Om du vill använda Kundhanterade nycklar med SSE måste du konfigurera en Azure Key Vault och en DiskEncryptionSet-resurs.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview"></a>Konfigurera en Azure Key Vault och DiskEncryptionSet med automatisk nyckel rotation (förhands granskning)

1. Kontrol lera att du har installerat den senaste [versionen av Azure PowerShell](/powershell/azure/install-az-ps)och att du är inloggad på ett Azure-konto i med `Connect-AzAccount` .
1. Skapa en instans av Azure Key Vault och krypterings nyckel.

    När du skapar Key Vault-instansen måste du aktivera rensnings skyddet. Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Den här inställningen skyddar dig från att förlora data på grund av oavsiktlig borttagning och är obligatorisk för kryptering av hanterade diskar.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Skapa en DiskEncryptionSet med hjälp av API-versionen `2020-12-01` och ange egenskapen `rotationToLatestKeyVersionEnabled` till sant via Azure Resource Manager mal len [CreateDiskEncryptionSetWithAutoKeyRotation.jspå](https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/AutoKeyRotation/CreateDiskEncryptionSetWithAutoKeyRotation.json)
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/AutoKeyRotation/CreateDiskEncryptionSetWithAutoKeyRotation.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $($keyVault.ResourceId) `
    -keyVaultKeyUrl $($key.Key.Kid) `
    -encryptionType "EncryptionAtRestWithCustomerKey" `
    -region $LocationName
    ```

1.  Ge DiskEncryptionSet-resursen åtkomst till nyckel valvet.

    > [!NOTE]
    > Det kan ta några minuter för Azure att skapa identiteten för din DiskEncryptionSet i din Azure Active Directory. Om du får ett fel meddelande som "det går inte att hitta Active Directory-objektet" när du kör följande kommando, väntar du några minuter och försöker igen.

    ```powershell
    $des=Get-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="examples"></a>Exempel

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande är exempel på skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en virtuell dator med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Skapa en tom disk som är krypterad med kryptering på Server sidan med Kundhanterade nycklar och koppla den till en virtuell dator

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

### <a name="encrypt-existing-managed-disks"></a>Kryptera befintliga hanterade diskar 

Dina befintliga diskar får inte anslutas till en virtuell dator som körs för att du ska kunna kryptera dem med följande skript:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

### <a name="encrypt-an-existing-virtual-machine-scale-set-with-sse-and-customer-managed-keys"></a>Kryptera en befintlig skalnings uppsättning för virtuella datorer med SSE och Kundhanterade nycklar 

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan:

```powershell
#set variables 
$vmssname = "name of the vmss that is already created"
$diskencryptionsetname = "name of the diskencryptionset already created"
$vmssrgname = "vmss resourcegroup name"
$diskencryptionsetrgname = "diskencryptionset resourcegroup name"

#get vmss object and create diskencryptionset object attach to vmss os disk
$ssevmss = get-azvmss -ResourceGroupName $vmssrgname -VMScaleSetName $vmssname
$ssevmss.VirtualMachineProfile.StorageProfile.OsDisk.ManagedDisk.DiskEncryptionSet = New-Object -TypeName Microsoft.Azure.Management.Compute.Models.DiskEncryptionSetParameters

#get diskencryption object and retrieve the resource id
$des = Get-AzDiskEncryptionSet -ResourceGroupName $diskencryptionsetrgname -Name $diskencryptionsetname
write-host "the diskencryptionset resource id is:" $des.Id

#associate DES resource id to os disk and update vmss 
$ssevmss.VirtualMachineProfile.StorageProfile.OsDisk.ManagedDisk.DiskEncryptionSet.id = $des.Id
$ssevmss | update-azvmss
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en skalnings uppsättning för virtuella datorer med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Ändra nyckeln för en DiskEncryptionSet för att rotera nyckeln för alla resurser som refererar till DiskEncryptionSet

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Hitta statusen för kryptering på Server sidan för en disk

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till de hanterade diskarna till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Nästa steg

- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Replikera datorer med Kundhanterade nycklar aktiverade diskar](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurera haveriberedskap till Azure för virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
