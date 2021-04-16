---
title: Kopiera en avbildning från ett annat galleri med PowerShell
description: Kopiera en bild från ett annat galleri med Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 35346836767bc1da8c498e23fd3b42afe7a9c350
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531194"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopiera en avbildning från ett annat galleri med PowerShell

Om du har flera gallerier i din organisation kan du skapa avbildningar från bilder som lagras i andra gallerier. Du kan till exempel ha ett utvecklings- och testgalleri för att skapa och testa nya bilder. När de är redo att användas i produktion kan du kopiera dem till ett produktionsgalleri med hjälp av det här exemplet. Du kan också skapa en avbildning från en avbildning i ett annat galleri med hjälp av [Azure CLI.](image-version-another-gallery-cli.md)


## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln måste du ha ett befintligt källgalleri, en bilddefinition och en bildversion. Du bör också ha ett målgalleri. 

Källavbildningsversionen måste replikeras till den region där målgalleriet finns. 

Vi kommer att skapa en ny bilddefinition och avbildningsversion i målgalleriet.


När du går igenom den här artikeln ersätter du resursnamnen där det behövs.


## <a name="get-the-source-image"></a>Hämta källavbildningen 

Du behöver information från källavbildningsdefinitionen så att du kan skapa en kopia av den i målgalleriet.

Visa information om befintliga gallerier, bilddefinitioner och avbildningsversioner med hjälp av cmdleten [Get-AzResource.](/powershell/module/az.resources/get-azresource)

Resultatet har formatet `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

När du har all information du behöver kan du hämta ID:t för källavbildningsversionen med hjälp av [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). I det här exemplet hämtar vi `1.0.0` avbildningsversionen, `myImageDefinition` av definitionen, i `myGallery` källgalleriet, i `myResourceGroup` resursgruppen.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Skapa avbildningsdefinitionen 

Du måste skapa en ny bilddefinition som matchar avbildningsdefinitionen för din källa. Du kan se all information som du behöver för att återskapa avbildningsdefinitionen med hjälp av [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


Utdata ser ut ungefär så här:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Skapa en ny avbildningsdefinition i målgalleriet med cmdleten [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) och informationen från utdata ovan.


I det här exemplet heter bilddefinitionen *myDestinationImgDef* i galleriet med namnet *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa en avbildningsversion [med New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Du måste skicka in ID:t för källavbildningen i `-Source` parametern för att skapa avbildningsversionen i målgalleriet. 

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet heter målgalleriet *myDestinationGallery* i resursgruppen *myDestinationRG* på platsen *USA,* västra. Versionen av avbildningen är *1.0.0* och vi ska skapa en replik i regionen USA, södra *centrala* och 2 repliker i regionen *USA,* västra. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Det kan ta en stund att replikera avbildningen till alla målregioner, så vi har skapat ett jobb så att vi kan följa förloppet. Om du vill se förloppet för jobbet skriver du `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen har skapats och replikerats helt innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra avbildningen i premiunlagring genom att lägga till eller zonredundant lagring genom att lägga `-StorageAccountType Premium_LRS` till när du skapar [](../storage/common/storage-redundancy.md) `-StorageAccountType Standard_ZRS` avbildningsversionen.
>


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad eller](vm-generalized-image-version-powershell.md) specialiserad avbildningsversion. [](vm-specialized-image-version-powershell.md)

[Azure Image Builder (förhandsversion)](./image-builder-overview.md) kan hjälpa dig att automatisera skapandet av avbildningsversion. Du kan till och med använda det för att uppdatera och skapa en ny [avbildningsversion från en befintlig avbildningsversion.](./linux/image-builder-gallery-update-image-version.md) 

Information om hur du tillhandahåller information om inköpsplanen finns i [Ange Azure Marketplace information om inköpsplanen när du skapar avbildningar.](marketplace-images.md)
