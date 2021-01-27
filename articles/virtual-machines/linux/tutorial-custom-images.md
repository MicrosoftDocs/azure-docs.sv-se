---
title: Självstudie – Skapa anpassade VM-avbildningar med Azure CLI
description: I den här självstudien får du lära dig hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning i Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 956e7c18deb597f6347dfd5dfdca99709a7052a0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880985"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Självstudie: Skapa en anpassad avbildning av en virtuell Azure-dator med Azure CLI

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett avbildnings Galleri


I den här självstudien används CLI i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.4.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Översikt

Ett [delat bild galleri](../shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. 

Funktionen för delad bild galleri har flera resurs typer:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i den här självstudiekursen. Vid behov kan du se CLI- [snabb](quick-create-cli.md) starten för att skapa en virtuell dator som ska användas för den här självstudien. När du arbetar igenom självstudien ersätter du resurs namnen där det behövs.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. 

Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.   Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [AZ sig-Create](/cli/azure/sig#az-sig-create). I följande exempel skapas en resurs grupp med namnet *myGalleryRG* i *USA, östra* och ett galleri som heter *Galleri.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga med [AZ VM List](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

När du känner till namnet på den virtuella datorn och vilken resurs grupp det finns i hämtar du ID: t för den virtuella datorn med [AZ VM get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Kopiera ID: t för den virtuella datorn som ska användas senare.

## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. 

Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](../shared-image-galleries.md#image-definitions).

Skapa en bild definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en [SPECIALISERAd](../shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. 

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

Kopiera bild definitionens ID från det utdata som ska användas senare.

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
> Du kan också lagra avbildningen i premiun-lagringen genom att lägga till `--storage-account-type  premium_lrs` eller [zonen redundant lagring](../../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type  standard_zrs` när du skapar avbildnings versionen.
>

 
## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create) med hjälp av parametern--specialiserad för att indikera att avbildningen är en specialiserad avbildning. 

Använd bild Definitions-ID: t för för `--image` att skapa den virtuella datorn från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa den virtuella datorn från en angiven version genom att ange avbildningens versions-ID för `--image` . 

I det här exemplet skapar vi en virtuell dator från den senaste versionen av *myImageDefinition* -avbildningen.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Dela galleriet

Du kan dela avbildningar över prenumerationer med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du kan dela bilder i galleriet, bild definitionen eller avbildnings versionen leve. Alla användare som har Läs behörighet till en avbildnings version, även över prenumerationer, kan distribuera en virtuell dator med hjälp av avbildnings versionen.

Vi rekommenderar att du delar med andra användare på Galleri nivån. Om du vill hämta objekt-ID: t för galleriet använder du [AZ sig Visa](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID: t som ett omfång, tillsammans med en e-postadress och [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge en användare åtkomst till det delade avbildnings galleriet. Ersätt `<email-address>` och `<gallery iD>` med din egen information.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Mer information om hur du delar resurser med hjälp av Azure RBAC finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Image Builder

Azure erbjuder också en tjänst som bygger på Packer, [Azure VM Image Builder](../image-builder-overview.md). Du behöver bara beskriva dina anpassningar i en mall så att du kan hantera avbildnings skapandet. 

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett avbildnings Galleri

Gå vidare till nästa självstudie om du vill veta mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)