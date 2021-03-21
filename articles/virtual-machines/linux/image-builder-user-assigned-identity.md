---
title: Skapa en avbildning av en virtuell dator och Använd en användardefinierad hanterad identitet för att få åtkomst till filer i Azure Storage (för hands version)
description: Skapa en avbildning av en virtuell dator med hjälp av Azure Image Builder, som har åtkomst till filer som lagras i Azure Storage med hjälp av användardefinierad hanterad identitet.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9bcb7a94cdf1d5478db32a22ba6e612a90c53ed9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695387"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Skapa en avbildning och Använd en användardefinierad hanterad identitet för att komma åt filer i Azure Storage 

Azure Image Builder stöder användning av skript eller att kopiera filer från flera platser, till exempel GitHub och Azure Storage osv. För att kunna använda dessa måste de ha extern åtkomst till Azure Image Builder, men du kan skydda Azure Storage blobbar med SAS-token.

Den här artikeln visar hur du skapar en anpassad avbildning med hjälp av Azure VM Image Builder där tjänsten använder en [användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att komma åt filer i Azure Storage för avbildnings anpassningen, utan att du behöver göra filerna offentligt tillgängliga eller ställa in SAS-token.

I exemplet nedan skapas två resurs grupper, en används för den anpassade avbildningen och den andra kommer att vara värd för ett Azure Storage-konto som innehåller en skript fil. Detta simulerar ett verkligt livs scenario där du kan bygga artefakter eller bildfiler i olika lagrings konton utanför Image Builder. Du skapar en tilldelad identitet och beviljar sedan Läs behörighet till skript filen, men du kommer inte att ange någon offentlig åtkomst till filen. Sedan använder du Shell-anpassningen för att ladda ned och köra skriptet från lagrings kontot.


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


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med hjälp av `az account show | grep id` .

```console
subscriptionID=<Your subscription ID>
```

Skapa resurs grupper för både avbildningen och skript lagringen.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Skapa en användardefinierad identitet och ange behörigheter för resurs gruppen.

Image Builder använder den [användar identitet](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) som tillhandahölls för att mata in avbildningen i resurs gruppen. I det här exemplet ska du skapa en Azure-roll definition som innehåller detaljerade åtgärder för att distribuera avbildningen. Roll definitionen tilldelas sedan till användar identiteten.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Skapa lagringen och kopiera exempel skriptet till det från GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Ge Image Builder behörighet att skapa resurser i resurs gruppen avbildning. `--assignee`Värdet är användar identitets-ID: t.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Ändra exemplet

Ladda ned exempel. JSON-filen och konfigurera den med de variabler som du har skapat.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Vänta tills skapandet har slutförts. Detta kan ta ungefär 15 minuter.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator från avbildningen. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

När den virtuella datorn har skapats startar du en SSH-session med den virtuella datorn.

```console
ssh aibuser@<publicIp>
```

Du bör se att avbildningen har anpassats till ett meddelande om dygnet så snart din SSH-anslutning har upprättats!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Rensa

När du är färdig kan du ta bort resurserna om de inte längre behövs.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med att arbeta med Azure Image Builder, se [fel sökning](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).
