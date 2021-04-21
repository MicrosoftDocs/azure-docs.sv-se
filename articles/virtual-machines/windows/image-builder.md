---
title: Skapa en virtuell Windows-dator med Azure Image Builder (förhandsversion)
description: Skapa en virtuell Windows-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764575"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Förhandsversion: Skapa en virtuell Windows-dator med Azure Image Builder

Den här artikeln visar hur du kan skapa en anpassad Windows-avbildning med hjälp av Azure VM Image Builder. Exemplet i den här artikeln använder [anpassare](../linux/image-builder-json.md#properties-customize) för att anpassa bilden:
- PowerShell (ScriptUri) – ladda ned och kör ett [PowerShell-skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows Restart – startar om den virtuella datorn.
- PowerShell (infogade) – kör ett specifikt kommando. I det här exemplet skapas en katalog på den virtuella datorn med hjälp av `mkdir c:\\buildActions` .
- Fil – kopiera en fil från GitHub till den virtuella datorn. I det här [exemplet index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) till på den `c:\buildArtifacts\index.html` virtuella datorn.
- buildTimeoutInMinutes – Öka byggtiden för att tillåta längre byggen, standardvärdet är 240 minuter och du kan öka byggtiden för att tillåta byggen som körs längre.
- vmProfile – ange egenskaper för vmSize och Network
- osDiskSizeGB – du kan öka storleken på avbildningen
- identity – tillhandahålla en identitet för Azure Image Builder som ska användas under bygget


Du kan också ange `buildTimeoutInMinutes` en . Standardvärdet är 240 minuter och du kan öka byggtiden för att tillåta längre byggen som körs.

Vi kommer att använda en .json-exempelmall för att konfigurera avbildningen. Den .json-fil som vi använder finns här: [helloImageTemplateWin.jspå](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Registrera funktionerna

Om du vill Image Builder Azure-portalen under förhandsversionen måste du registrera den nya funktionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Kontrollera status för funktionsregistreringen.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kontrollera registreringen.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Om de inte säger registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Ange variabler

Vi kommer att använda vissa uppgifter upprepade gånger, så vi skapar några variabler för att lagra den informationen.


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

Skapa en variabel för ditt prenumerations-ID. Du kan hämta detta med hjälp av `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Den här resursgruppen används för att lagra bildkonfigurationsmallens artefakt och avbildningen.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Skapa en användar tilldelad identitet och ange behörigheter för resursgruppen
Image Builder använder den [användaridentitet som tillhandahålls](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) för att mata in avbildningen i resursgruppen. I det här exemplet skapar du en Azure-rolldefinition som har detaljerade åtgärder för att distribuera avbildningen. Rolldefinitionen tilldelas sedan till användaridentiteten.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Skapa användar tilldelad hanterad identitet och bevilja behörigheter 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

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



## <a name="download-the-image-configuration-template-example"></a>Ladda ned exempel på avbildningskonfigurationsmall

En parametriserad avbildningskonfigurationsmall har skapats för att du ska kunna prova. Ladda ned .json-exempelfilen och konfigurera den med de variabler som du konfigurerade tidigare.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Du kan ändra det här exemplet i terminalen med hjälp av en textredigerare som `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> För källavbildningen måste du alltid [ange en version](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), du kan inte använda `latest` .
> Om du lägger till eller ändrar resursgruppen där avbildningen distribueras måste du ange [behörigheterna för](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) resursgruppen.
 
## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildningskonfigurationen till den virtuella Image Builder tjänsten

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

När du är klar returneras ett meddelande om att det lyckades till konsolen och du skapar `Image Builder Configuration Template` ett i `$imageResourceGroup` . Du kan se den här resursen i resursgruppen i Azure Portal om du aktiverar "Visa dolda typer".

I bakgrunden skapar Image Builder en mellanlagringsresursgrupp i din prenumeration. Den här resursgruppen används för avbildningsbygget. Det kommer att vara i det här formatet: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Du får inte ta bort resursgruppen för mellanlagring direkt. Ta först bort avbildningsmallens artefakt. Då tas resursgruppen för mellanlagring bort.

Om tjänsten rapporterar ett fel under överföringen av avbildningskonfigurationsmallen:
-  Gå igenom de [här felsökningsstegen.](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 
- Du måste ta bort mallen med hjälp av följande kodfragment innan du försöker skicka in igen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta avbildningsbygget
Starta processen för att skapa avbildningar [med az resource invoke-action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Vänta tills bygget är klart. Det kan ta cirka 15 minuter.

Om du stöter på fel kan du gå igenom de här [felsökningsstegen.](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med hjälp av den avbildning som du skapade. Ersätt *\<password>* med ditt eget lösenord för på den virtuella `aibuser` datorn.

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

Skapa en fjärrskrivbordsanslutning till den virtuella datorn med det användarnamn och lösenord som du konfigurerade när du skapade den virtuella datorn. I den virtuella datorn öppnar du en kommandotolk och skriver:

```console
dir c:\
```

Du bör se dessa två kataloger som skapades under avbildningsanpassningen:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Rensa

Ta bort resurserna när du är klar.

### <a name="delete-the-image-builder-template"></a>Ta bort image builder-mallen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Ta bort rolltilldelningen, rolldefinitionen och användaridentiteten.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Ta bort resursgruppen för avbildningen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i .json-filen som används i den här artikeln finns i [Mallreferens för Image Builder.](../linux/image-builder-json.md)
