---
title: Skapa en ny version av VM-avbildningen från en befintlig avbildnings version med hjälp av Azure Image Builder (för hands version)
description: Skapa en ny version av VM-avbildningen från en befintlig avbildnings version med hjälp av Azure Image Builder i Linux.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 26b0a989e6f16bfffe324dc31a739093b7d99802
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676212"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>För hands version: skapa en ny version av VM-avbildning från en befintlig avbildnings version med hjälp av Azure Image Builder i Linux

Den här artikeln visar hur du tar en befintlig avbildnings version i ett [delat avbildnings Galleri](../shared-image-galleries.md), uppdaterar den och publicerar den som en ny avbildnings version i galleriet.

Vi kommer att använda en Sample. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateforSIGfromSIG.jspå](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registrera funktionerna
Om du vill använda Azure Image Builder i för hands versionen måste du registrera den nya funktionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Kontrol lera status för funktions registreringen.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kontrol lera registreringen.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

Om de inte säger att de är registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter

Om du har använt [skapa en avbildning och distribuerar till ett delat avbildnings Galleri](image-builder-gallery.md) för att skapa ditt delade avbildnings Galleri har du redan skapat några av de variabler vi behöver. Om inte, måste du konfigurera vissa variabler som ska användas i det här exemplet.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med hjälp av `az account show | grep id` .

```console
subscriptionID=<Subscription ID>
```

Hämta avbildnings versionen som du vill uppdatera.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Skapa en användardefinierad identitet och ange behörigheter för resurs gruppen
När du har ställt in användar identiteten i föregående exempel behöver du bara hämta resurs-ID: t för den, så läggs det till i mallen.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Om du redan har ett eget galleri för delad avbildning, och inte har följt det tidigare exemplet, måste du tilldela behörigheter för Image Builder för att få åtkomst till resurs gruppen, så att den kan komma åt galleriet. Granska stegen i exemplet [skapa en avbildning och distribuera till ett delat avbildnings Galleri](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>Ändra helloImage-exempel
Du kan granska exemplet som vi ska använda genom att öppna. JSON-filen här: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) tillsammans med [Image Builder-mal len referens](image-builder-json.md). 


Hämta. JSON-exemplet och konfigurera den med dina variabler. 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildnings konfigurationen till tjänsten VM Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Vänta tills avbildningen har skapats och repliker ATS innan du går vidare till nästa steg.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Skapa en SSH-anslutning till den virtuella datorn med den offentliga IP-adressen för den virtuella datorn.

```console
ssh azureuser@<pubIp>
```

Du bör se att avbildningen har anpassats med ett "meddelande på dagen" så snart som SSH-anslutningen har upprättats.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Skriv `exit` för att stänga ssh-anslutningen.

Du kan också lista de avbildnings versioner som nu är tillgängliga i galleriet.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i. JSON-filen som används i den här artikeln finns i [referens för Image Builder-mallar](../linux/image-builder-json.md).