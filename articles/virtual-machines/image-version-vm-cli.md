---
title: Skapa en avbildning från en virtuell dator med hjälp av Azure CLI
description: Lär dig hur du skapar en avbildning i ett delat avbildnings Galleri från en virtuell dator i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c35be8821b6763531b43ec85b10325e91f8bc5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556868"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Skapa en avbildnings version från en virtuell dator i Azure med hjälp av Azure CLI

Om du har en befintlig virtuell dator som du vill använda för att göra flera identiska virtuella datorer kan du använda den virtuella datorn för att skapa en avbildning i ett delat avbildnings galleri med hjälp av Azure CLI. Du kan också skapa en avbildning från en virtuell dator med hjälp av [Azure PowerShell](image-version-vm-powershell.md).

En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du använder en avbildnings version för att skapa en virtuell dator, används avbildnings versionen för att skapa diskar för den nya virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha ett befintligt delat avbildnings Galleri för att kunna slutföra den här artikeln. 

Du måste också ha en befintlig virtuell dator i Azure i samma region som ditt Galleri. 

Om den virtuella datorn har en kopplad datadisk får data disk storleken inte vara större än 1 TB.

Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.

## <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga med [AZ VM List](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

När du känner till namnet på den virtuella datorn och vilken resurs grupp det finns i hämtar du ID: t för den virtuella datorn med [AZ VM get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. 

Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

Se till att bild definitionen är av rätt typ. Om du har generaliserat den virtuella datorn (med Sysprep för Windows eller waagent för Linux) bör du skapa en generaliserad avbildnings definition med hjälp av `--os-state generalized` . Om du vill använda den virtuella datorn utan att ta bort befintliga användar konton skapar du en specialiserad avbildnings definition med hjälp av `--os-state specialized` .

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa en bild definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en [SPECIALISERAd](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. Använd om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem `--os-type Windows` . 

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


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa en avbildnings version från den virtuella datorn med [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa 2 repliker i regionen *västra centrala USA* , 1 replik i regionen *södra centrala USA* och 1 replik i regionen *USA, östra 2* med zon-redundant lagring. De replikerade regionerna måste innehålla den region som den virtuella käll datorn finns i.

Ersätt värdet för `--managed-image` i det här exemplet med ID: t för din virtuella dator från föregående steg.

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
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra din avbildning i Premium Storage genom att lägga till `--storage-account-type  premium_lrs` eller [zonens redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type  standard_zrs` när du skapar avbildnings versionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från den [generaliserade avbildningen](vm-generalized-image-version-cli.md) med hjälp av Azure CLI.

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).