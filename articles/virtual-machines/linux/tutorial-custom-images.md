---
title: Självstudie – Skapa anpassade VM-avbildningar med Azure CLI
description: I den här självstudien får du lära dig hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning i Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 81bbf0b49919db68407a85b9ea2f731c5f8e1d91
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769885"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Självstudie: Skapa en anpassad avbildning av en virtuell Azure-dator med Azure CLI

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildningsdefinition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett bildgalleri


Den här självstudien använder CLI [i Azure Cloud Shell](../../cloud-shell/overview.md), som ständigt uppdateras till den senaste versionen. Om du vill Cloud Shell väljer **du Testa** längst upp i ett kodblock.

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.4.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Översikt

En [Shared Image Gallery](../shared-image-galleries.md) förenklar delning av anpassade avbildningar i organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med Shared Image Gallery kan du dela dina anpassade VM-avbildningar med andra. Välj vilka avbildningar du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. 

Funktionen Shared Image Gallery har flera resurstyper:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i den här självstudiekursen. Om det behövs kan du se [CLI-snabbstarten för att](quick-create-cli.md) skapa en virtuell dator som ska användas för den här självstudien. När du går igenom självstudien ersätter du resursnamnen där det behövs.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri 

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. 

Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla bindestreck.   Gallerinamn måste vara unika i din prenumeration. 

Skapa ett avbildningsgalleri med [az sig create](/cli/azure/sig#az_sig_create). I följande exempel skapas en resursgrupp med namnet gallery med namnet *myGalleryRG* i *USA,* östra och ett galleri med namnet *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga med [az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

När du vet namnet på den virtuella datorn och vilken resursgrupp den finns i hämtar du ID:t för den virtuella datorn med [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Kopiera ID:t för den virtuella datorn för senare användning.

## <a name="create-an-image-definition"></a>Skapa en avbildningsdefinition

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de avbildningsversioner som skapas i dem. 

Bilddefinitionsnamn kan består av versaler eller gemener, siffror, punkter, bindestreck och punkter. 

Mer information om de värden som du kan ange för en bilddefinition finns i [Bilddefinitioner](../shared-image-galleries.md#image-definitions).

Skapa en avbildningsdefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create). 

I det här exemplet heter avbildningsdefinitionen *myImageDefinition* och är för en specialiserad [Linux](../shared-image-galleries.md#generalized-and-specialized-images) OS-avbildning. 

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

Kopiera ID:t för bilddefinitionen från utdata för senare användning.

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
> Du måste vänta tills avbildningsversionen är klar och replikeras innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra avbildningen i premiunlagring genom att lägga till eller zonredundant lagring genom att lägga `--storage-account-type  premium_lrs` till när du skapar [](../../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` avbildningsversionen.
>

 
## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med [az vm create med](/cli/azure/vm#az_vm_create) parametern --specialized för att indikera att avbildningen är en specialiserad avbildning. 

Använd avbildningsdefinitions-ID för att skapa den virtuella datorn från den `--image` senaste versionen av avbildningen som är tillgänglig. Du kan också skapa den virtuella datorn från en specifik version genom att ange avbildningsversions-ID:t för `--image` . 

I det här exemplet skapar vi en virtuell dator från den senaste versionen av avbildningen *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Dela galleriet

Du kan dela avbildningar mellan prenumerationer med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC). Du kan dela bilder i galleriet, bilddefinitionen eller bildversionen. Alla användare som har läsbehörighet till en avbildningsversion, även mellan prenumerationer, kommer att kunna distribuera en virtuell dator med avbildningsversionen.

Vi rekommenderar att du delar med andra användare på gallerinivå. Hämta galleriets objekt-ID med [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID:t som ett omfång, tillsammans med en e-postadress och [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) för att ge en användare åtkomst till det delade bildgalleriet. Ersätt `<email-address>` och med din egen `<gallery iD>` information.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Mer information om hur du delar resurser med Hjälp av Azure RBAC finns i [Lägga till eller ta bort Azure-rolltilldelningar med Azure CLI.](../../role-based-access-control/role-assignments-cli.md)

## <a name="azure-image-builder"></a>Azure Image Builder

Azure erbjuder också en tjänst som bygger på Packer, [Azure VM Image Builder](../image-builder-overview.md). Beskriv bara dina anpassningar i en mall så hanterar den skapandet av avbildningen. 

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildningsdefinition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett bildgalleri

Gå vidare till nästa självstudie om du vill veta mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)