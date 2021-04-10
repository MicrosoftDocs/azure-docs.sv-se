---
title: Kopiera en bild från ett annat galleri med hjälp av PowerShell
description: Kopiera en bild från ett annat galleri med hjälp av Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d9bbe40e35bdad6fac5c5ccb0b15b909e77b938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564024"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopiera en bild från ett annat galleri med hjälp av PowerShell

Om du har flera gallerier i din organisation kan du skapa bilder från bilder som lagras i andra gallerier. Du kan till exempel ha ett utvecklings-och test Galleri för att skapa och testa nya avbildningar. När de är redo att användas i produktionen kan du kopiera dem till ett produktions galleri med hjälp av det här exemplet. Du kan också skapa en avbildning från en avbildning i ett annat galleri med hjälp av [Azure CLI](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln måste du ha ett befintligt käll Galleri, bild definition och avbildnings version. Du bör också ha ett mål Galleri. 

Käll avbildnings versionen måste replikeras till den region där mål galleriet finns. 

Vi kommer att skapa en ny avbildnings definition och avbildnings version i mål galleriet.


Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.


## <a name="get-the-source-image"></a>Hämta käll avbildningen 

Du behöver information från käll avbildnings definitionen så att du kan skapa en kopia av den i mål galleriet.

Visa information om befintliga gallerier, bild definitioner och avbildnings versioner med hjälp av cmdleten [Get-AzResource](/powershell/module/az.resources/get-azresource) .

Resultaten är i formatet `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

När du har all information du behöver kan du hämta ID för käll avbildnings versionen med hjälp av [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). I det här exemplet hämtar vi `1.0.0` avbildnings versionen av `myImageDefinition` definitionen i `myGallery` käll galleriet i `myResourceGroup` resurs gruppen.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Skapa avbildnings definitionen 

Du måste skapa en ny avbildnings definition som matchar bild definitionen för din källa. Du kan se all information som du behöver för att återskapa avbildnings definitionen med [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

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

Skapa en ny avbildnings definition i mål galleriet med hjälp av cmdleten [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) och informationen från utdata ovan.


I det här exemplet heter bild definitionen *myDestinationImgDef* i galleriet med namnet *myDestinationGallery*.


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


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa en avbildnings version med [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Du måste skicka in käll avbildningens ID i- `--managed-image` parametern för att skapa avbildnings versionen i mål galleriet. 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet heter mål galleriet *myDestinationGallery*, i resurs gruppen *MYDESTINATIONRG* på platsen *USA, västra* . Versionen av vår avbildning är *1.0.0* och vi kommer att skapa en replik i regionen USA, *södra centrala* och 2 repliker i regionen *västra USA* . 


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

Det kan ta en stund att replikera avbildningen till alla mål regioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet skriver du `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra avbildningen i premiun-lagringen genom att lägga till `-StorageAccountType Premium_LRS` eller [zonen redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `-StorageAccountType Standard_ZRS` när du skapar avbildnings versionen.
>


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad](vm-generalized-image-version-powershell.md) eller [specialiserad](vm-specialized-image-version-powershell.md) avbildnings version.

[Azure Image Builder (för hands version)](./image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](./linux/image-builder-gallery-update-image-version.md). 

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).