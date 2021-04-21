---
title: Förhandsversion – Skapa en avbildningsversion som är krypterad med dina egna nycklar
description: Skapa en avbildningsversion i ett delat bildgalleri med hjälp av kund hanterade krypteringsnycklar.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759677"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Förhandsversion: Använda kund hanterade nycklar för kryptering av avbildningar

Bilder i ett delat bildgalleri lagras som ögonblicksbilder, så de krypteras automatiskt via kryptering på serversidan. Kryptering på serversidan använder 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffreringarna som är tillgängliga. Kryptering på serversidan är också FIPS 140-2-kompatibel. Mer information om de kryptografiska modulerna som underliggande Azure-hanterade diskar finns [i Kryptografi-API: Nästa generation](/windows/desktop/seccng/cng-portal).

Du kan förlita dig på plattform hanterade nycklar för kryptering av dina avbildningar eller använda dina egna nycklar. Du kan också använda båda tillsammans för dubbel kryptering. Om du väljer att hantera kryptering med dina  egna nycklar kan du ange en kundhanterad nyckel som ska användas för kryptering och dekryptering av alla diskar i dina avbildningar. 

Kryptering på serversidan via kund hanterade nycklar använder Azure Key Vault. Du kan antingen [importera DINA RSA-nycklar](../key-vault/keys/hsm-protected-keys.md) till nyckelvalvet eller generera nya RSA-nycklar i Azure Key Vault.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln kräver att du redan har en diskkrypteringsuppsättning i varje region där du vill replikera avbildningen:

- Om du bara vill använda en kund hanterad nyckel kan du läsa artiklarna om hur du aktiverar kund hanterade nycklar med kryptering på serversidan med hjälp [av Azure Portal](./disks-enable-customer-managed-keys-portal.md) [eller PowerShell.](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)

- Om du vill använda både plattforms-hanterade och kund-hanterade nycklar (för dubbel kryptering) kan du läsa artiklarna om att aktivera dubbel kryptering i vila med hjälp [av Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) [eller PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Du måste använda länken för [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) att komma åt Azure Portal. Dubbel kryptering i vila visas för närvarande inte i den offentliga Azure Portal om du inte använder den länken.

## <a name="limitations"></a>Begränsningar

När du använder kund hanterade nycklar för att kryptera bilder i ett delat bildgalleri gäller dessa begränsningar:   

- Krypteringsnyckeluppsättningar måste finnas i samma prenumeration som din avbildning.

- Krypteringsnyckeluppsättningar är regionala resurser, så varje region kräver en annan krypteringsnyckeluppsättning.

- Du kan inte kopiera eller dela avbildningar som använder kund hanterade nycklar. 

- När du har använt egna nycklar för att kryptera en disk eller avbildning kan du inte gå tillbaka till att använda plattformsbaserade nycklar för kryptering av dessa diskar eller avbildningar.


> [!IMPORTANT]
> Kryptering via kund hanterade nycklar finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och vi rekommenderar det inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

För den offentliga förhandsversionen måste du först registrera funktionen:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Det tar några minuter för registreringen att slutföras. Använd `Get-AzProviderFeature` för att kontrollera status för funktionsregistreringen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

När `RegistrationState` `Registered` returnerar kan du gå vidare till nästa steg.

Kontrollera providerregistreringen. Se till att den returnerar `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Om den inte returnerar `Registered` använder du följande kod för att registrera providers:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Om du vill ange en diskkrypteringsuppsättning för en avbildningsversion använder du  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) med `-TargetRegion` parametern : 

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

Du kan skapa en virtuell dator (VM) från ett delat avbildningsgalleri och använda kundbaserade nycklar för att kryptera diskarna. Syntaxen är samma som när du skapar en [generaliserad eller](vm-generalized-image-version-powershell.md) [specialiserad virtuell](vm-specialized-image-version-powershell.md) dator från en avbildning. Använd den utökade parameteruppsättningen och lägg till `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` i VM-konfigurationen.

För datadiskar lägger du till `-DiskEncryptionSetId $setID` parametern när du använder [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

För den offentliga förhandsversionen måste du först registrera dig för funktionen. Registreringen tar cirka 30 minuter.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Kontrollera status för funktionsregistreringen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

När den här koden `"state": "Registered"` returnerar kan du gå vidare till nästa steg.

Kontrollera registreringen:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Om den inte är registrerad kör du följande kommando:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Om du vill ange en diskkrypteringsuppsättning för en avbildningsversion använder [du az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) med `--target-region-encryption` parametern . Formatet för `--target-region-encryption` är en kommaavgränsad lista med nycklar för kryptering av operativsystem och datadiskar. Det bör se ut så här: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Om källan för OS-disken är en hanterad disk eller en virtuell dator använder du `--managed-image` för att ange källan för avbildningsversionen. I det här exemplet är källan en hanterad avbildning som har en OS-disk och en datadisk på LUN 0. OS-disken krypteras med DiskEncryptionSet1 och datadisken krypteras med DiskEncryptionSet2.

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

Om os-diskens källa är en ögonblicksbild använder du `--os-snapshot` för att ange OS-disken. Om det finns ögonblicksbilder av datadiskar som också ska ingå i avbildningsversionen lägger du till dem. Använd `--data-snapshot-luns` för att ange LUN och använda för att ange `--data-snapshots` ögonblicksbilderna.

I det här exemplet är källorna ögonblicksbilder av diskar. Det finns en OS-disk och en datadisk på LUN 0. OS-disken krypteras med DiskEncryptionSet1 och datadisken krypteras med DiskEncryptionSet2.

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

Du kan skapa en virtuell dator från ett delat avbildningsgalleri och använda kund hanterade nycklar för att kryptera diskarna. Syntaxen är samma som när du skapar en [generaliserad eller](vm-generalized-image-version-cli.md) [specialiserad virtuell](vm-specialized-image-version-cli.md) dator från en avbildning. Lägg bara till `--os-disk-encryption-set` parametern med ID:t för krypteringsuppsättningen. För datadiskar lägger du `--data-disk-encryption-sets` till med en blankstegsavgränsad lista över diskkrypteringsuppsättningarna för datadiskarna.


## <a name="portal"></a>Portalen

När du skapar avbildningsversionen i portalen kan du använda fliken **Kryptering för** att tillämpa dina lagringskrypteringsuppsättningar.

> [!IMPORTANT]
> Om du vill använda dubbel kryptering måste du använda länken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) för att komma åt Azure Portal. Dubbel kryptering i vila visas för närvarande inte i den offentliga Azure Portal om du inte använder den länken.


1. På sidan **Skapa en avbildningsversion** väljer du **fliken** Kryptering.
2. I **Krypteringstyp** väljer du **Kryptering i vila** med en kund hanterad nyckel eller Dubbel kryptering med **plattforms-hanterade och kund hanterade nycklar.** 
3. För varje disk i avbildningen väljer du en krypteringsuppsättning från **listrutan Diskkrypteringsuppsättning.** 

### <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan skapa en virtuell dator från en avbildningsversion och använda kund hanterade nycklar för att kryptera diskarna. När du skapar den virtuella datorn i portalen går du till fliken Diskar och väljer Kryptering i vila med **kund** hanterade nycklar eller Dubbel kryptering med **plattforms-hanterade** och **kund** hanterade nycklar för **Krypteringstyp.** Du kan sedan välja krypteringsuppsättningen från listrutan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [diskkryptering på serversidan.](./disk-encryption.md)

Information om hur du tillhandahåller information om inköpsplan finns i Ange Azure Marketplace när du [skapar avbildningar.](marketplace-images.md)