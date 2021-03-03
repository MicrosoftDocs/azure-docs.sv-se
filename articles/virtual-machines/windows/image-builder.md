---
title: Skapa en virtuell Windows-dator med Azure Image Builder (för hands version)
description: Skapa en virtuell Windows-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 6e2dda624c66f5081720188ada20db10bf7ede5b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669077"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>För hands version: skapa en virtuell Windows-dator med Azure Image Builder

Den här artikeln visar hur du kan skapa en anpassad Windows-avbildning med hjälp av Image Builder för Azure VM. I exemplet i den här artikeln används [anpassningar](../linux/image-builder-json.md#properties-customize) för att anpassa avbildningen:
- PowerShell (ScriptUri) – Hämta och kör ett [PowerShell-skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows-omstart – startar om den virtuella datorn.
- PowerShell (infogat) – kör ett speciellt kommando. I det här exemplet skapar den en katalog på den virtuella datorn med hjälp av `mkdir c:\\buildActions` .
- Fil – Kopiera en fil från GitHub till den virtuella datorn. I det här exemplet kopieras [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) till `c:\buildArtifacts\index.html` på den virtuella datorn.
- buildTimeoutInMinutes – öka en Bygg tid för att tillåta längre versioner av versioner. standardvärdet är 240 minuter, och du kan öka Bygg tiden för att kunna köra versioner längre.
- vmProfile – ange en vmSize och nätverks egenskaper
- osDiskSizeGB – du kan öka bild storleken
- identitet – tillhandahåller en identitet för Azure Image Builder som ska användas under versionen


Du kan också ange en `buildTimeoutInMinutes` . Standardvärdet är 240 minuter, och du kan öka Bygg tiden för att kunna köra versioner längre.

Vi kommer att använda en Sample. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateWin.jspå](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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
```

Om de inte säger att de är registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables"></a>Ange variabler

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med hjälp av `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Den här resurs gruppen används för att lagra bild konfigurations mal len artefakt och avbildningen.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Skapa en användardefinierad identitet och ange behörigheter för resurs gruppen
Image Builder använder den [användar identitet](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) som tillhandahölls för att mata in avbildningen i resurs gruppen. I det här exemplet ska du skapa en Azure-roll definition som innehåller detaljerade åtgärder för att distribuera avbildningen. Roll definitionen tilldelas sedan till användar identiteten.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Skapa användare som tilldelats hanterad identitet och bevilja behörigheter 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Hämta exempel på en mall för avbildnings konfiguration

En parametriserad avbildnings konfigurations mal len har skapats för att du ska kunna prova. Ladda ned exempel. JSON-filen och konfigurera den med de variabler som du anger tidigare.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Du kan ändra det här exemplet i terminalen med hjälp av en text redigerare som `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> För käll avbildningen måste du alltid [Ange en version](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)som du inte kan använda `latest` .
> Om du lägger till eller ändrar resurs gruppen där avbildningen distribueras till, måste du [ange behörigheterna](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) för resurs gruppen.
 
## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildnings konfigurationen till tjänsten VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

När det är klart returneras ett meddelande till konsolen och du kan skapa en `Image Builder Configuration Template` i `$imageResourceGroup` . Du kan se den här resursen i resurs gruppen i Azure Portal om du aktiverar Visa dolda typer.

I bakgrunden skapar Image Builder också en resurs grupp för mellanlagring i din prenumeration. Den här resurs gruppen används för avbildnings versionen. Formatet är i följande format: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Du får inte ta bort mellanlagrings resurs gruppen direkt. Först tar du bort bild mal len artefakt. Detta innebär att den mellanlagrings resurs grupp som ska tas bort.

Om tjänsten rapporterar ett problem under överföringen av avbildnings konfigurations mal len:
-  Granska de här [fel söknings](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) stegen. 
- Du måste ta bort mallen med hjälp av följande kodfragment innan du försöker skicka igen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta Image-versionen
Starta processen för avbildnings skapande med [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Vänta tills versionen har slutförts. Detta kan ta ungefär 15 minuter.

Om du stöter på några fel kan du läsa följande [fel söknings](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) steg.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med den avbildning som du har skapat. Ersätt *\<password>* med ditt eget lösen ord för `aibuser` på den virtuella datorn.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verifiera anpassningen

Skapa en fjärr skrivbords anslutning till den virtuella datorn med det användar namn och lösen ord som du angav när du skapade den virtuella datorn. Öppna en kommando tolk i den virtuella datorn och skriv:

```console
dir c:\
```

Du bör se dessa två kataloger som skapas under bild anpassningen:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Rensa

När du är färdig tar du bort resurserna.

### <a name="delete-the-image-builder-template"></a>Ta bort Image Builder-mallen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Ta bort roll tilldelningen, roll definitionen och användar identiteten.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Ta bort avbildnings resurs gruppen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i. JSON-filen som används i den här artikeln finns i [referens för Image Builder-mallar](../linux/image-builder-json.md).
