---
title: Hantera runbooks i Azure Automation
description: Den här artikeln beskriver hur du hanterar runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830096"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera runbooks i Azure Automation

Du kan lägga till en runbook Azure Automation genom att antingen skapa en ny eller importera en befintlig från en fil eller [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om hur du hanterar en runbook som importerats från en fil. Du hittar all information om åtkomst till community-runbooks och moduler i [Runbook- och modulgallerier för Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Skapa en runbook

Skapa en ny runbook i Azure Automation med hjälp av Azure Portal eller Windows PowerShell. När runbooken har skapats kan du redigera den med hjälp av informationen i:

* [Redigera text-runbook i Azure Automation](automation-edit-textual-runbook.md)
* [Lär dig Windows PowerShell arbetsflödesbegrepp för Automation-runbooks](automation-powershell-workflow.md)
* [Hantera Python 2-paket i Azure Automation](python-packages.md)
* [Hantera Python 3-paket (förhandsversion) i Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en runbook i Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben väljer du **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.
3. Klicka **på Skapa en runbook**.
4. Ange ett namn för runbooken och välj dess [typ](automation-runbook-types.md). Runbook-namnet måste börja med en bokstav och får innehålla bokstäver, siffror, understreck och bindestreck.
5. Klicka **på Skapa** för att skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en runbook med PowerShell

Använd [cmdleten New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) för att skapa en tom runbook. Använd `Type` parametern för att ange en av de runbook-typer som definierats för `New-AzAutomationRunbook` .

I följande exempel visas hur du skapar en ny tom runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importera en runbook

Du kan importera ett PowerShell- eller PowerShell Workflow-skript **(.ps1),** en grafisk runbook (**.graphrunbook**) eller ett Python 2- eller Python 3-skript (**.py**) för att skapa en egen runbook. Du måste ange vilken [typ av runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* Du kan importera en **.ps1-fil** som inte innehåller något arbetsflöde till antingen en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-runbook.](automation-runbook-types.md#powershell-workflow-runbooks) Om du importerar den till en PowerShell Workflow-runbook konverteras den till ett arbetsflöde. I det här fallet ingår kommentarer i runbooken för att beskriva de ändringar som gjorts.

* Du kan bara importera en **.ps1-fil** som innehåller ett PowerShell-arbetsflöde till en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Om filen innehåller flera PowerShell-arbetsflöden misslyckas importen. Du måste spara varje arbetsflöde i en egen fil och importera dem separat.

* Importera inte en **.ps1-fil** som innehåller ett PowerShell-arbetsflöde till en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks)eftersom PowerShell-skriptmotorn inte kan identifiera den.

* Importera endast en **.graphrunbook-fil** till en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importera en runbook från Azure Portal

Du kan använda följande procedur för att importera en skriptfil till Azure Automation.

> [!NOTE]
> Du kan bara importera en **.ps1-fil** till en PowerShell Workflow-runbook med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.
3. Klicka **på Importera en runbook**.
4. Klicka **på Runbook-fil** och välj den fil som ska importeras.
5. Om fältet **Namn** är aktiverat kan du ändra runbook-namnet. Namnet måste börja med en bokstav och får innehålla bokstäver, siffror, understreck och bindestreck.
6. [Runbook-typen](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen när du har tagit hänsyn till tillämpliga begränsningar.
7. Klicka på **Skapa**. Den nya runbooken visas i listan över runbooks för Automation-kontot.
8. Du måste [publicera runbooken](#publish-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk runbook kan du konvertera den till en annan typ. Du kan dock inte konvertera en grafisk runbook till en textbaserad runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Importera en runbook med Windows PowerShell

Använd [cmdleten Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) för att importera en skriptfil som ett utkast till en runbook. Om runbooken redan finns misslyckas importen om du inte använder `Force` parametern med cmdleten .

I följande exempel visas hur du importerar en skriptfil till en runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Hantera resurser

Om din runbook skapar [en](automation-runbook-execution.md#resources)resurs bör skriptet kontrollera om resursen redan finns innan du försöker skapa den. Här är ett grundläggande exempel.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Hämta information från aktivitetsloggen

Du kan hämta runbook-information, till exempel den person eller det konto som startade en runbook, från [aktivitetsloggen](automation-runbook-execution.md#activity-logging) för Automation-kontot. I följande PowerShell-exempel visas den senaste användaren som kör den angivna runbooken.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Spåra förlopp

Det är en bra idé att skapa dina runbooks så att de är modulära till sin natur, med logik som enkelt kan återanvändas och startas om. Genom att spåra förloppet i en runbook ser du till att runbook-logiken körs korrekt om det finns problem.

Du kan spåra förloppet för en Runbook med hjälp av en extern källa, till exempel ett lagringskonto, en databas eller delade filer. Skapa logik i din runbook för att först kontrollera tillståndet för den senaste vidtagna åtgärden. Baserat på resultatet av kontrollen kan logiken antingen hoppa över eller fortsätta med specifika uppgifter i runbooken.

## <a name="prevent-concurrent-jobs"></a>Förhindra samtidiga jobb

Vissa runbooks beter sig konstigt om de körs över flera jobb samtidigt. I det här fallet är det viktigt att en runbook implementerar logik för att avgöra om det redan finns ett jobb som körs. Här är ett grundläggande exempel.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Hantera tillfälliga fel i ett tidsberoende skript

Dina runbooks måste vara robusta och kunna hantera [fel,](automation-runbook-execution.md#errors)inklusive tillfälliga fel som kan göra att de startas om eller misslyckas. Om en runbook misslyckas Azure Automation försöker den på nytt.

Om din runbook normalt körs inom en tidsbegränsning måste skriptet implementera logik för att kontrollera körningstiden. Den här kontrollen säkerställer att åtgärder som start, avstängning eller utskalning endast körs under specifika tider.

> [!NOTE]
> Den lokala tiden i Azure Sandbox-processen är inställd på UTC. Beräkningar för datum och tid i dina runbooks måste ta hänsyn till detta faktum.

## <a name="work-with-multiple-subscriptions"></a>Arbeta med flera prenumerationer

Din runbook måste kunna arbeta med [prenumerationer](automation-runbook-execution.md#subscriptions). För att till exempel hantera flera prenumerationer använder runbooken cmdleten [Disable-AzContextAutosave.](/powershell/module/Az.Accounts/Disable-AzContextAutosave) Denna cmdlet säkerställer att autentiseringskontexten inte hämtas från en annan runbook som körs i samma sandbox-miljö. Runbooken använder också `Get-AzContext` cmdleten för att hämta kontexten för den aktuella sessionen och tilldela den till variabeln `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Arbeta med ett anpassat skript

> [!NOTE]
> Du kan normalt inte köra anpassade skript och runbooks på värden med en Log Analytics-agent installerad.

Så här använder du ett anpassat skript:

1. Skapa ett Automation-konto och skaffa en [deltagarroll.](automation-role-based-access-control.md)
2. [Länka kontot till Azure-arbetsytan](../security-center/security-center-enable-data-collection.md).
3. Aktivera [Hybrid Runbook Worker,](automation-hybrid-runbook-worker.md) [Uppdateringshantering](./update-management/overview.md)eller någon annan Automation-funktion. 
4. Om du använder en Linux-dator behöver du hög behörighet. Logga in för [att stänga av signaturkontroller](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testa en Runbook

När du testar en runbook körs [utkastversionen](#publish-a-runbook) och alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men [utdata- och](automation-runbook-output-and-messages.md#use-the-output-stream) [varnings- och](automation-runbook-output-and-messages.md#working-with-message-streams) felströmmarna visas i fönstret Testa utdata. Meddelanden till den [utförliga dataströmmen](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) visas bara i fönstret Utdata om [variabeln VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) har angetts till `Continue` .

Även om utkastversionen körs körs runbooken fortfarande normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa runbooks på icke-produktionsresurser.

Proceduren för att testa varje [typ av runbook](automation-runbook-types.md) är densamma. Det finns ingen skillnad i testning mellan textredigeraren och den grafiska redigeraren i Azure Portal.

1. Öppna utkastversionen av runbooken i [textredigeraren eller](automation-edit-textual-runbook.md) i den [grafiska redigeraren](automation-graphical-authoring-intro.md).
1. Klicka **på Test** för att öppna sidan Test.
1. Om runbooken har parametrar visas de i den vänstra rutan, där du kan ange värden som ska användas för testet.
1. Om du vill köra testet på [en Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)ändrar du **Körningsinställningar** **till Hybrid Worker** och väljer namnet på målgruppen.  Annars behåller du **Standard-Azure** för att köra testet i molnet.
1. Klicka **på Starta** för att starta testet.
1. Du kan använda knapparna under fönstret Utdata för att stoppa eller pausa [ett PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller en grafisk [runbook](automation-runbook-types.md#graphical-runbooks) medan den testas. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Granska utdata från runbooken i fönstret Utdata.

## <a name="publish-a-runbook"></a>Publicera en runbook

När du skapar eller importerar en ny runbook måste du publicera den innan du kan köra den. Varje runbook i Azure Automation har en utkastversion och en publicerad version. Det är bara den Publicerade versionen som kan köras och bara Utkastet som kan redigeras. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När utkastversionen ska göras tillgänglig publicerar du den och skriver över den aktuella publicerade versionen med utkastversionen.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicera en runbook i Azure Portal

1. Öppna runbooken i Azure Portal.
2. Klicka på **Redigera**.
3. Klicka **på** Publicera och **sedan på** Ja som svar på verifieringsmeddelandet.

### <a name="publish-a-runbook-using-powershell"></a>Publicera en runbook med PowerShell

Använd [cmdleten Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) för att publicera din runbook. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Schemalägg en runbook i Azure Portal

När din runbook har publicerats kan du schemalägga den för drift:

1. Öppna runbooken i Azure Portal.
2. Välj **Scheman** under **Resurser**.
3. Välj **Lägg till ett schema.**
4. I fönstret Schemalägg runbook väljer du **Länka ett schema till din runbook**.
5. Välj **Skapa ett nytt schema** i fönstret Schema.
6. Ange ett namn, en beskrivning och andra parametrar i fönstret Nytt schema.
7. När schemat har skapats markerar du det och klickar på **OK.** Den bör nu vara länkad till din runbook.
8. Leta efter ett e-postmeddelande i postlådan för att meddela dig om runbook-statusen.

## <a name="obtain-job-statuses"></a>Hämta jobbstatusar

### <a name="view-statuses-in-the-azure-portal"></a>Visa statusar i Azure Portal

Information om jobbhantering i Azure Automation finns i [Jobb](automation-runbook-execution.md#jobs). När du är redo att se dina Runbook-jobb använder du Azure Portal åtkomst till ditt Automation-konto. Till höger kan du se en sammanfattning av alla Runbook-jobb i **Jobbstatistik**.

![Panelen Jobbstatistik](./media/manage-runbooks/automation-account-job-status-summary.png)

Sammanfattningen visar antalet och en grafisk representation av jobbstatusen för varje jobb som körs.

När du klickar på panelen visas sidan Jobb, som innehåller en sammanfattad lista över alla jobb som körs. Den här sidan visar status, runbooknamn, starttid och slutförandetid för varje jobb.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Skärmbild av sidan Jobb.":::

Du kan filtrera listan över jobb genom att välja **Filtrera jobb**. Filtrera på en specifik runbook, jobbstatus eller ett val i listrutan och ange ett intervall för sökningen.

![Filtrera jobbstatus](./media/manage-runbooks/automation-account-jobs-filter.png)

Du kan också visa jobbsammanfattningsinformation för en specifik runbook genom att välja denna runbook på sidan Runbooks i ditt Automation-konto och sedan **välja Jobb.** Den här åtgärden visar sidan Jobb. Härifrån kan du klicka på en jobbpost för att visa information och utdata.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Skärmbild av sidan Jobb med knappen Fel markerad.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Hämta jobbstatusar med PowerShell

Använd [cmdleten Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) för att hämta jobben som skapats för en runbook och information om ett visst jobb. Om du startar en runbook med `Start-AzAutomationRunbook` , returneras det resulterande jobbet. Använd [Get-AzAutomationJobOutput för att](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) hämta jobbutdata.

I följande exempel hämtar det sista jobbet för en exempel-runbook och visar dess status, de värden som anges för runbook-parametrarna och jobbutdata.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

I följande exempel hämtas utdata för ett specifikt jobb och returnerar varje post. Om det finns ett [undantag](automation-runbook-execution.md#exceptions) för en av posterna skriver skriptet undantaget i stället för värdet . Det här beteendet är användbart eftersom undantag kan ge ytterligare information som kanske inte loggas normalt under utdata.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om runbookhantering finns i [Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Information om hur du förbereder en PowerShell-runbook [finns i Redigera textbaserade runbooks i Azure Automation](automation-edit-textual-runbook.md).
* Information om hur du felsöker problem med runbook-körning finns [i Felsöka runbook-problem.](troubleshoot/runbooks.md)
