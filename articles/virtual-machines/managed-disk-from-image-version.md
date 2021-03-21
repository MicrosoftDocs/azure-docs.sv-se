---
title: Skapa en hanterad disk från en avbildnings version
description: Skapa en hanterad disk från en avbildnings version i ett delat avbildnings Galleri.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 359816bbf3fb93a4ec2bfa3092adbb2236ae2f07
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562562"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Skapa en hanterad disk från en avbildnings version

Om det behövs kan du exportera operativ systemet eller en enskild datadisk från en avbildnings version som en hanterad disk från en avbildnings version som lagras i ett delat avbildnings Galleri.


## <a name="cli"></a>CLI

Visa en lista över avbildnings versionerna i ett galleri med hjälp av [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az_sig_image_version_list). I det här exemplet letar vi efter alla avbildnings versioner som ingår i *myImageDefinition* -bilddefinitionen i galleriet för *gallerier* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Ange `source` variabeln till ID för avbildnings versionen och Använd sedan [AZ disk Create](/cli/azure/disk#az_disk_create) för att skapa den hanterade disken. 

I det här exemplet exporterar vi OS-disken för avbildnings versionen för att skapa en hanterad disk med namnet *myManagedOSDisk* i regionen *östra* i en resurs grupp med namnet *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Om du vill exportera en datadisk från avbildnings versionen lägger du till `--gallery-image-reference-lun` för att ange LUN-platsen för data disken som ska exporteras. 

I det här exemplet exporterar vi data disken som finns på LUN 0 i avbildnings versionen för att skapa en hanterad disk med namnet *myManagedDataDisk* i regionen *östra* i en resurs grupp med namnet *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Visa en lista med bild versioner i ett galleri med [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

När du har all information du behöver kan du använda [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) för att hämta den käll avbildnings version som du vill använda och tilldela den till en variabel. I det här exemplet hämtar vi `1.0.0` avbildnings versionen av `myImageDefinition` definitionen i `myGallery` käll galleriet i `myResourceGroup` resurs gruppen.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

När du har angett `source` variabeln till ID för avbildnings versionen använder du [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) för att skapa en disk konfiguration och [New-AzDisk](/powershell/module/az.compute/new-azdisk) för att skapa disken. 

I det här exemplet exporterar vi OS-disken för avbildnings versionen för att skapa en hanterad disk med namnet *myManagedOSDisk* i regionen *östra* i en resurs grupp med namnet *myResourceGroup*. 

Skapa en disk konfiguration.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Skapa disken.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Om du vill exportera en datadisk på avbildnings versionen lägger du till ett LUN-ID i disk konfigurationen för att ange LUN-platsen för data disken som ska exporteras. 

I det här exemplet exporterar vi data disken som finns på LUN 0 i avbildnings versionen för att skapa en hanterad disk med namnet *myManagedDataDisk* i regionen *östra* i en resurs grupp med namnet *myResourceGroup*. 

Skapa en disk konfiguration.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Skapa disken.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en avbildnings version från en hanterad disk med hjälp av [Azure CLI](image-version-managed-image-cli.md) eller [PowerShell](image-version-managed-image-powershell.md).


