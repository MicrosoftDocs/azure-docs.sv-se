---
title: Konfigurera runbook-utdata och meddelandeströmmar
description: Den här artikeln beskriver hur du implementerar felhanteringslogik och beskriver utdata- och meddelandeströmmar i Azure Automation runbooks.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 033900ddd0bd19332b4a9a996c68b3b187d631c4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833570"
---
# <a name="configure-runbook-output-and-message-streams"></a>Konfigurera runbook-utdata och meddelandeströmmar

De Azure Automation runbooks har någon form av utdata. Dessa utdata kan vara ett felmeddelande för användaren eller ett komplext objekt som är avsett att användas med en annan runbook. Windows PowerShell flera [strömmar för att](/powershell/module/microsoft.powershell.core/about/about_redirection) skicka utdata från ett skript eller arbetsflöde. Azure Automation fungerar på olika sätt med var och en av dessa strömmar. Du bör följa bästa praxis för att använda strömmarna när du skapar en runbook.

I följande tabell beskrivs kortfattat varje dataström med dess beteende i Azure Portal publicerade runbooks och under [testning av en Runbook](./manage-runbooks.md). Utdataströmmen är den huvudström som används för kommunikation mellan runbooks. De andra strömmarna klassificeras som meddelandeströmmar som är avsedda att kommunicera information till användaren.

| Stream | Description | Publicerad | Test |
|:--- |:--- |:--- |:--- |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter runbooken efter ett felmeddelande som standard. |Skrivs till jobbhistorik |Visas i fönstret Testutdata |
| Felsöka |Meddelanden avsedda för en interaktiv användare. Bör inte användas i runbooks. |Skrivs inte till jobbhistoriken |Visas inte i fönstret Testutdata |
| Utdata |Objekt som ska användas av andra Runbooks. |Skrivs till jobbhistorik |Visas i fönstret Testutdata |
| Förlopp |Poster som genereras automatiskt före och efter varje aktivitet i Runbooken. Runbooken bör inte försöka skapa egna förloppsposter eftersom de är avsedda för en interaktiv användare. |Skrivs endast till jobbhistoriken om förloppsloggning är aktiverat för runbooken |Visas inte i fönstret Testutdata |
| Verbose |Meddelanden som ger allmän information eller felsökningsinformation. |Skrivs endast till jobbhistoriken om utförlig loggning är aktiverat för runbooken |Visas bara i fönstret Testutdata `VerbosePreference` om variabeln har angetts till Fortsätt i runbook |
| Varning |Varningsmeddelande avsett för användaren. |Skrivs till jobbhistorik |Visas i fönstret Testutdata |

## <a name="use-the-output-stream"></a>Använda utdataströmmen

Utdataströmmen används för utdata från objekt som skapats av ett skript eller arbetsflöde när det körs korrekt. Azure Automation använder främst den här dataströmmen för objekt som ska användas av överordnade runbooks som anropar den [aktuella runbooken](automation-child-runbooks.md). När en överordnad [anropar en infogade runbook](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution)returnerar den underordnade data från utdataströmmen till den överordnade.

Din runbook använder utdataströmmen för att endast kommunicera allmän information till klienten om den aldrig anropas av en annan runbook. Som bästa praxis bör du dock normalt använda den utförliga dataströmmen [för att](#write-output-to-verbose-stream) kommunicera allmän information till användaren.

Be din runbook skriva data till utdataströmmen med [write-output.](/powershell/module/microsoft.powershell.utility/write-output) Du kan också placera objektet på en egen rad i skriptet.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Hantera utdata från en funktion

När en runbook-funktion skriver till utdataströmmen skickas utdata tillbaka till runbooken. Om runbooken tilldelar dessa utdata till en variabel skrivs inte utdata till utdataströmmen. Skrivning till andra strömmar inifrån funktionen skriver till motsvarande dataström för runbooken. Överväg följande PowerShell Workflow-exempel-runbook.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Utdataströmmen för runbook-jobbet är:

```output
Output inside of function
Output outside of function
```

Den utförliga dataströmmen för runbook-jobbet är:

```output
Verbose outside of function
Verbose inside of function
```

När du har publicerat runbooken och innan du startar den måste du också aktivera utförlig loggning i runbook-inställningarna för att få utförliga utdata från dataströmmen.

### <a name="declare-output-data-type"></a>Deklarera utdatatyp

Följande är exempel på typer av utdata:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarera utdatatyp i ett arbetsflöde

Ett arbetsflöde anger datatypen för dess utdata med hjälp av [attributet OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Det här attributet har ingen effekt under körning, men ger dig en indikation vid designtiden för förväntade utdata från runbooken. När verktygsuppsättningen för runbooks fortsätter att utvecklas, ökar vikten av att deklarera utdatatyper vid designtiden. Därför är det bästa praxis att inkludera den här deklarationen i alla runbooks som du skapar.

Följande exempel-Runbook matar ut ett strängobjekt och innehåller en förklaring av utdatatypen. Om din Runbook matar ut en matris av en viss typ bör du fortfarande ange typen, till skillnad från en matris av typen.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarera utdatatyp i en grafisk runbook

Om du vill deklarera en utdatatyp i en grafisk eller grafisk PowerShell Workflow-runbook kan du välja menyalternativet **Indata** och Utdata och ange utdatatypen. Vi rekommenderar att du använder det fullständiga .NET-klassnamnet för att göra typen lätt att identifiera när en överordnad runbook refererar till den. Om du använder det fullständiga namnet exponeras alla egenskaper för klassen för databusen i runbooken och ökar flexibiliteten när egenskaperna används för villkorsstyrd logik, loggning och referens som värden för andra Runbook-aktiviteter.<br> ![Alternativet Indata och utdata för Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>När du har skrivit  ett värde i fältet Utdatatyp i egenskapsfönstret För indata och utdata klickar du utanför kontrollen så att den identifierar din post.

I följande exempel visas två grafiska runbooks för att demonstrera indata- och utdatafunktionen. Genom att använda den modulära runbook-designmodellen har du en runbook som autentiseringsmall för Runbook som hanterar autentisering med Azure med hjälp av Kör som-kontot. Den andra runbooken, som normalt utför kärnlogik för att automatisera ett visst scenario, kör i det här fallet mallen Autentisera runbook. Resultatet visas i fönstret Testutdata. Under normala omständigheter skulle denna runbook göra något mot en resurs som utnyttjar utdata från den underordnade runbooken.

Här är den grundläggande logiken i **runbooken AuthenticateTo-Azure.**<br> ![Exempel på autentisering av Runbook-mall. ](media/automation-runbook-output-and-messages/runbook-authentication-template.png)

Runbooken innehåller utdatatypen `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , som returnerar egenskaperna för autentiseringsprofilen.<br> ![Exempel på runbook-utdatatyp](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Även om den här runbooken är enkel finns det ett konfigurationsobjekt att påpeka här. Den sista aktiviteten kör `Write-Output` cmdleten för att skriva profildata till en variabel med hjälp av ett PowerShell-uttryck för `Inputobject` parametern. Den här parametern krävs för `Write-Output` .

Den andra runbooken i det här exemplet, **med namnet Test-ChildOutputType,** definierar bara två aktiviteter.<br> ![Exempel på runbook av underordnad utdatatyp](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Den första aktiviteten anropar **Runbooken AuthenticateTo-Azure.** Den andra aktiviteten kör `Write-Verbose` cmdleten med **Datakälla inställt** på **Aktivitetsutdata**. Dessutom är **Fältsökväg** inställd på **Context.Subscription.SubscriptionName**, kontextutdata från **runbooken AuthenticateTo-Azure.**<br> ![Write-Verbose Cmdlet Parameter Data Source](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Resulterande utdata är namnet på prenumerationen.<br> ![Test-ChildOutputType runbook-resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Arbeta med meddelandeströmmar

Till skillnad från utdataströmmen kommunicerar meddelandeströmmar information till användaren. Det finns flera meddelandeströmmar för olika typer av information och Azure Automation hanterar varje dataström på olika sätt.

### <a name="write-output-to-warning-and-error-streams"></a>Skriva utdata till varnings- och felströmmar

Varningen och felet strömmar loggproblem som uppstår i en runbook. Azure Automation skriver dessa strömmar till jobbhistoriken när du kör en runbook. Automation innehåller strömmarna i fönstret Testutdata i Azure Portal när en runbook testas.

Som standard fortsätter en runbook att köras efter en varning eller ett fel. Du kan ange att runbooken ska pausas vid en varning eller ett fel genom att ange en inställningsvariabel [för runbooken](#work-with-preference-variables) innan meddelandet skapas. Om du till exempel vill göra så att runbooken pausas vid ett fel som vid ett undantag anger du `ErrorActionPreference` variabeln till Stoppa.

Skapa en varning eller ett felmeddelande med hjälp av [cmdleten Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) [eller Write-Error.](/powershell/module/microsoft.powershell.utility/write-error) Aktiviteter kan också skriva till varnings- och felströmmarna.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Skriva utdata till felsökningsströmmen

Azure Automation använder meddelandeströmmen för felsökning för interaktiva användare. Som Azure Automation inte några felsökningsdata, samlas endast utdata, fel- och varningsdata in samt utförliga data om runbooken är konfigurerad för att avbilda den.

För att kunna samla in data från felsökningsströmmar måste du utföra två åtgärder i dina runbooks:

1. Ange variabeln `$GLOBAL:DebugPreference="Continue"` , som anger att PowerShell ska fortsätta när ett felsökningsmeddelande påträffas.  Den **$GLOBAL:** delen talar om för PowerShell att göra detta i det globala omfånget i stället för det lokala omfång som skriptet finns i när instruktionen körs.

1. Omdirigera felsökningsströmmen som vi inte avbildar till en dataström som vi samlar in, till exempel *utdata*. Detta görs genom att ställa in PowerShell-omdirigering mot instruktionen som ska köras. Mer information om PowerShell-omdirigering finns i [Om omdirigering.](/powershell/module/microsoft.powershell.core/about/about_redirection)

#### <a name="examples"></a>Exempel

I det här exemplet konfigureras runbooken med hjälp av cmdletarna och i syfte att `Write-Output` mata ut två olika `Write-Debug` strömmar.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Om denna runbook skulle köras som den är skulle utdatafönstret för runbook-jobbet strömma följande utdata:

```output
This is an output message.
```

I det här exemplet konfigureras runbooken på liknande sätt som i föregående exempel, förutom att -instruktionen ingår med tillägget i `$GLOBAL:DebugPreference="Continue"` `5>&1` slutet av `Write-Debug` -instruktionen.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Om denna runbook skulle köras skulle utdatafönstret för runbook-jobbet strömma följande utdata:

```output
This is an output message.
This is a debug message.
```

Detta beror på att -instruktionen talar om för PowerShell att visa felsökningsmeddelanden, och tillägget i slutet av instruktionen talar om för PowerShell att omdirigera `$GLOBAL:DebugPreference="Continue"` `5>&1` ström `Write-Debug` 5 (felsökning) till att strömma 1 (utdata).

### <a name="write-output-to-verbose-stream"></a>Skriva utdata till utförlig ström

Den utförliga meddelandeströmmen stöder allmän information om runbook-åtgärd. Eftersom felsökningsströmmen inte är tillgänglig för en Runbook bör din runbook använda utförliga meddelanden för felsökningsinformation.

Som standard lagrar inte jobbhistoriken utförliga meddelanden från publicerade runbooks av prestandaskäl. Om du vill lagra utförliga meddelanden  Azure Portal fliken  Konfigurera med inställningen Logg utförliga poster för att konfigurera dina publicerade runbooks att logga utförliga meddelanden. Aktivera bara det här alternativet om du behöver felsöka en runbook. I de flesta fall bör du behålla standardinställningen att inte logga utförliga poster.

När [du testar en runbook](./manage-runbooks.md)visas inte utförliga meddelanden även om runbooken är konfigurerad för att logga utförliga poster. Om du vill visa utförliga meddelanden [när du testar en Runbook](./manage-runbooks.md)måste du ange `VerbosePreference` variabeln till Fortsätt. Med den variabeluppsättningen visas utförliga meddelanden i fönstret Testutdata i Azure Portal.

Följande kod skapar ett utförligt meddelande med hjälp av [cmdleten Write-Verbose.](/powershell/module/microsoft.powershell.utility/write-verbose)

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Hantera förloppsposter

Du kan använda fliken **Konfigurera** i den här Azure Portal konfigurera en runbook för att logga förloppsposter. Standardinställningen är att inte logga posterna för att maximera prestanda. I de flesta fall bör du behålla standardinställningen. Aktivera bara det här alternativet om du behöver felsöka en runbook.

Om du aktiverar förloppspostloggning skriver runbooken en post till jobbhistoriken före och efter att varje aktivitet körs. Om du testar en runbook visas inte förloppsmeddelanden även om runbooken är konfigurerad för att logga förloppsposter.

>[!NOTE]
>Cmdleten [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) är inte giltig i en runbook, eftersom denna cmdlet är avsedd att användas med en interaktiv användare.

## <a name="work-with-preference-variables"></a>Arbeta med inställningsvariabler

Du kan ange vissa Windows PowerShell [inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables) i dina runbooks för att styra svaret på data som skickas till olika utdataströmmar. I följande tabell visas de inställningsvariabler som kan användas i runbooks, med deras standardvärden och giltiga värden. Ytterligare värden är tillgängliga för inställningsvariablerna när de används Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| `WarningPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `ErrorActionPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppa<br>Fortsätt<br>SilentlyContinue |

I nästa tabell visas beteendet för de inställningsvariabelvärden som är giltiga i runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätt |Loggar meddelandet och fortsätter att köra Runbooken. |
| SilentlyContinue |Fortsätter att köra Runbooken utan att logga meddelandet. Det här värdet innebär att meddelandet ignoreras. |
| Stoppa |Loggar meddelandet och gör uppehåll i körningen av Runbooken. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Hämta runbook-utdata och meddelanden

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Hämta runbook-utdata och meddelanden i Azure Portal

Du kan visa information om ett Runbook-jobb i Azure Portal med hjälp **av fliken** Jobb för runbooken. Jobbsammanfattningen visar indataparametrarna och utdataströmmen [,](#use-the-output-stream)förutom allmän information om jobbet och eventuella undantag som har inträffat. Jobbhistoriken innehåller meddelanden från utdataströmmen och [varnings- och felströmmar](#write-output-to-warning-and-error-streams). Den innehåller även meddelanden från den [utförliga dataströmmen](#write-output-to-verbose-stream) och förloppsposter om runbooken är konfigurerad för att logga utförliga poster och förloppsposter. [](#handle-progress-records)

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Hämta runbook-utdata och meddelanden i Windows PowerShell

I Windows PowerShell kan du hämta utdata och meddelanden från en runbook med hjälp av cmdleten [Get-AzAutomationJobOutput.](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) Den här cmdleten kräver ID:t för jobbet och har en parameter med namnet `Stream` som anger vilken dataström som ska hämtas. Du kan ange värdet Alla för den här parametern för att hämta alla strömmar för jobbet.

I följande exempel startas en exempel-Runbook och väntar sedan på att den ska slutföras. När runbooken har körts klart samlar skriptet in runbook-utdataströmmen från jobbet.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Hämta runbook-utdata och meddelanden i grafiska runbooks

För grafiska runbooks finns extra loggning av utdata och meddelanden i form av spårning på aktivitetsnivå. Det finns två spårningsnivåer: Grundläggande och detaljerad. Grundläggande spårning visar start- och sluttid för varje aktivitet i runbooken, plus information relaterad till eventuella aktivitetsförsök. Några exempel är antalet försök och aktivitetens starttid. Detaljerad spårning innehåller grundläggande spårningsfunktioner plus loggning av indata och utdata för varje aktivitet. 

För närvarande skriver spårning på aktivitetsnivå poster med hjälp av den utförliga strömmen. Därför måste du aktivera utförlig loggning när du aktiverar spårning. För grafiska runbooks med spårning aktiverat behöver du inte logga förloppsposter. Grundläggande spårning har samma syfte och är mer informativ.

![Vy över grafiska redigeringsjobbströmmar](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Du kan se i bilden att om du aktiverar utförlig loggning och spårning för grafiska runbooks blir mycket mer information tillgänglig i **produktionsvyn Jobbströmmar.** Den här extra informationen kan vara viktig vid felsökning av produktionsproblem med en runbook. 

Men om du inte kräver den här informationen för att spåra förloppet för en runbook för felsökning, kanske du vill behålla spårning inaktiverat som en allmän praxis. Spårningsposterna kan vara särskilt många. Med grafisk runbook-spårning kan du få två till fyra poster per aktivitet, beroende på din konfiguration av grundläggande eller detaljerad spårning.

**Så här aktiverar du spårning på aktivitetsnivå:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Processautomatisering** för att öppna listan över runbooks.
3. På sidan Runbooks väljer du en grafisk runbook från din lista över runbooks.
4. Under **Inställningar klickar** du på **Loggning och spårning.**
5. På sidan Loggning och spårning går du till **Logga utförliga poster och klickar** på På **för** att aktivera utförlig loggning.
6. Under **Spårning på aktivitetsnivå** ändrar du spårningsnivån till **Basic** eller **Detaljerad,** baserat på vilken spårningsnivå du behöver.<br>

   ![Loggnings- och spårningssida för grafisk redigering](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Hämta runbook-utdata och meddelanden i Microsoft Azure Monitor-loggar

Azure Automation kan skicka status för runbook-jobb och jobbströmmar till Log Analytics-arbetsytan. Azure Monitor har stöd för loggar som gör att du kan:

* Få insikter om dina Automation-jobb.
* Utlösa ett e-postmeddelande eller en avisering baserat på statusen för runbook-jobbet, till exempel Misslyckades eller Pausad.
* Skriva avancerade frågor mellan jobbströmmar.
* Korrelera jobb mellan Automation-konton.
* Visualisera jobbhistorik.

Mer information om hur du konfigurerar integrering med Azure Monitor-loggar för att samla in, korrelera och agera på jobbdata finns i Vidarebefordra [jobbstatus](automation-manage-send-joblogs-log-analytics.md)och jobbströmmar från Automation till Azure Monitor Logs .

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med runbooks finns [i Hantera runbooks i Azure Automation](manage-runbooks.md).
* Om du inte är bekant med PowerShell-skript kan du läsa [PowerShell-dokumentationen.](/powershell/scripting/overview)
* Mer information Azure Automation PowerShell-cmdlet finns i [Az.Automation](/powershell/module/az.automation).
