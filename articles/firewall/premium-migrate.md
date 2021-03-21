---
title: Migrera till Azure Firewall Premium Preview
description: Lär dig hur du migrerar från Azure Firewall standard till Azure Firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549854"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrera till Azure Firewall Premium Preview

Du kan migrera Azure Firewall standard till Azure Firewall Premium Preview för att dra nytta av de nya Premium funktionerna. Mer information om för hands versions funktionerna i Azure Firewall Premium finns i för [hands versionen av Azure Firewall Premium](premium-features.md).

I följande två exempel visas hur du:
- Migrera en befintlig standard princip med Azure PowerShell
- Migrera en befintlig standard brand vägg (med klassiska regler) till Azure Firewall Premium med en Premium-princip.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrera en befintlig princip med Azure PowerShell

`Transform-Policy.ps1` är ett Azure PowerShell-skript som skapar en ny Premium-princip från en befintlig standard princip.

Med en standard-ID för brand Väggs princip omvandlar skriptet det till en Premium Azure Firewall-princip. Skriptet ansluter först till ditt Azure-konto, hämtar principen, transformerar/lägger till olika parametrar och laddar sedan upp en ny Premium-princip. Den nya Premium-principen heter `<previous_policy_name>_premium` .

Exempel på användning:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Skriptet migrerar inte hot informations inställningar. Du måste Anteckna inställningarna innan du fortsätter och migrerar dem manuellt.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrera en befintlig standard brand vägg med hjälp av Azure Portal

Det här exemplet visar hur du använder Azure Portal för att migrera en standard brand vägg (klassiska regler) till Azure Firewall Premium med en Premium princip.

1. Välj din standard brand vägg från Azure Portal. På sidan **Översikt** väljer du **migrera till brand Väggs princip**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrera till brand Väggs princip":::

1. På sidan **migrera till brand Väggs princip** väljer du **Granska + skapa**.
1. Välj **Skapa**.

   Det tar några minuter att slutföra distributionen.
1. Använd `Transform-Policy.ps1` [Azure PowerShell-skriptet](#migrate-an-existing-policy-using-azure-powershell) för att omvandla den nya standard principen till en Premium princip.
1. På portalen väljer du standard brand Väggs resurs. 
1. Under **Automation** väljer du **Exportera mall**. Behåll den här webb läsar fliken öppen. Du kommer tillbaka till den senare.
   > [!TIP]
   > För att se till att du inte förlorar mallen kan du ladda ned och spara den om din webbläsare-flik stängs eller uppdateras.
1. Öppna en ny webbläsare-flik, navigera till Azure Portal och öppna resurs gruppen som innehåller din brand vägg.
1. Ta bort den befintliga standard brand Väggs instansen.

   Det tar några minuter att slutföra.

1. Gå tillbaka till fliken webbläsare med den exporterade mallen.
1. Välj **distribuera** och välj sedan **Redigera mall** på sidan **Anpassad distribution** .
1. Redigera mal linne texten:
   
   1. Under `Microsoft.Network/azureFirewalls` resursen, `Properties` `sku` ändrar du `tier` från "standard" till "Premium".
   1. Under mallen `Parameters` ändrar `defaultValue` du för `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` från:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      till:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Välj **Spara**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

När distributionen är klar kan du nu konfigurera alla nya funktioner för för hands versionen av Azure Firewall Premium.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Firewall Premium-funktioner](premium-features.md)