---
title: Hantera förskript och efterskript i din Uppdateringshantering i Azure
description: Den här artikeln beskriver hur du konfigurerar och hanterar förskript och efterskript för uppdateringsdistributioner.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51067095b7ebb33da61908b1424752b481668f5f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830816"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Hantera förskript och efterskript

Förskript och efterskript är runbooks som ska köras i ditt Azure Automation konto före (före uppgift) och efter (efter uppgift) en uppdateringsdistribution. Förskript och efterskript körs i Azure-kontexten, inte lokalt. Förskript körs i början av uppdateringsdistributionen. Efterskript körs i slutet av distributionen och efter eventuella omstarter som har konfigurerats.

## <a name="pre-script-and-post-script-requirements"></a>Krav för förskript och efterskript

För att en runbook ska kunna användas som ett förskript eller efterskript måste du importera den till ditt Automation-konto och [publicera runbooken](../manage-runbooks.md#publish-a-runbook).

För närvarande stöds endast PowerShell- och Python 2-runbooks som för-/efterskript. Andra runbook-typer som Python 3, Grafiskt, PowerShell-arbetsflöde, Grafiskt PowerShell-arbetsflöde stöds för närvarande inte som för-/efterskript.

## <a name="pre-script-and-post-script-parameters"></a>Parametrar för förskript och efterskript

När du konfigurerar förskript och efterskript kan du skicka in parametrar precis som att schemalägga en runbook. Parametrar definieras när distributionen av uppdateringen skapas. Förskript och efterskript stöder följande typer:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [enkel]
* [double]
* [DateTime]
* [sträng]

Runbook-parametrar för förskript och efterskript stöder inte booleska, objekt- eller matristyper. Dessa värden gör att runbooks misslyckas. 

Om du behöver en annan objekttyp kan du typge den till en annan typ med din egen logik i runbooken.

Förutom dina standard-runbook-parametrar tillhandahålls `SoftwareUpdateConfigurationRunContext` parametern (typen JSON-sträng). Om du definierar parametern i din förskripts- eller efterskripts-runbook skickas den automatiskt av uppdateringsdistributionen. Parametern innehåller information om uppdateringsdistributionen, som är en delmängd av informationen som returneras av [SoftwareUpdateconfigurations-API:et](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Avsnitten nedan definierar de associerade egenskaperna.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Egenskaper för SoftwareUpdateConfigurationRunContext

|Egenskap  |Beskrivning  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Namnet på programuppdateringskonfigurationen.        |
|SoftwareUpdateConfigurationRunId     | Det unika ID:t för körningen.        |
|SoftwareUpdateConfigurationSettings     | En samling egenskaper som är relaterade till programuppdateringskonfigurationen.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Operativsystem som är mål för uppdateringsdistributionen.         |
|SoftwareUpdateConfigurationSettings.duration     | Den maximala varaktigheten för uppdateringsdistributionen `PT[n]H[n]M[n]S` körs enligt ISO8601, även kallat underhållsfönstret.          |
|SoftwareUpdateConfigurationSettings.Windows     | En samling egenskaper som är relaterade till Windows-datorer.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | En lista över KB som är undantagna från uppdateringsdistributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Uppdateringsklassificering har valts för uppdateringsdistributionen.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Starta om inställningarna för uppdateringsdistributionen.        |
|azureVirtualMachines     | En lista över resourceIds för de virtuella Azure-datorerna i uppdateringsdistributionen.        |
|nonAzureComputerNames|En lista över FQDN för icke-Azure-datorer i uppdateringsdistributionen.|

Följande exempel är en JSON-sträng som skickas till parametern **SoftwareUpdateConfigurationRunContext:**

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "PT2H0M",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Ett fullständigt exempel med alla egenskaper finns i: Hämta [konfiguration av programuppdatering efter namn.](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Objektet `SoftwareUpdateConfigurationRunContext` kan innehålla dubblettposter för datorer. Detta kan göra att förskript och efterskript körs flera gånger på samma dator. Du kan komma runt det här beteendet genom `Sort-Object -Unique` att använda för att välja endast unika VM-namn.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Använda ett förskript eller efterskript i en distribution

Om du vill använda ett förskript eller efterskript i en uppdateringsdistribution börjar du med att skapa en uppdateringsdistribution. Välj **Förskript + Efterskript.** Den här åtgärden **öppnar sidan Välj förskript + efterskript.**

![Välj skript](./media/pre-post-scripts/select-scripts.png)

Välj det skript som du vill använda. I det här exemplet använder vi **runbooken UpdateManagement-TurnOnVms.** När du väljer runbooken öppnas **sidan Konfigurera** skript. Välj **Förskript** och sedan **OK.**

Upprepa den här processen för **skriptet UpdateManagement-TurnOffVms.** Men när du väljer **Skripttyp** väljer du **Post-Script (Efterskript).**

I **avsnittet Valda** objekt visas nu båda dina valda skript. Det ena är ett förskript och det andra är ett efterskript:

![Valda objekt](./media/pre-post-scripts/selected-items.png)

Slutför konfigurationen av uppdateringsdistributionen.

När uppdateringsdistributionen är klar kan du gå till **Uppdateringsdistributioner** för att visa resultatet. Som du ser anges status för förskriptet och efterskriptet:

![Uppdatera resultat](./media/pre-post-scripts/update-results.png)

När du väljer körningen av uppdateringsdistributionen visas ytterligare information om förskript och efterskript. En länk till skriptkällan vid tidpunkten för körningen tillhandahålls.

![Distributionskörningsresultat](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Stoppa en distribution

Om du vill stoppa en distribution baserat på ett förskript måste du [skapa](../automation-runbook-execution.md#throw) ett undantag. Om du inte gör det körs fortfarande distributionen och efterskriptet. Följande kodfragment visar hur du utlöste ett undantag med hjälp av PowerShell.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

I Python 2 hanteras undantagshantering i ett [try-block.](https://www.python-course.eu/exception_handling.php)

## <a name="interact-with-machines"></a>Interagera med datorer

Förskript och efterskript körs som runbooks i ditt Automation-konto och inte direkt på datorerna i distributionen. För- och efteruppgifter körs också i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. Följande avsnitt visar hur du kan interagera med datorerna direkt, oavsett om de är virtuella Azure-datorer eller icke-Azure-datorer.

### <a name="interact-with-azure-machines"></a>Interagera med Azure-datorer

För- och efteruppgifter körs som runbooks och körs inte inbyggt på dina virtuella Azure-datorer i distributionen. Om du vill interagera med dina virtuella Azure-datorer måste du ha följande:

* Ett Kör som-konto
* En runbook som du vill köra

Om du vill interagera med Azure-datorer bör du använda cmdleten [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) för att interagera med dina virtuella Azure-datorer. Ett exempel på hur du gör detta finns i runbook-exemplet [Uppdateringshantering – kör skript med kommandot Kör](https://github.com/azureautomation/update-management-run-script-with-run-command).

### <a name="interact-with-non-azure-machines"></a>Interagera med datorer som inte är Azure-datorer

För- och efteruppgifter körs i Azure-kontexten och har inte åtkomst till datorer som inte är Azure-datorer. Om du vill interagera med datorer som inte är Azure-datorer måste du ha följande:

* Ett Kör som-konto
* Hybrid Runbook Worker installerat på datorn
* En runbook som du vill köra lokalt
* En överordnad runbook

För att interagera med icke-Azure-datorer körs en överordnad runbook i Azure-kontexten. Den här runbooken anropar en underordnad runbook med [cmdleten Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Du måste ange `RunOn` parametern och ange namnet på Hybrid Runbook Worker som skriptet ska köras på. Se runbook-exemplet [Uppdateringshantering – kör skriptet lokalt](https://github.com/azureautomation/update-management-run-script-locally).

## <a name="abort-patch-deployment"></a>Avbryta korrigeringsdistribution

Om ditt förskript returnerar ett fel kanske du vill avbryta distributionen. Om du vill göra det måste [du skapa](/powershell/module/microsoft.powershell.core/about/about_throw) ett fel i skriptet för all logik som skulle utgöra ett fel.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Om du vill skapa ett fel när ett visst villkor inträffar i Python 2 använder du en [raise-instruktion.](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement)

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Exempel

Exempel för förskript och efterskript finns i [Azure Automation GitHub-organisationen](https://github.com/azureautomation) och [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), eller så kan du importera dem via Azure Portal. Det gör du genom att välja Runbooks Gallery under **Processautomatisering** **i ditt Automation-konto.** Använd **Uppdateringshantering** för filtret.

![Gallerilista](./media/pre-post-scripts/runbook-gallery.png)

Eller så kan du söka efter dem efter deras skriptnamn, som du ser i följande lista:

* Uppdateringshantering – Aktivera virtuella datorer
* Uppdateringshantering – Inaktivera virtuella datorer
* Uppdateringshantering – Köra skript lokalt
* Uppdateringshantering – Mall för för-/efterskript
* Uppdateringshantering – Köra skript med Kör kommando

> [!IMPORTANT]
> När du har importerat runbooks måste du publicera dem innan de kan användas. Det gör du genom att leta upp runbooken i ditt Automation-konto, **välja Redigera** och sedan **välja Publicera.**

Exemplen baseras på den grundläggande mall som definieras i följande exempel. Den här mallen kan användas för att skapa en egen runbook som ska användas med förskript och efterskript. Nödvändig logik för att autentisera med Azure och hantera `SoftwareUpdateConfigurationRunContext` parametern ingår.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> För icke-grafiska PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Du kan använda dessa cmdlets eller uppdatera [dina moduler i ditt](../automation-update-azure-modules.md) Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

## <a name="next-steps"></a>Nästa steg

Mer information om uppdateringshantering finns i [Hantera uppdateringar och korrigeringar för dina virtuella datorer.](manage-updates-for-vm.md)
