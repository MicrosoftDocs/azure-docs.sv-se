---
title: Lägga till en artefakt till en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en artefakt till en virtuell dator i ett labb i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483898"
---
# <a name="add-an-artifact-to-a-vm"></a>Lägg till en artefakt till en virtuell dator
När du skapar en virtuell dator kan du lägga till befintliga artefakter till den. Dessa artefakter kan vara antingen från den [offentliga DevTest Labs git-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) eller från din egen git-lagringsplats. Den här artikeln visar hur du lägger till artefakter i Azure Portal och genom att använda Azure PowerShell. 

Med Azure DevTest Labs *artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn är etablerad, till exempel köra Windows PowerShell-skript, köra bash-kommandon och installera program vara. Med artefakt *parametrar* kan du anpassa artefakten för ditt specifika scenario.

Information om hur du skapar anpassade artefakter finns i artikeln: [skapa anpassade artefakter](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Använda Azure-portalen 
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du det labb som innehåller den virtuella dator som du vill arbeta med.  
1. Välj **mina virtuella datorer**.
1. Välj önskad virtuell dator.
1. Välj **Hantera artefakter**. 
1. Välj **tillämpa artefakter**.
1. I fönstret **Använd artefakter** väljer du den artefakt som du vill lägga till i den virtuella datorn.
1. I fönstret **Lägg till artefakt** anger du de parameter värden som krävs och eventuella valfria parametrar som du behöver.  
1. Välj **Lägg till** för att lägga till artefakten och gå tillbaka till fönstret **Använd artefakter** .
1. Fortsätt att lägga till artefakter efter behov för den virtuella datorn.
1. När du har lagt till dina artefakter kan du [ändra i vilken ordning artefakterna ska köras](#change-the-order-in-which-artifacts-are-run). Du kan också gå tillbaka om du vill [Visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar med att lägga till artefakter väljer du **tillämpa**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra i vilken ordning artefakter körs
Som standard körs aktiviteterna i artefakterna i den ordning som de läggs till i den virtuella datorn. Följande steg illustrerar hur du ändrar i vilken ordning artefakterna körs.

1. Längst upp i fönstret **Använd artefakter** väljer du den länk som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som har lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. I fönstret **valda artefakter** drar du och släpper artefakterna i önskad ordning. Om du har problem med att dra artefakten ser du till att du drar från den vänstra sidan av artefakten. 
1. Välj **OK** när du är klar.  

### <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg visar hur du visar eller ändrar parametrarna för en artefakt:

1. Längst upp i fönstret **Använd artefakter** väljer du den länk som anger antalet artefakter som har lagts till den virtuella datorn.
   
    ![Antal artefakter som har lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. I fönstret **valda artefakter** väljer du den artefakt som du vill visa eller redigera.  
1. I fönstret **Lägg till artefakt** gör du nödvändiga ändringar och väljer **OK** för att stänga fönstret **Lägg till artefakt** .
1. Klicka på **OK** för att stänga fönstret **valda artefakter** .

## <a name="use-powershell"></a>Använd PowerShell
Följande skript använder den angivna artefakten på den angivna virtuella datorn. Kommandot [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) är det som utför åtgärden.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om artefakter:

- [Ange obligatoriska artefakter för ditt labb](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter](devtest-lab-artifact-author.md)
- [Lägga till ett artefaktcentrallager till ett labb](devtest-lab-artifact-author.md)
- [Diagnostisera fel i artefakter](devtest-lab-troubleshoot-artifact-failure.md)
