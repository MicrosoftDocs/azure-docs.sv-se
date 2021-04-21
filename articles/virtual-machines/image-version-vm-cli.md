---
title: Skapa en avbildning från en virtuell dator med hjälp av Azure CLI
description: Lär dig hur du skapar en avbildning i en Shared Image Gallery från en virtuell dator i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7bfe8b1255c88878c2dc4661e9daa3e16397e9f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792281"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Skapa en avbildningsversion från en virtuell dator i Azure med hjälp av Azure CLI

Om du har en befintlig virtuell dator som du vill använda för att skapa flera identiska virtuella datorer kan du använda den virtuella datorn för att skapa en avbildning i en Shared Image Gallery med hjälp av Azure CLI. Du kan också skapa en avbildning från en virtuell dator med [hjälp av Azure PowerShell](image-version-vm-powershell.md).

En **avbildningsversion** är det du använder för att skapa en virtuell dator när du använder Shared Image Gallery. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du använder en avbildningsversion för att skapa en virtuell dator används avbildningsversionen för att skapa diskar för den nya virtuella datorn. Avbildningsversioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln måste du ha en Shared Image Gallery. 

Du måste också ha en befintlig virtuell dator i Azure, i samma region som galleriet. 

Om den virtuella datorn har en ansluten datadisk får datadiskstorleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resursnamnen där det behövs.

## <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga med [az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

När du känner till namnet på den virtuella datorn och vilken resursgrupp den finns i hämtar du ID:t för den virtuella datorn med [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Skapa en avbildningsdefinition

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de avbildningsversioner som skapas i dem. 

Bilddefinitionsnamn kan består av versaler eller gemener, siffror, punkter, bindestreck och punkter. 

Kontrollera att bilddefinitionen är rätt typ. Om du har generaliserat den virtuella datorn (med Sysprep för Windows eller waagent -deprovision för Linux) bör du skapa en generaliserad avbildningsdefinition med hjälp av `--os-state generalized` . Om du vill använda den virtuella datorn utan att ta bort befintliga användarkonton skapar du en specialiserad avbildningsdefinition med `--os-state specialized` .

Mer information om de värden som du kan ange för en bilddefinition finns i [Bilddefinitioner](./shared-image-galleries.md#image-definitions).

Skapa en avbildningsdefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

I det här exemplet heter avbildningsdefinitionen *myImageDefinition* och är för en specialiserad [Linux](./shared-image-galleries.md#generalized-and-specialized-images) OS-avbildning. Om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem använder du `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa en avbildningsversion från den virtuella datorn [med az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet är versionen av avbildningen *1.0.0* och vi ska skapa 2 repliker  i regionen USA, västra *centrala,* 1 replik i regionen USA, södra centrala och 1 replik i regionen USA, östra *2* med zonredundant lagring. Replikeringsregionerna måste innehålla den region som den virtuella källdatorn finns i.

Ersätt värdet för i `--managed-image` det här exemplet med ID:t för den virtuella datorn från föregående steg.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen har skapats och replikerats helt innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra avbildningen i Premium Storage genom att lägga till `--storage-account-type  premium_lrs` eller [zonredundant lagring genom](../storage/common/storage-redundancy.md) att lägga till när du skapar `--storage-account-type  standard_zrs` avbildningsversionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från den [generaliserade avbildningen](vm-generalized-image-version-cli.md) med hjälp av Azure CLI.

Information om hur du tillhandahåller information om inköpsplan finns i Ange Azure Marketplace [när du skapar avbildningar.](marketplace-images.md)