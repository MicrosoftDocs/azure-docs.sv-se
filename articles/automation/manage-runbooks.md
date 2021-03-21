---
title: Hantera Runbooks i Azure Automation
description: Den här artikeln beskriver hur du hanterar Runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: af767ab37e8e77195b7d13b24ea78f4fb88485fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122144"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera Runbooks i Azure Automation

Du kan lägga till en Runbook i Azure Automation genom att antingen skapa en ny eller importera en befintlig från en fil eller [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om hur du hanterar en Runbook som importer ATS från en fil. Du hittar all information om hur du kommer åt community-Runbooks och moduler i [Runbook-och modul gallerier för Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Skapa en runbook

Skapa en ny Runbook i Azure Automation med hjälp av Azure Portal eller Windows PowerShell. När runbook har skapats kan du redigera den med hjälp av information i:

* [Redigera text Runbook i Azure Automation](automation-edit-textual-runbook.md)
* [Lär dig viktiga koncept i Windows PowerShell-arbetsflöde för Automation-runbooks](automation-powershell-workflow.md)
* [Hantera python 2-paket i Azure Automation](python-packages.md)
* [Hantera python 3-paket (för hands version) i Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en Runbook i Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben väljer du **Runbooks** under **process automatisering** för att öppna listan över Runbooks.
3. Klicka på **skapa en Runbook**.
4. Ange ett namn för runbooken och välj dess [typ](automation-runbook-types.md). Run Book namnet måste börja med en bokstav och får innehålla bokstäver, siffror, under streck och bindestreck.
5. Klicka på **skapa** för att skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en Runbook med PowerShell

Använd cmdleten [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) för att skapa en tom Runbook. Använd `Type` parametern för att ange en av de definierade Runbook-typerna `New-AzAutomationRunbook` .

I följande exempel visas hur du skapar en ny tom Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importera en Runbook

Du kan importera ett PowerShell-eller PowerShell-arbetsflöde (**. ps1**), en grafisk Runbook (**. graphrunbook**) eller ett python 2-eller python 3-skript (**. py**) för att skapa en egen Runbook. Du måste ange vilken [typ av Runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* Du kan importera en **. ps1** -fil som inte innehåller ett arbets flöde till antingen en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Om du importerar den till en PowerShell Workflow-Runbook konverteras den till ett arbets flöde. I det här fallet ingår kommentarer i runbooken för att beskriva de ändringar som gjorts.

* Du kan bara importera en **. ps1** -fil som innehåller ett PowerShell-arbetsflöde till en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Importen Miss lyckas om filen innehåller flera PowerShell-arbetsflöden. Du måste spara varje arbets flöde till en egen fil och importera varje separat.

* Importera inte en **. ps1** -fil som innehåller ett PowerShell-arbetsflöde till en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks), eftersom PowerShell-skriptfilen inte kan identifiera den.

* Importera bara en **. graphrunbook** -fil till en ny [grafisk Runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importera en Runbook från Azure Portal

Du kan använda följande procedur för att importera en skript fil till Azure Automation.

> [!NOTE]
> Du kan bara importera en **. ps1** -fil till en PowerShell Workflow-Runbook med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .
3. Klicka på **Importera en Runbook**.
4. Klicka på **Runbook-fil** och välj den fil som ska importeras.
5. Om fältet **namn** är aktiverat har du möjlighet att ändra Runbook-namnet. Namnet måste börja med en bokstav och får innehålla bokstäver, siffror, under streck och bindestreck.
6. [Runbook-typen](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen efter att ha vidtagit tillämpliga begränsningar i kontot.
7. Klicka på **Skapa**. Den nya runbooken visas i listan över Runbooks för Automation-kontot.
8. Du måste [publicera runbooken](#publish-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk Runbook kan du konvertera den till en annan typ. Du kan dock inte konvertera en grafisk Runbook till en text-Runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Importera en Runbook med Windows PowerShell

Använd cmdleten [import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) för att importera en skript fil som ett utkast till en Runbook. Om det redan finns en Runbook Miss lyckas importen om du inte använder `Force` parametern med cmdleten.

I följande exempel visas hur du importerar en skript fil till en Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Hantera resurser

Om din Runbook skapar en [resurs](automation-runbook-execution.md#resources), bör skriptet kontrol lera om resursen redan finns innan du försöker skapa den. Här är ett grundläggande exempel.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Hämta information från aktivitets loggen

Du kan hämta information om Runbook, till exempel den person eller det konto som startade en Runbook, från [aktivitets loggen](automation-runbook-execution.md#activity-logging) för Automation-kontot. Följande PowerShell-exempel ger den senaste användaren att köra den angivna runbooken.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Spåra förlopp

Det är en bra idé att redigera dina runbooks så att de är modulära i sin natur, med logik som kan återanvändas och startas om enkelt. Genom att följa förloppet i en Runbook ser du till att Runbook-logiken körs korrekt om det finns problem.

Du kan följa förloppet för en Runbook genom att använda en extern källa, till exempel ett lagrings konto, en databas eller delade filer. Skapa logik i din Runbook för att först kontrol lera status för den senaste åtgärden som har vidtagits. Sedan, baserat på resultaten av kontrollen, kan logiken antingen hoppa över eller fortsätta med vissa uppgifter i runbooken.

## <a name="prevent-concurrent-jobs"></a>Förhindra samtidiga jobb

Vissa Runbooks fungerar konstigt om de körs över flera jobb samtidigt. I det här fallet är det viktigt att en Runbook implementerar logik för att avgöra om det redan finns ett jobb som körs. Här är ett grundläggande exempel.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Hantera tillfälliga fel i ett tids beroende skript

Dina Runbooks måste vara robusta och kunna hantera [fel](automation-runbook-execution.md#errors), inklusive tillfälliga fel som kan orsaka att de startas om eller kraschar. Azure Automation försöker igen om en Runbook Miss lyckas.

Om din Runbook normalt körs inom en tids gräns, ska skriptet implementera logik för att kontrol lera körnings tiden. Den här kontrollen säkerställer att körning av åtgärder som t. ex. Start, avstängning eller skalning bara sker under vissa tidpunkter.

> [!NOTE]
> Den lokala tiden för Azures sand Box process är inställd på UTC. Beräkningar för datum och tid i dina Runbooks måste ta hänsyn till detta.

## <a name="work-with-multiple-subscriptions"></a>Arbeta med flera prenumerationer

Din Runbook måste kunna arbeta med [prenumerationer](automation-runbook-execution.md#subscriptions). Om du till exempel vill hantera flera prenumerationer använder Runbook cmdleten [disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) . Den här cmdleten säkerställer att autentiserings kontexten inte hämtas från en annan Runbook som körs i samma sandbox. Runbooken använder också `Get-AzContext` cmdleten för att hämta kontexten för den aktuella sessionen och tilldelar den till variabeln `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>Arbeta med ett anpassat skript

> [!NOTE]
> Du kan normalt inte köra anpassade skript och Runbooks på värden med en Log Analytics-agent installerad.

Så här använder du ett anpassat skript:

1. Skapa ett Automation-konto och få en [deltagar roll](automation-role-based-access-control.md).
2. [Länka kontot till Azure-arbetsytan](../security-center/security-center-enable-data-collection.md).
3. Aktivera [hybrid Runbook Worker](automation-hybrid-runbook-worker.md), [uppdateringshantering](./update-management/overview.md)eller någon annan automatiserings funktion. 
4. Om du behöver hög behörighet på en Linux-dator. Logga in för att [stänga av signatur kontroller](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testa en Runbook

När du testar en runbook körs [utkast versionen](#publish-a-runbook) och alla åtgärder som den utför slutförs. Ingen jobb historik skapas, men data strömmarna för [utdata](automation-runbook-output-and-messages.md#use-the-output-stream) och [fel](automation-runbook-output-and-messages.md#working-with-message-streams) visas i fönstret Testa utdata. Meddelanden till [utförlig data ström](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) visas endast i fönstret utdata om variabeln [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) har värdet `Continue` .

Även om utkastet körs körs inte runbooken normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa Runbooks på icke-produktions resurser.

Proceduren för att testa varje [typ av Runbook](automation-runbook-types.md) är densamma. Det finns ingen skillnad i testning mellan text redigeraren och den grafiska redigeraren i Azure Portal.

1. Öppna utkast versionen av runbooken i antingen [text redigeraren](automation-edit-textual-runbook.md) eller den [grafiska redigeraren](automation-graphical-authoring-intro.md).
1. Öppna test sidan genom att klicka på **test** .
1. Om runbooken har parametrar visas de i den vänstra rutan där du kan ange värden som ska användas för testet.
1. Om du vill köra testet på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)ändrar du **körnings inställningarna** till **hybrid Worker** och väljer namnet på mål gruppen.  Annars behåller du standard **Azure** att köra testet i molnet.
1. Starta testet genom att klicka på **Starta** .
1. Du kan använda knapparna under fönstret utdata för att stoppa eller pausa ett [PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller en [grafisk](automation-runbook-types.md#graphical-runbooks) Runbook medan den testas. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Granska utdata från runbooken i fönstret utdata.

## <a name="publish-a-runbook"></a>Publicera en Runbook

När du skapar eller importerar en ny Runbook måste du publicera den innan du kan köra den. Varje Runbook i Azure Automation har en utkast version och en publicerad version. Det är bara den Publicerade versionen som kan köras och bara Utkastet som kan redigeras. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När du ska göra utkastet tillgänglig, publicerar du den och skriver över den aktuella publicerade versionen med utkast versionen.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicera en Runbook i Azure Portal

1. Öppna runbooken i Azure Portal.
2. Klicka på **Redigera**.
3. Klicka på **publicera** och sedan **Ja** som svar på verifierings meddelandet.

### <a name="publish-a-runbook-using-powershell"></a>Publicera en Runbook med hjälp av PowerShell

Använd cmdleten [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) för att publicera din Runbook. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Schemalägg en runbook i Azure Portal

När din Runbook har publicerats kan du schemalägga den för åtgärden:

1. Öppna runbooken i Azure Portal.
2. Välj **scheman** under **resurser**.
3. Välj **Lägg till ett schema**.
4. I fönstret Schemalägg Runbook väljer du **Länka ett schema till din Runbook**.
5. Välj **skapa ett nytt schema** i rutan schema.
6. Ange ett namn, en beskrivning och andra parametrar i fönstret Nytt schema.
7. När schemat har skapats markerar du det och klickar på **OK**. Den bör nu vara länkad till din Runbook.
8. Sök efter ett e-postmeddelande i din post låda för att meddela dig om Runbook-statusen.

## <a name="obtain-job-statuses"></a>Hämta jobb status

### <a name="view-statuses-in-the-azure-portal"></a>Visa status i Azure Portal

Information om jobb hantering i Azure Automation finns i [jobb](automation-runbook-execution.md#jobs). När du är redo att se dina Runbook-jobb använder du Azure Portal och får åtkomst till ditt Automation-konto. Till höger kan du se en översikt över alla Runbook-jobb i **jobb statistik**.

![Panel för jobb statistik](./media/manage-runbooks/automation-account-job-status-summary.png)

I sammanfattningen visas ett antal och en grafisk representation av jobb status för varje jobb som körs.

Om du klickar på panelen visas jobb sidan som innehåller en sammanfattande lista över alla jobb som körs. Den här sidan visar status, Runbook-namn, start tid och slut för ande tid för varje jobb.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Skärm bild av sidan jobb.":::

Du kan filtrera listan över jobb genom att välja **filter jobb**. Filtrera på en angiven Runbook, jobb status eller ett alternativ i list rutan och ange tidsintervallet för sökningen.

![Filtrera jobb status](./media/manage-runbooks/automation-account-jobs-filter.png)

Du kan också Visa jobb sammanfattnings information för en angiven Runbook genom att välja denna Runbook från sidan Runbooks i ditt Automation-konto och sedan välja **jobb**. Den här åtgärden visar sidan jobb. Härifrån kan du klicka på en jobb post för att visa information och utdata.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Skärm bild av sidan jobb med knappen fel markerat.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Hämta jobb status med PowerShell

Använd cmdleten [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) för att hämta jobb som skapats för en Runbook och information om ett visst jobb. Om du startar en Runbook med `Start-AzAutomationRunbook` , returnerar den det resulterande jobbet. Använd [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) för att hämta jobbets utdata.

Följande exempel hämtar det senaste jobbet för en exempel-Runbook och visar dess status, de värden som har angetts för Runbook-parametrarna och jobbets utdata.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

I följande exempel hämtas utdata för ett särskilt jobb och returnerar varje post. Om det finns ett [undantag](automation-runbook-execution.md#exceptions) för en av posterna skriver skriptet detta undantag i stället för värdet. Det här beteendet är användbart eftersom undantag kan ge ytterligare information som kanske inte loggas normalt under utdata.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Runbook-hantering finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Information om hur du förbereder en PowerShell-Runbook finns i [Redigera text-Runbooks i Azure Automation](automation-edit-textual-runbook.md).
* Information om hur du felsöker problem med Runbook-körning finns i [Felsöka Runbook-problem](troubleshoot/runbooks.md).