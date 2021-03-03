---
title: Använd Azure Image Builder & delade avbildnings galleriet för virtuella Linux-datorer (för hands version)
description: Lär dig hur du använder Azure Image Builder och Azure CLI för att skapa en avbildnings version i ett delat avbildnings galleri och sedan distribuera avbildningen globalt.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: cf7f5ed4a27772a89d0356c60e6f7135786ca07d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695608"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>För hands version: skapa en Linux-avbildning och distribuera den till ett delat avbildnings Galleri 

Den här artikeln visar hur du kan använda Azure Image Builder och Azure CLI för att skapa en avbildnings version i ett [delat avbildnings Galleri](../shared-image-galleries.md)och sedan distribuera avbildningen globalt. Du kan också göra detta med hjälp av [Azure PowerShell](../windows/image-builder-gallery.md).


Vi kommer att använda en Sample. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateforSIG.jspå](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

För att distribuera avbildningen till ett delat bild galleri använder mallen [sharedImage](image-builder-json.md#distribute-sharedimage) som värde för `distribute` avsnittet i mallen.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Network | grep registrationState
```

Om de inte säger att de är registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen.

För för hands versionen stöder Image Builder bara att skapa anpassade avbildningar i samma resurs grupp som den hanterade avbildningen. Uppdatera resurs grupp namnet i det här exemplet så att det blir samma resurs grupp som din käll hanterade avbildning.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med hjälp av `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Skapa en användardefinierad identitet och ange behörigheter för resurs gruppen
Image Builder använder den [användar identitet](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) som tillhandahölls för att mata in avbildningen i Azure-galleriet för delad avbildning (sig). I det här exemplet ska du skapa en Azure-roll definition som innehåller detaljerade åtgärder för att distribuera avbildningen till SIG själv. Roll definitionen tilldelas sedan till användar identiteten.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Skapa en bild definition och ett galleri

Om du vill använda Image Builder med ett delat bild galleri måste du ha ett befintligt bild galleri och en bild definition. Image Builder skapar inte bild galleriet och bild definitionen åt dig.

Om du inte redan har ett galleri och en bild definition som ska användas börjar du med att skapa dem. Börja med att skapa ett bild galleri.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Skapa sedan en avbildnings definition.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Hämta och konfigurera. JSON

Hämta. JSON-mallen och konfigurera den med dina variabler.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Nästa del kommer att skapa avbildnings versionen i galleriet. 

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du fortsätter med att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildnings versionen som skapades av Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH till den virtuella datorn.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Du bör se att avbildningen har anpassats till ett *meddelande om dygnet* så snart din SSH-anslutning har upprättats!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du nu vill försöka anpassa avbildnings versionen för att skapa en ny version av samma avbildning, hoppar du över nästa steg och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).


Detta tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrol lera att du är färdig med distributionen innan du tar bort resurserna.

När du tar bort avbildnings Galleri resurser måste du ta bort alla avbildnings versioner innan du kan ta bort avbildnings definitionen som används för att skapa dem. Om du vill ta bort ett galleri måste du först ta bort alla bild definitionerna i galleriet.

Ta bort Image Builder-mallen.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Ta bort behörighets tilldelningar, roller och identitet
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Hämta avbildnings versionen som skapats av Image Builder, detta börjar alltid med och `0.` tar sedan bort avbildnings versionen

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Ta bort avbildnings definitionen.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Ta bort galleriet.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Ta bort resurs gruppen.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures delade bild gallerier](../shared-image-galleries.md).
