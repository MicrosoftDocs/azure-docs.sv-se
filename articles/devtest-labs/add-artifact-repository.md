---
title: Lägga till en artefakt lagrings plats i labbet i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du anger din egen artefakt lagring för ditt labb i Azure DevTest Labs till Store-verktyg som inte är tillgängliga i den offentliga artefakt lagrings platsen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7553f6b1afa416a5428577a8313bdadb669e32c2
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270996"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Lägga till en artefakt lagrings plats i labbet i DevTest Labs
Med DevTest Labs kan du ange en artefakt som ska läggas till i en virtuell dator vid tidpunkten för att skapa den virtuella datorn eller efter att den virtuella datorn har skapats. Den här artefakten kan vara ett verktyg eller ett program som du vill installera på den virtuella datorn. Artefakter definieras i en JSON-fil som läses in från en GitHub-eller Azure DevOps git-lagringsplats.

Det [offentliga artefakt lagret](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), som underhålls av DevTest Labs, innehåller många vanliga verktyg för både Windows och Linux. En länk till den här lagrings platsen läggs automatiskt till i labbet. Du kan skapa ett eget artefakt lager med specifika verktyg som inte är tillgängliga i den offentliga artefakt lagrings platsen. Information om hur du skapar anpassade artefakter finns i [skapa anpassade artefakter](devtest-lab-artifact-author.md).

Den här artikeln innehåller information om hur du lägger till din anpassade artefakt lagrings plats med hjälp av Azure Portal, Azure Resource Management-mallar och Azure PowerShell. Du kan automatisera att lägga till en artefakt lagrings plats i ett labb genom att skriva PowerShell-eller CLI-skript.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Om du vill lägga till en lagrings plats i labbet hämtar du först viktig information från din lagrings plats. I följande avsnitt beskrivs hur du hämtar den information som krävs för databaser som finns på **GitHub** eller **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta URL för kloning av GitHub-lagringsplats och personlig åtkomst-token

1. Gå till start sidan för GitHub-lagringsplatsen som innehåller definitionerna för artefakt-eller Resource Manager-mallen.
2. Välj **Klona eller ladda ned**.
3. Kopiera URL: en till Urklipp genom att välja URL-knappen **https-kloning** . Spara URL: en för senare användning.
4. I det övre högra hörnet av GitHub väljer du profil avbildningen och väljer sedan **Inställningar**.
5. I menyn **personliga inställningar** till vänster väljer du inställningar för **utvecklare**.
6. Välj **personliga** åtkomsttoken på den vänstra menyn.
7. Välj **generera ny token**.
8. På sidan **ny personlig åtkomst-token** under **token-Beskrivning**anger du en beskrivning. Godkänn standard objekt under **Välj omfång**och välj sedan **skapa token**.
9. Spara den genererade token. Du använder token senare.
10. Stäng GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Hämta URL för Azure databaser-kloning och personlig åtkomst-token
1. Gå till start sidan för din grupp samling (till exempel `https://contoso-web-team.visualstudio.com` ) och välj sedan ditt projekt.
2. På projektets start sida väljer du **kod**.
3. Om du vill visa klon-URL: en på sidan projekt **kod** väljer du **klona**.
4. Spara URL: en. Du använder URL: en senare.
5. Om du vill skapa en personlig åtkomsttoken går du till den nedrullningsbara menyn användar konto och väljer **min profil**.
6. På sidan profil information väljer du **säkerhet**.
7. På fliken **säkerhet > personliga** åtkomsttoken väljer du **+ ny token**.
8. På sidan **skapa en ny personlig åtkomst-token** :
   1. Ange ett **namn** för token.
   2. I listan **organisation** väljer du **alla tillgängliga organisationer**.
   3. I listan **förfallo datum (UTC)** väljer du **90 dagar**eller en anpassad definierad förfallo period.
   4. Välj alternativet **fullständig åtkomst** för omfattningar.
   5. Välj **Skapa**.
9. Den nya token visas i listan med **personliga åtkomst-token** . Välj **Kopiera token**och spara sedan token-värdet för senare användning.
10. Fortsätt till avsnittet Anslut ditt labb till databasen.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet innehåller steg för att lägga till en artefakt lagrings plats i ett labb i Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **fler tjänster**och välj sedan **DevTest Labs** i listan över tjänster.
3. I listan med labb väljer du ditt labb.
4. Välj **konfiguration och principer** på den vänstra menyn.
5. Avsnittet Välj **databaser** under **externa resurser** på den vänstra menyn.
6. Välj **+ Lägg till** i verktygsfältet.

    ![Knappen Lägg till lagrings plats](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Ange följande information på sidan **databaser** :
   1. **Namn**. Ange ett namn för lagrings platsen.
   2. **Git Clone-URL**. Ange den git HTTPS-klon-URL som du kopierade tidigare från antingen GitHub eller Azure DevOps Services.
   3. **Gren**. Ange grenen för att hämta dina definitioner.
   4. **Personlig åtkomsttoken**. Ange den personliga åtkomsttoken som du tidigare fick från antingen GitHub eller Azure DevOps Services.
   5. **Mappsökvägar.** Ange minst en mappsökväg i förhållande till klon-URL: en som innehåller dina definitioner för artefakt-eller Resource Manager-mallar. När du anger en under katalog kontrollerar du att du inkluderar snedstrecket i mappsökvägen.

        ![Områden i databaser](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager mall
Azure Resource Management-mallar (Azure Resource Manager) är JSON-filer som beskriver de resurser i Azure som du vill skapa. Mer information om de här mallarna finns i [redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

Det här avsnittet innehåller steg för att lägga till en artefakt lagrings plats i ett labb med hjälp av en Azure Resource Manager mall.  Mallen skapar labbet om det inte redan finns.

### <a name="template"></a>Mall
Exempel mal len som används i den här artikeln samlar in följande information via parametrar. De flesta av parametrarna har smarta standardvärden, men det finns några värden som måste anges. Du måste ange Labbets namn, URI för artefakt lagrings platsen och säkerhetstoken för lagrings platsen.

- Labb namn.
- Visnings namn för artefakt lagrings platsen i användar gränssnittet för DevTest Labs (UI). Standardvärdet är: `Team Repository` .
- URI till databasen (exempel: `https://github.com/<myteam>/<nameofrepo>.git` eller `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Gren i lagrings platsen som innehåller artefakter. Standardvärdet är: `master` .
- Namnet på den mapp som innehåller artefakter. Standardvärdet är: `/Artifacts` .
- Typ av lagrings plats. Tillåtna värden är `VsoGit` eller `GitHub` .
- Åtkomsttoken för lagrings platsen.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Distribuera mallen
Det finns några sätt att distribuera mallen till Azure och låta resursen skapas, om den inte finns eller uppdateras, om den finns. Mer information finns i följande artiklar:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Distribuera resurser med Resource Manager-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

Nu ska vi gå vidare och se hur du distribuerar mallen i PowerShell. Cmdletar som används för att distribuera mallen är Sammanhangs beroende, så aktuell klient och aktuell prenumeration används. Använd [set-AzContext](/powershell/module/az.accounts/set-azcontext) innan du distribuerar mallen, om det behövs, för att ändra kontexten.

Börja med att skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Om den resurs grupp som du vill använda redan finns hoppar du över det här steget.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Skapa sedan en distribution till resurs gruppen med hjälp av [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Den här cmdleten tillämpar resurs ändringarna på Azure. Flera resurs distributioner kan göras till en specifik resurs grupp. Om du distribuerar flera gånger till samma resurs grupp kontrollerar du att namnet på varje distribution är unikt.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

När New-AzResourceGroupDeployment-körningen är klar, skriver kommandot ut viktig information, till exempel etablerings status (bör utföras) och eventuella utdata för mallen.

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Det här avsnittet innehåller ett exempel på ett PowerShell-skript som kan användas för att lägga till en artefakt lagrings plats i ett labb. Om du inte har Azure PowerShell kan du läsa mer i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/?view=azps-1.2.0) detaljerade anvisningar för att installera det.

### <a name="full-script"></a>Fullständigt skript
Här är det fullständiga skriptet, inklusive några utförliga meddelanden och kommentarer:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet
I följande exempel visas hur du kör skriptet:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametrar
Exemplet på PowerShell-skriptet i den här artikeln tar följande parametrar:

| Parameter | Beskrivning |
| --------- | ----------- |
| LabName | Labbets namn. |
| ArtifactRepositoryName | Namn på den nya artefakt lagrings platsen. Skriptet skapar ett slumpmässigt namn för lagrings platsen om det inte har angetts. |
| ArtifactRepositoryDisplayName | Visnings namn för artefakt lagrings platsen. Detta är det namn som visas i Azure Portal ( https://portal.azure.com) när du visar alla artefakt databaser för ett labb. |
| RepositoryUri | URI till lagrings platsen. Exempel: `https://github.com/<myteam>/<nameofrepo>.git` eller `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Gren där artefakt filen kan hittas. Standardvärdet är Master. |
| FolderPath | Mapp som artefakter kan hittas under. Standardvärdet är "/Artifacts" |
| PersonalAccessToken | Säkerhetstoken för åtkomst till GitHub-eller VSOGit-lagringsplatsen. Instruktioner för att hämta personlig åtkomsttoken finns i avsnittet krav. |
| SourceType | Om artefakt är VSOGit-eller GitHub-lagringsplats. |

Själva lagrings platsen behöver ett internt namn för identifiering, vilket skiljer sig från det visnings namn som visas i Azure Portal. Du ser inte det interna namnet med hjälp av Azure Portal, men du ser det när du använder Azure REST-API: er eller Azure PowerShell. Skriptet ger ett namn, om det inte har angetts av användaren av vårt skript.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>PowerShell-kommandon som används i skriptet

| PowerShell-kommando | Anteckningar |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Det här kommandot används för att hämta information om labbet, till exempel dess plats. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Det finns inget speciellt kommando för att lägga till artefakt databaser. Den allmänna [New-AzResource-](/powershell/module/az.resources/new-azresource) cmdleten utför jobbet. Denna cmdlet kräver antingen **ResourceID** -eller **resourceName** -och **resourcetype** -paret för att veta vilken typ av resurs som ska skapas. Det här exempel skriptet använder paret resurs namn och resurs typ. <br/><br/>Observera att du skapar artefakt lager källan på samma plats och under samma resurs grupp som labbet.|

Skriptet lägger till en ny resurs i den aktuella prenumerationen. Använd [Get-AzContext](/powershell/module/az.accounts/get-azcontext) för att visa den här informationen. Använd [set-AzContext](/powershell/module/az.accounts/set-azcontext) för att ställa in den aktuella klienten och prenumerationen.

Det bästa sättet att identifiera resurs namn och resurs typs information är att använda [test enheten Azure REST API: er](https://azure.github.io/projects/apis/) . Kolla in [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) -providern för att se tillgängliga REST-API: er för DevTest Labs-providern. Skriptet användare följande resurs-ID.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Resurs typen är allt som anges efter "providers" i URI: n, förutom objekt som listas inom klammerparenteserna. Resurs namnet är allt som visas inom klammerparenteserna. Om fler än ett objekt förväntas för resurs namnet avgränsar du varje objekt med ett snedstreck som vi har gjort.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Nästa steg
- [Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter för den virtuella DevTest Labs-datorn](devtest-lab-artifact-author.md)
- [Diagnostisera artefakt fel i labbet](devtest-lab-troubleshoot-artifact-failure.md)
