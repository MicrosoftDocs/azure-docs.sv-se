---
title: Starta en runbook i Azure Automation
description: Den här artikeln beskriver hur du startar en runbook i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01b6e060fcab9c7dab4934aad3d1ab6047ec5236
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829862"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starta en runbook i Azure Automation

Följande tabell hjälper dig att fastställa vilken metod du ska använda för att starta en runbook Azure Automation som passar bäst för just ditt scenario. Den här artikeln innehåller information om hur du startar en runbook med Azure Portal och med Windows PowerShell. Information om de andra metoderna finns i annan dokumentation som du kan komma åt via länkarna nedan.

| **Metod** | **Kännetecken** |
| --- | --- |
| [Azure-portalen](#start-a-runbook-with-the-azure-portal) |<li>Enklaste metoden med interaktivt användargränssnitt.<br> <li>Formulär för att ange enkla parametervärden.<br> <li>Spåra enkelt jobbtillstånd.<br> <li>Åtkomst autentiserad med Azure-inloggning. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Anropa från kommandoraden med Windows PowerShell cmdlets.<br> <li>Kan ingå i automatiserad funktion med flera steg.<br> <li>Begäran autentiseras med certifikatet eller OAuth-användarens huvudnamn/tjänstens huvudnamn.<br> <li>Ange enkla och komplexa parametervärden.<br> <li>Spåra jobbtillstånd.<br> <li>Klient som krävs för att stödja PowerShell-cmdlets. |
| [Azure Automation API](/rest/api/automation/) |<li>Den mest flexibla metoden men också mest komplex.<br> <li>Anropa från valfri anpassad kod som kan göra HTTP-begäranden.<br> <li>Begäran autentiserad med certifikat eller Oauth-användarens huvudnamn/tjänstens huvudnamn.<br> <li>Ange enkla och komplexa parametervärden. *Om du anropar en Python-runbook med hjälp av API:et måste JSON-nyttolasten serialiseras.*<br> <li>Spåra jobbtillstånd. |
| [Webhooks](automation-webhooks.md) |<li>Starta runbook från en enskild HTTP-begäran.<br> <li>Autentiserad med säkerhetstoken i URL:en.<br> <li>Klienten kan inte åsidosätta parametervärden som angavs när webhooken skapades. Runbook kan definiera en enskild parameter som fylls i med information om HTTP-begäran.<br> <li>Ingen möjlighet att spåra jobbtillstånd via webhook-URL. |
| [Svara på Azure-avisering](../azure-monitor/alerts/alerts-overview.md) |<li>Starta en runbook som svar på Azure-aviseringen.<br> <li>Konfigurera webhook för runbook och länka till avisering.<br> <li>Autentiserad med säkerhetstoken i URL:en. |
| [Schema](./shared-resources/schedules.md) |<li>Starta runbook automatiskt enligt schemat varje timme, varje dag, varje vecka eller varje månad.<br> <li>Ändra schemat via Azure Portal, PowerShell-cmdlets eller Azure API.<br> <li>Ange parametervärden som ska användas med schemat. |
| [Från en annan Runbook](automation-child-runbooks.md) |<li>Använd en runbook som en aktivitet i en annan runbook.<br> <li>Användbart för funktioner som används av flera runbooks.<br> <li>Ange parametervärden till underordnad runbook och använd utdata i överordnad runbook. |

Följande bild visar en detaljerad steg-för-steg-process i livscykeln för en runbook. Det omfattar olika sätt som en runbook startar i Azure Automation, vilka komponenter som krävs för att Hybrid Runbook Worker ska kunna köra Azure Automation runbooks och interaktioner mellan olika komponenter. Mer information om hur du kör Automation-runbooks i ditt datacenter finns i [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md)

![Runbook-arkitektur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Arbeta med runbook-parametrar

När du startar en runbook från Azure Portal eller Windows PowerShell skickas instruktionen via Azure Automation webbtjänsten. Den här tjänsten stöder inte parametrar med komplexa datatyper. Om du behöver ange ett värde för en komplex parameter måste du anropa det infogade från en annan runbook enligt beskrivningen i [Underordnade runbooks i Azure Automation](automation-child-runbooks.md).

Den Azure Automation webbtjänsten innehåller särskilda funktioner för parametrar som använder vissa datatyper enligt beskrivningen i följande avsnitt:

### <a name="named-values"></a>Namngivna värden

Om parametern är datatypen [object] kan du använda följande JSON-format för att skicka en lista med namngivna värden: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Dessa värden måste vara enkla typer. Runbooken tar emot parametern som [ett PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) med egenskaper som motsvarar varje namngivet värde.

Överväg följande test-Runbook som accepterar en parameter med namnet user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Följande text kan användas för user-parameter.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matriser

Om parametern är en matris som [array] eller [string[]] kan du använda följande JSON-format för att skicka en lista med värden: *[Value1, Value2, Value3]*. Dessa värden måste vara enkla typer.

Överväg följande test-Runbook som accepterar en parameter med namnet *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Följande text kan användas för user-parameter.

```input
["Joe","Smith",2,true]
```

Detta resulterar i följande utdata:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Autentiseringsuppgifter

Om parametern är `PSCredential` datatyp kan du ange namnet på en Azure Automation [för autentiseringsuppgifterna.](./shared-resources/credentials.md) Runbooken hämtar autentiseringssuppgifter med det namn som du anger. Följande test-runbook accepterar en parameter med namnet `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Följande text kan användas för användarparametern förutsatt att det fanns en autentiseringstillgång med namnet `My Credential` .

```input
My Credential
```

Förutsatt att användarnamnet i autentiseringsnamnet `jsmith` är visas följande utdata.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Starta en runbook med Azure Portal

1. I den Azure Portal väljer du **Automation** och klickar sedan på namnet på ett Automation-konto.
2. På navmenyn väljer du **Runbooks**.
3. På sidan Runbooks väljer du en runbook och klickar sedan på **Starta**.
4. Om runbooken har parametrar uppmanas du att ange värden med en textruta för varje parameter. Mer information om parametrar finns i [Runbook Parameters (Runbook-parametrar).](#work-with-runbook-parameters)
5. I fönstret Jobb kan du visa status för runbook-jobbet.

## <a name="start-a-runbook-with-powershell"></a>Starta en runbook med PowerShell

Du kan använda [Start-AzAutomationRunbook för](/powershell/module/az.automation/start-azautomationrunbook) att starta en runbook med Windows PowerShell. Följande exempelkod startar en runbook med namnet **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` returnerar ett jobbobjekt som du kan använda för att spåra status när runbooken har startats. Du kan sedan använda det här jobbobjektet [med Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) för att fastställa jobbets status och [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) för att hämta dess utdata. I följande exempel startar en runbook med **namnet Test-Runbook**, väntar tills den har slutförts och visar sedan dess utdata.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Om runbooken kräver parametrar måste du ange dem som en [hash-tabell.](/powershell/module/microsoft.powershell.core/about/about_hash_tables) Nyckeln för hash-tabellen måste matcha parameternamnet och värdet är parametervärdet. I följande exempel visas hur du startar en Runbook med två strängparametrar som heter FirstName och LastName, ett heltal som heter RepeatCount och en boolesk parameter som heter Show. Mer information om parametrar finns i [Runbook Parameters (Runbook-parametrar).](#work-with-runbook-parameters)

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>Nästa steg

* Mer information om runbookhantering finns i [Hantera runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell finns i [PowerShell Docs](/powershell/scripting/overview).
* Information om hur du felsöker problem med runbook-körning finns [i Felsöka runbook-problem.](troubleshoot/runbooks.md)
