---
title: Använd Azure Image Builder för virtuella Linux-datorer som ger åtkomst till ett befintligt Azure VNET (för hands version)
description: Skapa virtuella Linux-avbildningar med Azure Image Builder som ger åtkomst till ett befintligt Azure VNET
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: b523168429aa05a148a6e814881d1338a1bb39cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695625"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Använd Azure Image Builder för virtuella Linux-datorer som ger åtkomst till ett befintligt Azure VNET

Den här artikeln visar hur du kan använda Azure Image Builder för att skapa en grundläggande anpassad Linux-avbildning som har åtkomst till befintliga resurser i ett VNET. Build VM som du skapar distribueras till ett nytt eller befintligt VNET som du anger i din prenumeration. När du använder ett befintligt Azure VNET kräver inte Azure Image Builder-tjänsten offentlig nätverks anslutning.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrera funktionerna

Först måste du registrera dig för Azure Image Builder-tjänsten. Registreringen ger behörighet att skapa, hantera och ta bort en resurs grupp för mellanlagring. Tjänsten har också behörighet att lägga till resurser som gruppen som krävs för avbildnings versionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Du kommer att använda vissa informations delar flera gånger. Skapa några variabler för att lagra informationen.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Konfigurera nätverksfunktioner

Om du inte har en befintlig VNET\Subnet\NSG kan du använda följande skript för att skapa ett.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Lägg till regel för nätverks säkerhets grupp

Den här regeln tillåter anslutning från Azure Image Builder-belastningsutjämnaren till den virtuella proxy-datorn. Port 60001 är för Linux-OSs och port 60000 för Windows OSs. Den virtuella proxykonfigurationen ansluter till build VM med port 22 för Linux eller port 5986 för Windows OSs.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Inaktivera princip för privata tjänster på undernät

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Mer information om nätverk i Image Builder finns i [nätverks alternativ för Azure Image Builder-tjänster](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Ändra exempel mal len och skapa rollen

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Ange behörigheter för resurs gruppen

Image Builder använder den [användar identitet](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) som tillhandahölls för att mata in avbildningen i Azure-galleriet för delad avbildning (sig). I det här exemplet ska du skapa en Azure-roll definition som innehåller detaljerade åtgärder för att distribuera avbildningen till SIG själv. Roll definitionen tilldelas sedan till användar identiteten.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

I stället för att ge Image Builder lägre granularitet och ökad behörighet kan du skapa två roller. En ger Builder-behörighet att skapa en avbildning, den andra gör det möjligt att ansluta den virtuella datorn och belastningsutjämnaren till ditt VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Mer information om behörigheter finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du fortsätter med att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildnings versionen som skapades av Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

SSH till den virtuella datorn.

```bash
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

Om du nu vill försöka att omanpassa avbildnings versionen för att skapa en ny version av samma avbildning, hoppar du över nästa steg och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).


Följande tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrol lera att du är färdig med distributionen innan du tar bort resurserna.

När du tar bort avbildnings Galleri resurser måste du ta bort alla avbildnings versioner innan du kan ta bort avbildnings definitionen som används för att skapa dem. Om du vill ta bort ett galleri måste du först ta bort alla bild definitionerna i galleriet.

Ta bort Image Builder-mallen.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Ta bort behörighets tilldelningar, roller och identiteter
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Ta bort resurs gruppen.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Om du har skapat ett VNET för den här snabb starten kan du ta bort det virtuella nätverket om det inte längre används.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures delade bild gallerier](../shared-image-galleries.md).
