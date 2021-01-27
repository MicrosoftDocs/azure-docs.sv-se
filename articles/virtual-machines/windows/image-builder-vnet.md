---
title: Skapa en virtuell Windows-dator med Azure Image Builder med ett befintligt VNET (för hands version)
description: Skapa en virtuell Windows-dator med Azure Image Builder med ett befintligt VNET
author: cynthn
ms.author: cynthn
ms.date: 05/29/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: f4f55e9a5d08b2c2cd14b1ca94efac15f84f1978
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879535"
---
# <a name="use-azure-image-builder-for-windows-vms-allowing-access-to-an-existing-azure-vnet"></a>Använd Azure Image Builder för virtuella Windows-datorer som tillåter åtkomst till ett befintligt Azure VNET

Den här artikeln visar hur du kan använda Azure Image Builder för att skapa en grundläggande anpassad Windows-avbildning som har åtkomst till befintliga resurser i ett VNET. Build VM som du skapar distribueras till ett nytt eller befintligt VNET som du anger i din prenumeration. När du använder ett befintligt Azure VNET kräver inte Azure Image Builder-tjänsten offentlig nätverks anslutning.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrera funktionerna

Först måste du registrera dig för Azure Image Builder-tjänsten. Registreringen ger behörighet att skapa, hantera och ta bort en resurs grupp för mellanlagring. Tjänsten har också behörighet att lägga till resurser som gruppen som krävs för avbildnings versionen.

```powershell-interactive
# Register for Azure Image Builder Feature

Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'
```
## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Du kommer att använda vissa informations delar flera gånger. Skapa några variabler för att lagra informationen.

```powershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="aibImageRG"

# location (see possible locations in main docs)
$location="westus2"

## if you need to change your subscription: Get-AzSubscription / Select-AzSubscription -SubscriptionName 

# get subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# name of the image to be created
$imageName="win2019image01"

# image distribution metadata reference name
$runOutputName="win2019ManImg02ro"

# image template name
$imageTemplateName="window2019VnetTemplate03"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="winSvrSigR01"

# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
$vnetName="myexistingvnet01"
# subnet name
$subnetName="subnet01"
# VNET resource group name
$vnetRgName="existingVnetRG"
# Existing Subnet NSG Name or the demo will create it
$nsgName="aibdemoNsg"
# NOTE! The VNET must always be in the same region as the AIB service region.
```

Skapa resursgruppen.

```powershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="configure-networking"></a>Konfigurera nätverksfunktioner

Om du inte har en befintlig VNET\Subnet\NSG kan du använda följande skript för att skapa ett.

```powershell-interactive
New-AzResourceGroup -Name $vnetRgName -Location $location

## Create base NSG to simulate an existing NSG
New-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName -location $location

$nsg = Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName 

$subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.1.0/24" -PrivateLinkServiceNetworkPoliciesFlag "Disabled" -NetworkSecurityGroup $nsg

New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $subnet

## NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Lägg till regel för nätverks säkerhets grupp

Den här regeln tillåter anslutning från Azure Image Builder-belastningsutjämnaren till den virtuella proxy-datorn. Port 60001 är för Linux-OSs och port 60000 för Windows OSs. Den virtuella proxykonfigurationen ansluter till build VM med port 22 för Linux eller port 5986 för Windows OSs.

```powershell-interactive
Get-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $vnetRgName  | Add-AzNetworkSecurityRuleConfig -Name AzureImageBuilderAccess -Description "Allow Image Builder Private Link Access to Proxy VM" -Access Allow -Protocol Tcp -Direction Inbound -Priority 400 -SourceAddressPrefix AzureLoadBalancer -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 60000-60001 | Set-AzNetworkSecurityGroup
```

### <a name="disable-private-service-policy-on-subnet"></a>Inaktivera princip för privata tjänster på undernät

```powershell-interactive
$virtualNetwork= Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $vnetRgName 
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $subnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork
```

Mer information om nätverk i Image Builder finns i [nätverks alternativ för Azure Image Builder-tjänster](../linux/image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Ändra exempel mal len och skapa rollen

```powershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Win_Image_on_Existing_VNET/existingVNETWindows.json"
$templateFilePath = "existingVNETWindows.json"

$aibRoleNetworkingUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$aibRoleNetworkingPath = "aibRoleNetworking.json"

$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download configs
Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleNetworkingUrl -OutFile $aibRoleNetworkingPath -UseBasicParsing

Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

# update AIB image config template
((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imageName>',$imageName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<vnetName>',$vnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<subnetName>',$subnetName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $templateFilePath
```
## <a name="create-a-user-assigned-identity-and-set-permissions"></a>Skapa en användardefinierad identitet och ange behörigheter

```powershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

# create user identity
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

# update template with identity
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

# update the role defintion names
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role',$imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $aibRoleNetworkingPath

# update role definitions
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleNetworkingPath
((Get-Content -path $aibRoleNetworkingPath -Raw) -replace '<vnetRgName>',$vnetRgName) | Set-Content -Path $aibRoleNetworkingPath

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath

# create role definitions from role configurations examples, this avoids granting contributor to the SPN
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json
New-AzRoleDefinition -InputFile  ./aibRoleNetworking.json

# grant role definition to image builder user identity
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"
```

Mer information om behörigheter finns i [Konfigurera Azure Image Builder-tjänst behörigheter med Azure CLI](../linux/image-builder-permissions-cli.md) eller [Konfigurera Azure avbildning Builder-tjänst behörigheter med PowerShell](../linux/image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# note this will take minute, as validation is run (security / dependencies etc.)
```

Starta avbildnings versionen.

```powershell-interactive
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```

## <a name="get-image-build-status-and-properties"></a>Hämta status och egenskaper för avbildnings version

### <a name="query-the-image-template-for-current-or-last-run-status-and-image-template-settings"></a>Fråga avbildnings mal len efter aktuell eller senaste körnings status och inställningar för bildmall
```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl

$urlBuildStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id,$imageTemplateName)

$buildStatusResult = Invoke-WebRequest -Method GET  -Uri $urlBuildStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$buildJsonStatus =$buildStatusResult.Content
$buildJsonStatus

```

Avbildnings versionen för det här exemplet tar cirka 50 minuter (flera omstarter, Windows Update-installationer/omstart), när du frågar efter status måste du leta efter *lastRunStatus*, nedan visar att versionen fortfarande körs, om den har slutförts, visar den "lyckades".

```text
  "lastRunStatus": {
   "startTime": "2019-08-21T00:39:40.61322415Z",
   "endTime": "0001-01-01T00:00:00Z",
   "runState": "Running",
   "runSubState": "Building",
   "message": ""
  },
```

### <a name="query-the-distribution-properties"></a>Fråga distributions egenskaperna
Om du distribuerar till en VHD-plats, behöver hanterade avbildnings plats egenskaper eller status för delade avbildnings gallerier måste du fråga "runOutput", varje gång du har ett distributions mål, har du en unik runOutput för att beskriva egenskaperna för distributions typen.

```powerShell
$managementEp = $currentAzureContext.Environment.ResourceManagerUrl
$urlRunOutputStatus = [System.String]::Format("{0}subscriptions/{1}/resourceGroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/{2}?api-version=2020-02-14", $managementEp, $currentAzureContext.Subscription.Id, $runOutputName)

$runOutStatusResult = Invoke-WebRequest -Method GET  -Uri $urlRunOutputStatus -UseBasicParsing -Headers  @{"Authorization"= ("Bearer " + $accessToken)} -ContentType application/json 
$runOutJsonStatus =$runOutStatusResult.Content
$runOutJsonStatus
```
## <a name="create-a-vm"></a>Skapa en virtuell dator

Nu när versionen är färdig kan du bygga en virtuell dator från avbildningen. Använd exemplen från [PowerShell New-AzVM-dokumentationen](/powershell/module/az.compute/new-azvm#description).

## <a name="clean-up"></a>Rensa

### <a name="delete-image-template-artifact"></a>Ta bort bild mal len artefakt
```powerShell
# Get ResourceID of the Image Template
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"

### Delete Image Template Artifact
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force

```
### <a name="delete-role-assignment"></a>Ta bort roll tilldelning
```powerShell
## remove role assignments
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId  -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $networkRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$vnetRgName"

## remove definitions
Remove-AzRoleDefinition -Id $imageRoleDefObjId -Force
Remove-AzRoleDefinition -Id $networkRoleObjId -Force

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

### <a name="delete-resource-groups"></a>Ta bort resurs grupper
```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force


# delete VNET created
# BEWARE!!!!! In this example, you have either used an existing VNET or created one for this example. Do not delete your existing VNET. If you want to delete the VNET Resource group used in this example '$vnetRgName', modify the above code.
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures delade bild gallerier](../shared-image-galleries.md).
