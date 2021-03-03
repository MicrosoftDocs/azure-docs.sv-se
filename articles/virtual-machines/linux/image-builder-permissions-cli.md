---
title: Konfigurera Azure Image Builder-tjänstens behörigheter med Azure CLI
description: Konfigurera krav för tjänsten Azure VM Image Builder inklusive behörigheter och behörigheter med hjälp av Azure CLI
author: cynthn
ms.author: danis
ms.date: 03/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f9b60af2c9fe16f834ce3098266c03afe2b99667
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695438"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Konfigurera Azure Image Builder-tjänstens behörigheter med Azure CLI

Azure Image Builder-tjänsten kräver konfiguration av behörigheter och behörigheter innan en avbildning skapas. Följande avsnitt beskriver hur du konfigurerar möjliga scenarier med hjälp av Azure CLI.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrera funktionerna

Först måste du registrera dig för Azure Image Builder-tjänsten. Registreringen ger behörighet att skapa, hantera och ta bort en resurs grupp för mellanlagring. Tjänsten har också behörighet att lägga till resurser som gruppen som krävs för avbildnings versionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Skapa en Azure-användare som tilldelats en hanterad identitet

Azure Image Builder kräver att du skapar en [Azure-användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure Image Builder använder den användarspecifika hanterade identiteten för att läsa bilder, skriva bilder och få åtkomst till Azure Storage-konton. Du ger identiteten behörighet att utföra vissa åtgärder i din prenumeration.

> [!NOTE]
> Tidigare använde Azure Image Builder Azure Image Builder-tjänstens huvud namn (SPN) för att ge behörighet till avbildnings resurs grupperna. Att använda SPN är inaktuellt. Använd en hanterad identitet som tilldelats av användare i stället.

I följande exempel visas hur du skapar en hanterad Azure-användare som tilldelats Azure. Ersätt inställningarna för plats hållaren genom att ange dina variabler.

| Inställning | Beskrivning |
|---------|-------------|
| \<Resource group\> | Resurs grupp där du kan skapa den hanterade identiteten som användaren tilldelats. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Mer information om Azure User-tilldelade identiteter finns i dokumentationen för [Azure User-tilldelade hanterade identiteter](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) om hur du skapar en identitet.

## <a name="allow-image-builder-to-distribute-images"></a>Tillåt att Image Builder distribuerar bilder

För att Azure Image Builder ska distribuera avbildningar (hanterade avbildningar/delade avbildnings gallerier) måste tjänsten Azure Image Builder tillåtas att mata in avbildningarna i dessa resurs grupper. Om du vill bevilja de behörigheter som krävs måste du skapa en användardefinierad hanterad identitet och ge den behörighet till den resurs grupp där avbildningen har skapats. Azure Image Builder har **inte** behörighet att komma åt resurser i andra resurs grupper i prenumerationen. Du måste vidta uttryckliga åtgärder för att tillåta åtkomst för att förhindra att dina versioner fungerar.

Du behöver inte bevilja den användare som tilldelats behörighet för hanterad identitet i resurs gruppen för att distribuera avbildningar. Den användare som tilldelats den hanterade identiteten behöver dock följande Azure- `Actions` behörigheter i distributions resurs gruppen:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Om du distribuerar till ett delat avbildnings Galleri behöver du också:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Behörighet att anpassa befintliga avbildningar

För att Azure Image Builder ska bygga bilder från anpassade käll avbildningar (hanterade avbildningar/delade avbildnings gallerier), måste tjänsten Azure Image Builder tillåtas läsa avbildningarna till dessa resurs grupper. Om du vill bevilja de behörigheter som krävs måste du skapa en användardefinierad hanterad identitet och ge den behörighet till resurs gruppen där avbildningen finns.

Bygg från en befintlig anpassad avbildning:

```Actions
Microsoft.Compute/galleries/read
```

Bygg från en befintlig version av den delade avbildnings galleriet:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Behörighet att anpassa avbildningar på din virtuella nätverk

Azure Image Builder har möjlighet att distribuera och använda ett befintligt VNET i din prenumeration, vilket ger anpassningar till gång till anslutna resurser.

Du behöver inte bevilja den användare som tilldelats åtkomst till hanterade identiteter i resurs gruppen för att distribuera en virtuell dator till ett befintligt VNET. Den användarspecifika hanterade identiteten behöver dock följande Azure- `Actions` behörigheter i VNet-resurs gruppen:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Skapa en Azure-roll definition

I följande exempel skapas en Azure-roll definition från åtgärderna som beskrivs i föregående avsnitt. Exemplen används på resurs grupps nivå. Utvärdera och testa om exemplen är tillräckligt detaljerade för dina behov. För ditt scenario kan du behöva förfina det till ett speciellt delat avbildnings Galleri.

Avbildnings åtgärderna tillåter Läs-och skriv åtgärder. Bestäm vad som är lämpligt för din miljö. Du kan till exempel skapa en roll för att tillåta Azure Image Builder att läsa bilder från resurs grupp *exempel-RG-1* och skriva bilder till resurs grupp *exempel – RG-2*.

### <a name="custom-image-azure-role-example"></a>Exempel på Azure-roll för anpassad avbildning

I följande exempel skapas en Azure-roll som använder och distribuerar en anpassad käll avbildning. Sedan beviljar du den anpassade rollen till den användare-tilldelade hanterade identiteten för Azure Image Builder.

För att förenkla utbytet av värden i exemplet anger du först följande variabler. Ersätt inställningarna för plats hållaren genom att ange dina variabler.

| Inställning | Beskrivning |
|---------|-------------|
| \<Subscription ID\> | Ditt Azure-prenumerations-ID |
| \<Resource group\> | Resurs grupp för anpassad avbildning |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Exempel på befintligt VNET Azure-roll

I följande exempel skapas en Azure-roll som använder och distribuerar en befintlig VNET-avbildning. Sedan beviljar du den anpassade rollen till den användare-tilldelade hanterade identiteten för Azure Image Builder.

För att förenkla utbytet av värden i exemplet anger du först följande variabler. Ersätt inställningarna för plats hållaren genom att ange dina variabler.

| Inställning | Beskrivning |
|---------|-------------|
| \<Subscription ID\> | Ditt Azure-prenumerations-ID |
| \<Resource group\> | VNET-resurs grupp |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Använda hanterad identitet för Azure Storage åtkomst

Om du vill smidigt autentisera med Azure Storage och använda privata behållare, behöver Azure Image Builder en användardefinierad hanterad identitet i Azure. Azure Image Builder använder identiteten för att autentisera med Azure Storage.

> [!NOTE]
> Azure Image Builder använder bara identiteten vid sändnings tiden för bildmallen. Den virtuella datorns version har inte åtkomst till identiteten under image-genereringen.

Använd Azure CLI för att skapa den hanterade identiteten som användaren tilldelats.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

I Image Builder-mallen måste du ange den användare som tilldelats den hanterade identiteten.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Ersätt följande plats hållarnas inställningar:

| Inställning | Beskrivning |
|---------|-------------|
| \<Region\> | Mall region |
| \<Resource group\> | Resursgrupp |
| \<Storage account container\> | Namn på lagringskontocontainer |
| \<Subscription ID\> | Azure-prenumeration |

Mer information om hur du använder en användardefinierad hanterad identitet finns i avsnittet [skapa en anpassad avbildning som ska använda en Azure User-Assigned-hanterad identitet för att komma åt filer Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity). Snabb starten guidar dig genom hur du skapar och konfigurerar den användarspecifika hanterade identiteten för åtkomst till ett lagrings konto.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över Azure Image Builder](../image-builder-overview.md).
