---
title: Självstudie – Använda en anpassad virtuell datoravbildning i en skalningsuppsättning med Azure CLI
description: Läs hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning som du kan använda för att distribuera en VM-skalningsuppsättning
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: a9a4abe550da4f0438f875127b3b689045c06e6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763009"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Självstudie: Skapa och använd en anpassad avbildning för VM-skalningsuppsättningar med Azure CLI
När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Om du vill minska antalet uppgifter när de virtuella datorinstanserna distribueras, kan du använda en anpassad virtuell datoravbildning. Den här anpassade virtuella datoravbildningen inkluderar alla nödvändiga programinstallationer eller konfigurationer. Alla virtuella datorinstanser som skapats i skalningsuppsättningen använder den anpassade virtuella datoravbildningen och är redo att hantera din programtrafik. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en specialiserad avbildningsdefinition
> * Skapa en avbildningsversion
> * Skapa en skalningsuppsättning från en specialiserad avbildning
> * Dela ett bildgalleri


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.4.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="overview"></a>Översikt

En [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) förenklar delning av anpassade avbildningar i organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med Shared Image Gallery kan du dela dina anpassade VM-avbildningar med andra. Välj vilka avbildningar du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. 

## <a name="create-and-configure-a-source-vm"></a>Skapa och konfigurera en virtuell källdator

Först skapar du en resursgrupp med [az group create](/cli/azure/group), därefter skapar du en virtuell dator med [az vm create](/cli/azure/vm). Den här virtuella datorn används sedan som källa för avbildningen. Följande exempel skapar en virtuell dator som heter *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> **ID:t** för den virtuella datorn visas i utdata från [kommandot az vm create.](/cli/azure/vm) Kopiera den till en säker plats så att du kan använda den senare i den här självstudien.

Den offentliga IP-adressen för den virtuella datorn visas också i utdata från [kommandot az vm create.](/cli/azure/vm) SSH till den virtuella datorns offentliga IP-adress enligt följande:

```console
ssh azureuser@<publicIpAddress>
```

Nu ska vi installera en grundläggande webbserver för att anpassa din virtuella dator. När den virtuella datorinstansen i skalningsuppsättningen sen distribueras så har den alla nödvändiga paket för att köra ett webbprogram. Använd `apt-get` för att installera *NGINX* på följande sätt:

```bash
sudo apt-get install -y nginx
```

När du är klar skriver du för `exit` att koppla från SSH-anslutningen.

## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri 

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. 

Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla bindestreck.   Gallerinamn måste vara unika i din prenumeration. 

Skapa ett avbildningsgalleri med [az sig create](/cli/azure/sig#az_sig_create). I följande exempel skapas en resursgrupp med namnet gallery *med namnet myGalleryRG* *i* USA, östra och ett galleri med namnet *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Skapa en avbildningsdefinition

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de avbildningsversioner som skapas i dem. 

Bilddefinitionsnamn kan består av versaler eller gemener, siffror, punkter, bindestreck och punkter. 

Kontrollera att bilddefinitionen är rätt typ. Om du har generaliserat den virtuella datorn (med Sysprep för Windows eller waagent -deprovision för Linux) bör du skapa en generaliserad avbildningsdefinition med hjälp av `--os-state generalized` . Om du vill använda den virtuella datorn utan att ta bort befintliga användarkonton skapar du en specialiserad avbildningsdefinition med `--os-state specialized` .

Mer information om de värden som du kan ange för en bilddefinition finns i [Bilddefinitioner.](../virtual-machines/shared-image-galleries.md#image-definitions)

Skapa en avbildningsdefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

I det här exemplet heter avbildningsdefinitionen *myImageDefinition* och är för en specialiserad [Linux OS-avbildning.](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem använder du `--os-type Windows` . 

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

> [!IMPORTANT]
> **ID:t** för bilddefinitionen visas i kommandots utdata. Kopiera den till en säker plats så att du kan använda den senare i den här självstudien.


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa en avbildningsversion från den virtuella datorn [med az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet är versionen av avbildningen *1.0.0* och vi ska skapa en replik i regionen USA, södra *centrala* och en replik i regionen USA, östra *2.* Replikeringsregionerna måste innehålla den region där den virtuella källdatorn finns.

Ersätt värdet för i `--managed-image` det här exemplet med ID:t för den virtuella datorn från föregående steg.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen är klar och replikeras innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra avbildningen i Premium Storage genom att lägga till `--storage-account-type  premium_lrs` eller [zonredundant lagring](../storage/common/storage-redundancy.md) genom att `--storage-account-type  standard_zrs` lägga till när du skapar avbildningsversionen.
>




## <a name="create-a-scale-set-from-the-image"></a>Skapa en skalningsuppsättning från avbildningen
Skapa en skalningsuppsättning från den specialiserade avbildningen med hjälp av [`az vmss create`](/cli/azure/vmss#az_vmss_create) . 

Skapa skalningsuppsättningen [`az vmss create`](/cli/azure/vmss#az_vmss_create) med hjälp av parametern --specialized för att ange att avbildningen är en specialiserad avbildning. 

Använd avbildningsdefinitions-ID:t för `--image` att skapa skalningsuppsättningsinstanser från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa skalningsuppsättningsinstanserna från en specifik version genom att ange avbildningsversions-ID:t för `--image` . 

Skapa en skalningsuppsättning med *namnet myScaleSet med* den senaste versionen av *avbildningen myImageDefinition* som vi skapade tidigare.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om vill låta trafik nå din skalningsuppsättning och verifiera att webbservern fungerar som den ska, skapar du en lastbalanseringsregel med [az network lb rule create](/cli/azure/network/lb/rule). Följande exempel skapar en regel med namnet *myLoadBalancerRuleWeb* som tillåter trafik på *TCP* port *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Om du vill se din skalningsuppsättning fungera, hämtar du den offentliga IP-adressen på din lastbalanserare med [az network public-ip show](/cli/azure/network/public-ip). Följande exempel hämtar IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Ange den offentliga IP-adressen i din webbläsare. Standardwebbsidan för NGINX visas som det visas i följande exempel:

![Nginx körs från en anpassad virtuell datoravbildning](media/tutorial-use-custom-image-cli/default-nginx-website.png)



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

Mer information om hur du delar resurser med Hjälp av Azure RBAC finns i [Lägga till eller ta bort Azure-rolltilldelningar med Azure CLI.](../role-based-access-control/role-assignments-cli.md)


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder en anpassad virtuell datoravbildning för din skalningsuppsättning med Azure CLI:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en specialiserad avbildningsdefinition
> * Skapa en avbildningsversion
> * Skapa en skalningsuppsättning från en specialiserad avbildning
> * Dela ett bildgalleri

Gå vidare till nästa självstudie för att lära dig hur du distribuerar program till din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Distribuera program till dina skalningsuppsättningar](tutorial-install-apps-cli.md)