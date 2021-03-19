---
title: Starta datorer med Automation-runbooks i Azure DevTest Labs
description: Lär dig hur du startar virtuella datorer i ett labb i Azure DevTest Labs genom att använda Azure Automation runbooks.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282270"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Starta virtuella datorer i ett labb i ordning genom att använda Azure Automation runbooks
Med funktionen [Autostart](devtest-lab-set-lab-policy.md#set-autostart) i DevTest Labs kan du konfigurera virtuella datorer så att de startar automatiskt vid en viss tidpunkt. Den här funktionen stöder dock inte att datorer startar i en speciell ordning. Det finns flera scenarier där den här typen av automatisering skulle vara användbar.  Ett scenario är var du måste starta en virtuell dator i ett labb innan du börjar med de andra virtuella datorerna, eftersom hoppet används som åtkomst punkt till de andra virtuella datorerna.  Den här artikeln visar hur du konfigurerar ett Azure Automation-konto med en PowerShell-Runbook som kör ett skript. Skriptet använder taggar på virtuella datorer i labbet för att kontrol lera start ordningen utan att behöva ändra skriptet.

## <a name="setup"></a>Installation
I det här exemplet måste de virtuella datorerna i labbet ha taggen **StartupOrder** lagts till med lämpligt värde (0, 1, 2 osv.). Utse en dator som inte behöver startas som-1.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Skapa ett Azure Automation-konto genom att följa anvisningarna i [den här artikeln](../automation/automation-create-standalone-account.md). Välj alternativet **Kör som-konton** när du skapar kontot. När Automation-kontot har skapats öppnar du sidan **moduler** och väljer **Uppdatera Azure-moduler** på Meny raden. Standardmodulerna är flera äldre versioner och utan uppdateringen kanske inte skriptet fungerar.

## <a name="add-a-runbook"></a>Lägg till en Runbook
Om du nu vill lägga till en Runbook i Automation-kontot väljer du **Runbooks** på den vänstra menyn. Välj **Lägg till en Runbook** på menyn och följ instruktionerna för att [skapa en PowerShell-Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-skript
Följande skript tar prenumerations namnet, labb namnet som parametrar. Skriptets flöde är att hämta alla de virtuella datorerna i labbet och sedan analysera taggs informationen för att skapa en lista över de virtuella dator namnen och deras start ordning. Skriptet vägleder dig genom de virtuella datorerna i ordning och startar de virtuella datorerna. Om det finns flera virtuella datorer i ett särskilt ordnings nummer startas de asynkront med hjälp av PowerShell-jobb. För de virtuella datorer som inte har en-tagg anger du att startvärdet är det sista (10). de kommer att startas sist som standard.  Om labbet inte vill att den virtuella datorn ska startas automatiskt anger du värdet 11 och ignoreras.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Skapa ett schema
Om du vill att det här skriptet ska köras dagligen [skapar du ett schema](../automation/shared-resources/schedules.md#create-a-schedule) i Automation-kontot. När schemat har skapats [länkar du det till Runbook-flödet](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

I en storskalig situation där det finns flera prenumerationer på flera labb, lagrar du parameter informationen i en fil för olika labb och skickar filen till skriptet i stället för de enskilda parametrarna. Skriptet skulle behöva ändras, men kärn körningen är densamma. Exemplet använder Azure Automation för att köra PowerShell-skriptet, men det finns andra alternativ som att använda en uppgift i en pipeline för build/release.

## <a name="next-steps"></a>Nästa steg
I följande artikel finns mer information om Azure Automation: [en introduktion till Azure Automation](../automation/automation-intro.md).
