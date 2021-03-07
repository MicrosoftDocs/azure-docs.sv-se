---
title: Konfigurera Azure Image Builder-tjänstens behörigheter med PowerShell
description: Konfigurera krav för tjänsten Azure VM Image Builder inklusive behörigheter och privilegier med hjälp av PowerShell
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9f8793b6ea0ba454b66c525c2d53c1de2197d539
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440215"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Konfigurera Azure Image Builder-tjänstens behörigheter med PowerShell

När du registrerar dig för (AIB) ger detta behörigheten AIB-tjänst för att skapa, hantera och ta bort en resurs grupp för mellanlagring (IT_ *) och har behörighet att lägga till resurser i den, vilket krävs för avbildnings versionen. Detta görs av ett AIB tjänst huvud namn (SPN) som görs tillgängligt i prenumerationen vid en lyckad registrering.

Om du vill tillåta att Azure VM Image Builder distribuerar avbildningar till antingen de hanterade avbildningarna eller till ett delat avbildnings Galleri måste du skapa en Azure User-tilldelad identitet som har behörighet att läsa och skriva bilder. Om du använder Azure Storage måste du ha behörighet att läsa privata eller offentliga behållare.

Du måste konfigurera behörigheter och privilegier innan du skapar en avbildning. Följande avsnitt beskriver hur du konfigurerar möjliga scenarier med hjälp av PowerShell.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrera funktionerna

Först måste du registrera dig för Azure Image Builder-tjänsten. Registreringen ger behörighet att skapa, hantera och ta bort en resurs grupp för mellanlagring. Tjänsten har också behörighet att lägga till resurser som gruppen som krävs för avbildnings versionen.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Skapa en Azure-användare som tilldelats en hanterad identitet

Azure Image Builder kräver att du skapar en [Azure-användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure Image Builder använder den användarspecifika hanterade identiteten för att läsa bilder, skriva bilder och få åtkomst till Azure Storage-konton. Du ger identiteten behörighet att utföra vissa åtgärder i din prenumeration.

> [!NOTE]
> Tidigare använde Azure Image Builder Azure Image Builder-tjänstens huvud namn (SPN) för att ge behörighet till avbildnings resurs grupperna. Att använda SPN är inaktuellt. Använd en hanterad identitet som tilldelats av användare i stället.

I följande exempel visas hur du skapar en hanterad Azure-användare som tilldelats Azure. Ersätt inställningarna för plats hållaren genom att ange dina variabler.

| Inställning | Beskrivning |
|---------|-------------|
| \<Resource group\> | Resurs grupp där du kan skapa den hanterade identiteten som användaren tilldelats. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
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

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Exempel på befintligt VNET Azure-roll

I följande exempel skapas en Azure-roll som använder och distribuerar en befintlig VNET-avbildning. Sedan beviljar du den anpassade rollen till den användare-tilldelade hanterade identiteten för Azure Image Builder.

För att förenkla utbytet av värden i exemplet anger du först följande variabler. Ersätt inställningarna för plats hållaren genom att ange dina variabler.

| Inställning | Beskrivning |
|---------|-------------|
| \<Subscription ID\> | Ditt Azure-prenumerations-ID |
| \<Resource group\> | VNET-resurs grupp |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över Azure Image Builder](../image-builder-overview.md).
