---
title: För hands version – skapa en avbildnings version som är krypterad med dina egna nycklar
description: Skapa en avbildnings version i ett delat avbildnings Galleri genom att använda Kundhanterade krypterings nycklar.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554096"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>För hands version: Använd Kundhanterade nycklar för att kryptera avbildningar

Bilder i ett galleri för delade avbildningar lagras som ögonblicks bilder, så de krypteras automatiskt via kryptering på Server sidan. Kryptering på Server sidan använder 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga. Kryptering på Server sidan är också FIPS 140-2-kompatibel. Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](/windows/desktop/seccng/cng-portal).

Du kan förlita dig på plattforms hanterade nycklar för kryptering av dina avbildningar eller använda dina egna nycklar. Du kan också använda både tillsammans, för dubbel kryptering. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla diskar i dina avbildningar. 

Kryptering på Server sidan via Kundhanterade nycklar använder Azure Key Vault. Du kan antingen importera [dina RSA-nycklar](../key-vault/keys/hsm-protected-keys.md) till nyckel valvet eller generera nya RSA-nycklar i Azure Key Vault.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln kräver att du redan har en disk krypterings uppsättning i varje region där du vill replikera avbildningen:

- Om du bara vill använda en kundhanterad nyckel läser du artiklarna om hur du aktiverar Kundhanterade nycklar med kryptering på Server sidan med hjälp av [Azure Portal](./disks-enable-customer-managed-keys-portal.md) eller [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation).

- Om du vill använda både plattforms-hanterade och Kundhanterade nycklar (för dubbel kryptering) kan du läsa artiklarna om hur du aktiverar dubbel kryptering i vila med hjälp av [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) eller [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Du måste använda länken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) för att få åtkomst till Azure Portal. Dubbel kryptering i vila är för närvarande inte synligt i den offentliga Azure Portal om du inte använder den länken.

## <a name="limitations"></a>Begränsningar

När du använder Kundhanterade nycklar för att kryptera avbildningar i ett delat avbildnings Galleri gäller följande begränsningar:   

- Krypterings nyckel uppsättningar måste vara i samma prenumeration som din avbildning.

- Krypterings nyckel uppsättningar är regionala resurser, så varje region kräver en annan krypterings nyckel uppsättning.

- Du kan inte kopiera eller dela bilder som använder Kundhanterade nycklar. 

- När du har använt dina egna nycklar för att kryptera en disk eller avbildning kan du inte gå tillbaka till med hjälp av plattforms hanterade nycklar för kryptering av diskarna eller avbildningarna.


> [!IMPORTANT]
> Kryptering via Kundhanterade nycklar är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar den inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

För den allmänt tillgängliga för hands versionen måste du först registrera-funktionen:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Det tar några minuter för registreringen att slutföras. Använd `Get-AzProviderFeature` för att kontrol lera status för funktions registreringen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

När `RegistrationState` returer `Registered` kan du gå vidare till nästa steg.

Kontrol lera leverantörs registreringen. Se till att det returnerar `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Om den inte returneras `Registered` använder du följande kod för att registrera providern:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Om du vill ange en disk krypterings uppsättning för en avbildnings version använder du  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) med `-TargetRegion` parametern: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Du kan skapa en virtuell dator (VM) från ett delat avbildnings galleri och använda Kundhanterade nycklar för att kryptera diskarna. Syntaxen är densamma som att skapa en [generaliserad](vm-generalized-image-version-powershell.md) eller [specialiserad](vm-specialized-image-version-powershell.md) virtuell dator från en avbildning. Använd den utökade parameter uppsättningen och Lägg till i `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` VM-konfigurationen.

För data diskar lägger du till `-DiskEncryptionSetId $setID` parametern när du använder [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

För den allmänt tillgängliga för hands versionen måste du först registrera dig för funktionen. Registreringen tar cirka 30 minuter.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Kontrol lera status för funktions registreringen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

När den här koden returneras `"state": "Registered"` kan du gå vidare till nästa steg.

Kontrol lera registreringen:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Om det inte säger att det är registrerat kör du följande kommando:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Om du vill ange en disk krypterings uppsättning för en avbildnings version använder du [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create) med `--target-region-encryption` parametern. Formatet för `--target-region-encryption` är en kommaavgränsad lista över nycklar för kryptering av operativ system och data diskar. Den bör se ut så här: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Om källan för OS-disken är en hanterad disk eller en virtuell dator använder `--managed-image` du för att ange källan för avbildnings versionen. I det här exemplet är källan en hanterad avbildning som har en operativ system disk och en datadisk på LUN 0. Operativ system disken kommer att krypteras med DiskEncryptionSet1 och data disken krypteras med DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Om källan för operativ system disken är en ögonblicks bild använder `--os-snapshot` du för att ange OS-disken. Om det finns ögonblicks bilder av data diskar som även ska ingå i avbildnings versionen lägger du till dem. Använd `--data-snapshot-luns` för att ange LUN och Använd `--data-snapshots` för att ange ögonblicks bilderna.

I det här exemplet är källorna disk ögonblicks bilder. Det finns en OS-disk och en datadisk på LUN 0. Operativ system disken kommer att krypteras med DiskEncryptionSet1 och data disken krypteras med DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan skapa en virtuell dator från ett delat avbildnings galleri och använda Kundhanterade nycklar för att kryptera diskarna. Syntaxen är densamma som att skapa en [generaliserad](vm-generalized-image-version-cli.md) eller [specialiserad](vm-specialized-image-version-cli.md) virtuell dator från en avbildning. Lägg bara till `--os-disk-encryption-set` parametern med ID: t för krypterings uppsättningen. För data diskar lägger du till `--data-disk-encryption-sets` med en blankstegsavgränsad lista över disk krypterings uppsättningar för data diskarna.


## <a name="portal"></a>Portal

När du skapar din avbildnings version i portalen kan du använda fliken **kryptering** för att tillämpa dina lagrings krypterings uppsättningar.

> [!IMPORTANT]
> Om du vill använda Double Encryption måste du använda länken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) för att få åtkomst till Azure Portal. Dubbel kryptering i vila är för närvarande inte synligt i den offentliga Azure Portal om du inte använder den länken.


1. På sidan **skapa en avbildnings version** väljer du fliken **kryptering** .
2. I **krypterings typ** väljer du **kryptering i vila med en kundhanterad nyckel** eller **dubbel kryptering med plattforms hanterade och Kundhanterade nycklar**. 
3. Välj en krypterings uppsättning i list rutan **disk krypterings uppsättning** för varje disk i avbildningen. 

### <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan skapa en virtuell dator från en avbildnings version och använda Kundhanterade nycklar för att kryptera diskarna. När du skapar den virtuella datorn i portalen **väljer du** **kryptering i vila med Kundhanterade nycklar** eller **dubbel kryptering med plattforms hanterade och Kundhanterade nycklar** för **krypterings typ**. Du kan sedan välja krypterings uppsättningen i den nedrullningsbara listan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [disk kryptering på Server sidan](./disk-encryption.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).