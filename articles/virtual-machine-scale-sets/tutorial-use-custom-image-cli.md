---
title: Självstudie – Använd en anpassad VM-avbildning i en skalnings uppsättning med Azure CLI
description: Läs hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning som du kan använda för att distribuera en VM-skalningsuppsättning
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683062"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Självstudie: Skapa och använd en anpassad avbildning för VM-skalningsuppsättningar med Azure CLI
När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Om du vill minska antalet uppgifter när de virtuella datorinstanserna distribueras, kan du använda en anpassad virtuell datoravbildning. Den här anpassade virtuella datoravbildningen inkluderar alla nödvändiga programinstallationer eller konfigurationer. Alla virtuella datorinstanser som skapats i skalningsuppsättningen använder den anpassade virtuella datoravbildningen och är redo att hantera din programtrafik. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en specialiserad avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en skalnings uppsättning från en specialiserad avbildning
> * Dela ett avbildnings Galleri


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.4.0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="overview"></a>Översikt

Ett [delat bild galleri](../virtual-machines/shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. 

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
> **ID: t** för den virtuella datorn visas i utdata från kommandot [AZ VM Create](/cli/azure/vm) . Kopiera den här okänd anledning säkert så att du kan använda den senare i den här självstudien.

Den offentliga IP-adressen för den virtuella datorn visas också i utdata från kommandot [AZ VM Create](/cli/azure/vm) . SSH till den virtuella datorns offentliga IP-adress enligt följande:

```console
ssh azureuser@<publicIpAddress>
```

Nu ska vi installera en grundläggande webbserver för att anpassa din virtuella dator. När den virtuella datorinstansen i skalningsuppsättningen sen distribueras så har den alla nödvändiga paket för att köra ett webbprogram. Använd `apt-get` för att installera *NGINX* på följande sätt:

```bash
sudo apt-get install -y nginx
```

När du är färdig skriver du `exit` för att koppla från ssh-anslutningen.

## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. 

Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.   Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [AZ sig-Create](/cli/azure/sig#az-sig-create). I följande exempel skapas en resurs grupp med namnet *myGalleryRG* i *USA, östra* och ett galleri som heter *Galleri.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. 

Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

Se till att bild definitionen är av rätt typ. Om du har generaliserat den virtuella datorn (med Sysprep för Windows eller waagent för Linux) bör du skapa en generaliserad avbildnings definition med hjälp av `--os-state generalized` . Om du vill använda den virtuella datorn utan att ta bort befintliga användar konton skapar du en specialiserad avbildnings definition med hjälp av `--os-state specialized` .

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](../virtual-machines/shared-image-galleries.md#image-definitions).

Skapa en bild definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en [SPECIALISERAd](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. Använd om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem `--os-type Windows` . 

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
> **ID: t** för bild definitionen visas i kommandots utdata. Kopiera den här okänd anledning säkert så att du kan använda den senare i den här självstudien.


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa en avbildnings version från den virtuella datorn med [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa en replik i regionen USA, *södra centrala* och 1 i regionen *USA, östra 2* . De replikerade regionerna måste innehålla den region som den virtuella käll datorn finns i.

Ersätt värdet för `--managed-image` i det här exemplet med ID: t för din virtuella dator från föregående steg.

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
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra din avbildning i Premium Storage genom att lägga till `--storage-account-type  premium_lrs` eller [zonens redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type  standard_zrs` den när du skapar avbildnings versionen.
>




## <a name="create-a-scale-set-from-the-image"></a>Skapa en skalnings uppsättning från avbildningen
Skapa en skalnings uppsättning från den specialiserade avbildningen med hjälp av [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Skapa skalnings uppsättningen med [`az vmss create`](/cli/azure/vmss#az-vmss-create) hjälp av parametern--specialiserad för att ange att avbildningen är en specialiserad avbildning. 

Använd bild Definitions-ID: t för för `--image` att skapa skalnings uppsättnings instanserna från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa skalnings uppsättnings instanser från en speciell version genom att ange avbildningens versions-ID för `--image` . 

Skapa en skalnings uppsättning med namnet *myScaleSet* den senaste versionen av *myImageDefinition* -avbildningen som vi skapade tidigare.

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

Du kan dela avbildningar över prenumerationer med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du kan dela bilder i galleriet, bild definitionen eller avbildnings versionen. Alla användare som har Läs behörighet till en avbildnings version, även över prenumerationer, kan distribuera en virtuell dator med hjälp av avbildnings versionen.

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

Mer information om hur du delar resurser med hjälp av Azure RBAC finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort din skalnings uppsättning och ytterligare resurser genom att ta bort resurs gruppen och alla dess resurser med [AZ Group Delete](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder en anpassad virtuell datoravbildning för din skalningsuppsättning med Azure CLI:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en specialiserad avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en skalnings uppsättning från en specialiserad avbildning
> * Dela ett avbildnings Galleri

Gå vidare till nästa självstudie för att lära dig hur du distribuerar program till din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Distribuera program till dina skalningsuppsättningar](tutorial-install-apps-cli.md)