---
title: Starta en runbook i Azure Automation
description: Den här artikeln beskriver hur du startar en Runbook i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7cc1a25fbfc4d3f063c6fc6d225c3f5c7a4b5a3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572553"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starta en runbook i Azure Automation

I följande tabell får du hjälp att avgöra hur du ska starta en Runbook i Azure Automation som passar bäst för ditt specifika scenario. Den här artikeln innehåller information om hur du startar en Runbook med Azure Portal och med Windows PowerShell. Information om andra metoder finns i annan dokumentation som du kan komma åt från länkarna nedan.

| **Metod** | **Kännetecken** |
| --- | --- |
| [Azure-portalen](#start-a-runbook-with-the-azure-portal) |<li>Enklaste metoden med Interactive User Interface.<br> <li>Form för att tillhandahålla enkla parameter värden.<br> <li>Spåra jobbets status enkelt.<br> <li>Åtkomst autentiserad med Azure-inloggning. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Anropa från kommando raden med Windows PowerShell-cmdletar.<br> <li>Kan inkluderas i automatiserad funktion med flera steg.<br> <li>Begäran autentiseras med certifikat eller användar huvud/tjänstens huvud namn för OAuth-användare.<br> <li>Ange enkla och komplexa parameter värden.<br> <li>Spåra jobb status.<br> <li>Klienten krävde för att stödja PowerShell-cmdletar. |
| [Azure Automation-API](/rest/api/automation/) |<li>Den mest flexibla metoden, men även mest komplex.<br> <li>Anropa från en anpassad kod som kan göra HTTP-förfrågningar.<br> <li>Begäran autentiserad med certifikat eller användar huvud för OAuth/tjänstens huvud namn.<br> <li>Ange enkla och komplexa parameter värden. *Om du anropar en python-Runbook med API: t måste JSON-nyttolasten serialiseras.*<br> <li>Spåra jobb status. |
| [Webhooks](automation-webhooks.md) |<li>Starta Runbook från en enskild HTTP-begäran.<br> <li>Autentiserad med säkerhetstoken i URL.<br> <li>Klienten kan inte åsidosätta parameter värden som anges när webhooken skapades. En Runbook kan definiera en enda parameter som är ifylld med HTTP-begärans information.<br> <li>Det går inte att spåra jobb status via webhook-URL. |
| [Svara på Azure-avisering](../azure-monitor/alerts/alerts-overview.md) |<li>Starta en Runbook som svar på Azure-aviseringen.<br> <li>Konfigurera webhook för Runbook och länka till avisering.<br> <li>Autentiserad med säkerhetstoken i URL. |
| [Schema](./shared-resources/schedules.md) |<li>Starta automatiskt Runbook på varje timme, varje dag, varje vecka eller månads schema.<br> <li>Manipulera schemat via Azure Portal, PowerShell-cmdletar eller Azure API.<br> <li>Ange parameter värden som ska användas med schemat. |
| [Från en annan Runbook](automation-child-runbooks.md) |<li>Använd en Runbook som en aktivitet i en annan Runbook.<br> <li>Användbart för funktioner som används av flera Runbooks.<br> <li>Ange parameter värden för underordnad Runbook och Använd utdata i överordnad Runbook. |

Följande bild illustrerar en detaljerad steg-för-steg-process i livs cykeln för en Runbook. Den innehåller olika sätt som en Runbook startar i Azure Automation, vilka komponenter som krävs för att Hybrid Runbook Worker ska kunna köra Azure Automation runbooks och samverkan mellan olika komponenter. Information om hur du kör Automation-runbooks i ditt data Center finns i [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook-arkitektur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Arbeta med Runbook-parametrar

När du startar en Runbook från Azure Portal eller Windows PowerShell skickas instruktionen via Azure Automation-webbtjänsten. Den här tjänsten stöder inte parametrar med komplexa data typer. Om du behöver ange ett värde för en komplex parameter måste du anropa den infogad från en annan Runbook enligt beskrivningen i [underordnade Runbooks i Azure Automation](automation-child-runbooks.md).

Azure Automation-webbtjänsten tillhandahåller särskilda funktioner för parametrar med vissa data typer som beskrivs i följande avsnitt:

### <a name="named-values"></a>Namngivna värden

Om parametern är data typen [objekt], kan du använda följande JSON-format för att skicka en lista över namngivna värden: *{Name1: "värde1", Name2: "värde2", Name3: ' Value3 '}*. Dessa värden måste vara enkla typer. Runbooken tar emot parametern som en [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) med egenskaper som motsvarar varje namngivet värde.

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

Om parametern är en matris som t. ex. [array] eller [string []], kan du använda följande JSON-format för att skicka en lista med värden: *[värde1, värde2, Value3]*. Dessa värden måste vara enkla typer.

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

Om parametern är datatyp `PSCredential` kan du ange namnet på en Azure Automation [behörighet till till gång](./shared-resources/credentials.md). Runbooken hämtar autentiseringsuppgiften med det namn som du anger. Följande test-Runbook accepterar en parameter med namnet `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Följande text kan användas för användar parametern förutsatt att det fanns en referens till gång som kallas `My Credential` .

```input
My Credential
```

Förutsatt att användar namnet i autentiseringsuppgiften är `jsmith` visas följande utdata.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Starta en Runbook med Azure Portal

1. I Azure Portal väljer du **Automation** och klickar sedan på namnet på ett Automation-konto.
2. På menyn hubb väljer du **Runbooks**.
3. På sidan Runbooks väljer du en Runbook och klickar sedan på **Starta**.
4. Om runbooken har parametrar uppmanas du att ange värden med en text ruta för varje parameter. Mer information om parametrar finns i [Runbook-parametrar](#work-with-runbook-parameters).
5. I fönstret jobb kan du visa statusen för Runbook-jobbet.

## <a name="start-a-runbook-with-powershell"></a>Starta en Runbook med PowerShell

Du kan använda [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta en Runbook med Windows PowerShell. Följande exempel kod startar en Runbook med namnet **test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` Returnerar ett jobb objekt som du kan använda för att spåra status när runbooken har startats. Du kan sedan använda detta jobb objekt med [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) för att bestämma status för jobbet och [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) för att hämta dess utdata. I följande exempel startas en Runbook med namnet **test-Runbook**, väntar tills den har slutförts och visar sedan dess utdata.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Om Runbook kräver parametrar måste du ange dem som en [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables)-form. Nyckeln för hash-tabellen måste matcha parameter namnet och värdet är parametervärdet. I följande exempel visas hur du startar en Runbook med två strängparametrar som heter FirstName och LastName, ett heltal som heter RepeatCount och en boolesk parameter som heter Show. Mer information om parametrar finns i [Runbook-parametrar](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Runbook-hantering finns [i hantera Runbooks i Azure Automation](manage-runbooks.md).
* Information om PowerShell finns i [PowerShell-dokument](/powershell/scripting/overview).
* Information om hur du felsöker problem med Runbook-körning finns i [Felsöka Runbook-problem](troubleshoot/runbooks.md).
