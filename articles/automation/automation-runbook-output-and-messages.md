---
title: Konfigurera Runbook-utdata och meddelande strömmar
description: Den här artikeln beskriver hur du implementerar fel hanterings logik och beskriver utdata och meddelande strömmar i Azure Automation runbooks.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: bb14d5227204a69f8a2ef9e0bf2da05bd7bde51c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169104"
---
# <a name="configure-runbook-output-and-message-streams"></a>Konfigurera Runbook-utdata och meddelande strömmar

De flesta Azure Automation runbooks har någon form av utdata. Utdata kan vara ett fel meddelande till användaren eller ett komplext objekt som är avsett att användas med en annan Runbook. Windows PowerShell innehåller [flera strömmar](/powershell/module/microsoft.powershell.core/about/about_redirection) för att skicka utdata från ett skript eller ett arbets flöde. Azure Automation fungerar annorlunda med var och en av dessa strömmar. Du bör följa bästa praxis för att använda strömmarna när du skapar en Runbook.

I följande tabell beskrivs en kort beskrivning av varje data ström med dess beteende i Azure Portal för publicerade Runbooks och vid [testning av en Runbook](./manage-runbooks.md). Utdataströmmen är den huvud data ström som används för kommunikation mellan Runbooks. De andra strömmarna klassificeras som meddelande strömmar som är avsedda att kommunicera information till användaren.

| Stream | Beskrivning | Publicerad | Test |
|:--- |:--- |:--- |:--- |
| Fel |Felmeddelande avsett för användaren. Till skillnad från ett undantag fortsätter Runbook efter ett fel meddelande som standard. |Skrivs till jobb historik |Visas i fönstret Testa utdata |
| Felsöka |Meddelanden avsedda för en interaktiv användare. Bör inte användas i Runbooks. |Skrivs inte till jobb historik |Visas inte i fönstret Testa utdata |
| Utdata |Objekt som ska användas av andra Runbooks. |Skrivs till jobb historik |Visas i fönstret Testa utdata |
| Förlopp |Poster som genereras automatiskt före och efter varje aktivitet i Runbooken. Runbooken bör inte försöka skapa egna status poster eftersom de är avsedda för en interaktiv användare. |Skrivs till jobb historiken endast om förlopps loggning har Aktiver ATS för Runbook |Visas inte i fönstret Testa utdata |
| Verbose |Meddelanden som ger allmän eller felsöknings information. |Skrivs till jobb historiken endast om utförlig loggning har Aktiver ATS för Runbook |Visas endast i fönstret för att testa utdata om `VerbosePreference` variabeln är inställd på Fortsätt i Runbook |
| Varning |Varningsmeddelande avsett för användaren. |Skrivs till jobb historik |Visas i fönstret Testa utdata |

## <a name="use-the-output-stream"></a>Använda utdataströmmen

Utdataströmmen används för utdata från objekt som har skapats av ett skript eller ett arbets flöde när den körs på rätt sätt. Azure Automation använder främst den här data strömmen för objekt som ska konsumeras av överordnade Runbooks som anropar den [aktuella runbooken](automation-child-runbooks.md). När en överordnad [anropar en Runbook infogad](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), returnerar den underordnade data från utdataströmmen till den överordnade.

Din Runbook använder utdataströmmen för att kommunicera allmän information till klienten endast om den aldrig anropas av en annan Runbook. Som bästa praxis bör du normalt använda den [utförliga data strömmen](#write-output-to-verbose-stream) för att förmedla allmän information till användaren.

Låta din Runbook skriva data till utdataströmmen med hjälp av [Write-output](/powershell/module/microsoft.powershell.utility/write-output). Du kan också flytta objektet på en egen rad i skriptet.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Hantera utdata från en funktion

När en Runbook-funktion skriver till utdataströmmen skickas utdata tillbaka till runbooken. Om Runbook tilldelar utdata till en variabel, skrivs inte utdata till utdataströmmen. Skrivning till andra strömmar inifrån funktionen skriver till motsvarande ström för runbooken. Överväg följande exempel på PowerShell Workflow-Runbook.

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

Utdataströmmen för Runbook-jobbet är:

```output
Output inside of function
Output outside of function
```

Utförlig data ström för Runbook-jobbet är:

```output
Verbose outside of function
Verbose inside of function
```

När du har publicerat runbooken och innan du startar den, måste du också aktivera utförlig loggning i Runbook-inställningarna för att få utförliga data strömmar.

### <a name="declare-output-data-type"></a>Deklarerar data typen utdata

Följande är exempel på utdata av data typer:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarerar utdata-datatyp i ett arbets flöde

Ett arbets flöde anger data typen för utdata med hjälp av [OutputType-attributet](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Det här attributet har ingen effekt under körningen, men ger dig en indikation i design läge för den förväntade utmatningen av runbooken. När verktyget som angetts för Runbooks fortsätter att utvecklas ökar vikten av att deklarera utdatatyper i design tiden. Därför är det en bra idé att inkludera den här deklarationen i alla Runbooks som du skapar.

Följande exempel-Runbook matar ut ett strängobjekt och innehåller en förklaring av utdatatypen. Om din Runbook matar ut en matris av en viss typ bör du fortfarande ange typen, till skillnad från en matris av typen.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarera utdata som datatyp i en grafisk Runbook

Om du vill deklarera en utdatatyp i ett grafiskt eller grafiskt PowerShell-arbetsflöde i Runbook-flödet kan du välja meny alternativet **indata och utdata** och ange utdatatypen. Vi rekommenderar att du använder det fullständiga namnet på .NET-klassen för att göra typen lätt att identifiera när en överordnad Runbook refererar till den. Om du använder det fullständiga namnet exponeras alla egenskaper för klassen i data bussen i runbooken och ökar flexibiliteten när egenskaperna används för villkorlig logik, loggning och referenser som värden för andra Runbook-aktiviteter.<br> ![Alternativ för indata och utdata för Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>När du har angett ett värde **i fältet Utdatatyp i** fönstret Egenskaper för indata och utdata, se till att klicka utanför kontrollen så att den känner igen posten.

I följande exempel visas två grafiska runbooks som demonstrerar funktionerna för indata och utdata. När du använder design modellen för modulär Runbook har du en Runbook som mall för att autentisera Runbook hantera autentisering med Azure med hjälp av kör som-kontot. Den andra runbooken, som vanligt vis utför kärn logik för att automatisera ett specifikt scenario, kör i det här fallet mallen autentisera Runbook. Resultatet visas i fönstret Testa utdata. Under normala omständigheter skulle du ha denna Runbook att göra något mot en resurs som utnyttjar utdata från den underordnade runbooken.

Här är den grundläggande logiken i **AuthenticateTo-Azure-** runbooken.<br> ![Exempel på autentisering av Runbook-mall ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) .

Runbooken innehåller utdatatypen `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , som returnerar egenskaperna för autentiseringsinställningarna.<br> ![Exempel på Utdatatyp för Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Även om denna Runbook är enkel, finns det ett konfigurations objekt som kan anropas här. Den senaste aktiviteten kör `Write-Output` cmdleten för att skriva profil data till en variabel med hjälp av ett PowerShell-uttryck för `Inputobject` parametern. Den här parametern krävs för `Write-Output` .

Den andra runbooken i det här exemplet, med namnet **test-ChildOutputType**, definierar bara två aktiviteter.<br> ![Exempel på underordnad Utdatatyp Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Den första aktiviteten anropar **AuthenticateTo-Azure-** runbooken. Den andra aktiviteten kör `Write-Verbose` cmdleten med **data källa** inställt på **aktivitets utdata**. Dessutom anges **fält Sök vägen** till **context. Subscription. SubscriptionName**, kontext resultatet från **AuthenticateTo-Azure-** runbooken.<br> ![Data källa för Write-utförlig cmdlet-parameter](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Resultatet är namnet på prenumerationen.<br> ![Test-ChildOutputType Runbook-resultat](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Arbeta med meddelande strömmar

Till skillnad från utdataströmmen skickar meddelande strömmar information till användaren. Det finns flera meddelande strömmar för olika typer av information och Azure Automation hanterar varje data ström på olika sätt.

### <a name="write-output-to-warning-and-error-streams"></a>Skriv utdata till varnings-och fel strömmar

Varnings-och fel strömmar loggar problem som inträffar i en Runbook. Azure Automation skriver dessa strömmar till jobb historiken när en runbook körs. Automation innehåller strömmar i fönstret testutdata i Azure Portal när en Runbook testas.

Som standard fortsätter en Runbook att köras efter en varning eller ett fel. Du kan ange att din Runbook ska inaktive ras vid en varning eller ett fel genom att ange en [inställnings variabel](#work-with-preference-variables) i Runbook innan du skapar meddelandet. Om du t. ex. vill göra så att runbooken pausas vid ett fel eftersom det sker vid ett undantag anger du att `ErrorActionPreference` variabeln ska stoppas.

Skapa en varning eller ett fel meddelande med hjälp av cmdleten [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) eller [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) . Aktiviteter kan också skriva till varnings-och fel strömmar.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Skriv utdata till fel söknings ström

Azure Automation använder data strömmen för fel söknings meddelande för interaktiva användare. Som standard samlar Azure Automation inte in några fel söknings data, endast utdata, fel och varnings data samlas in, samt utförliga data om runbooken har kon figurer ATS för att avbilda den.

För att kunna samla in fel söknings data strömmar måste du utföra två åtgärder i dina runbooks:

1. Ange variabeln `$GLOBAL:DebugPreference="Continue"` , som anger att PowerShell ska fortsätta när ett fel söknings meddelande påträffas.  Avsnittet **$Global:** anger att PowerShell ska göra detta i det globala omfånget i stället för det lokala omfång som skriptet är i vid den tidpunkt då instruktionen körs.

1. Omdirigera fel söknings strömmen som vi inte fångar till en ström som vi samlar in, till exempel *utdata*. Detta görs genom att ställa in PowerShell-omdirigering mot instruktionen som ska köras. Mer information om PowerShell-omdirigering finns i [om omdirigering](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Exempel

I det här exemplet konfigureras runbooken med `Write-Output` `Write-Debug` cmdletarna och med avsikt att placera två olika strömmar.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Om denna Runbook skulle köras i det här fallet strömmar utdata-fönstret för Runbook-jobbet följande utdata:

```output
This is an output message.
```

I det här exemplet konfigureras runbooken som liknar föregående exempel, förutom instruktionen som `$GLOBAL:DebugPreference="Continue"` ingår i `5>&1` slutet av `Write-Debug` instruktionen.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Om denna Runbook skulle köras, strömma utdata-fönstret för Runbook-jobbet följande utdata:

```output
This is an output message.
This is a debug message.
```

Detta inträffar eftersom `$GLOBAL:DebugPreference="Continue"` instruktionen meddelar PowerShell att visa fel söknings meddelanden, och tillägget `5>&1` till slutet av `Write-Debug` instruktionen anger att PowerShell ska omdirigera Stream 5 (debug) till Stream 1 (utdata).

### <a name="write-output-to-verbose-stream"></a>Skriv utdata till utförlig ström

Den utförliga meddelande strömmen stöder allmän information om Runbook-åtgärder. Eftersom fel söknings strömmen inte är tillgänglig för en Runbook ska din Runbook använda utförliga meddelanden för felsöknings information.

Som standard lagrar jobb historiken inte utförliga meddelanden från publicerade Runbooks av prestanda skäl. Om du vill lagra utförliga meddelanden använder du fliken Azure Portal **Konfigurera** med inställningen **Logga utförliga poster** för att konfigurera dina publicerade Runbooks att logga utförliga meddelanden. Aktivera bara det här alternativet om du behöver felsöka en runbook. I de flesta fall bör du behålla standardinställningen att inte logga utförliga poster.

När du [testar en Runbook](./manage-runbooks.md)visas inte utförliga meddelanden även om runbooken har kon figurer ATS för att logga utförliga poster. Om du vill visa utförliga meddelanden när du [testar en Runbook](./manage-runbooks.md)måste du ange `VerbosePreference` att variabeln ska fortsätta. Med den variabel uppsättningen visas utförliga meddelanden i fönstret Testa utdata i Azure Portal.

Följande kod skapar ett utförligt meddelande med hjälp av [Write-Verbose-](/powershell/module/microsoft.powershell.utility/write-verbose) cmdleten.

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Hantera status poster

Du kan använda fliken **Konfigurera** i Azure Portal om du vill konfigurera en Runbook att logga status poster. Standardvärdet är att inte logga posterna för att maximera prestanda. I de flesta fall bör du behålla standardinställningen. Aktivera bara det här alternativet om du behöver felsöka en runbook.

Om du aktiverar loggning av status poster skriver din Runbook en post till jobb historiken innan och efter varje aktivitet körs. Om du testar en Runbook visas inga förlopps meddelanden även om runbooken har kon figurer ATS för att logga status poster.

>[!NOTE]
>Cmdleten [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) är inte giltig i en Runbook eftersom denna cmdlet är avsedd att användas med en interaktiv användare.

## <a name="work-with-preference-variables"></a>Arbeta med Preferences-variabler

Du kan ställa in vissa variabler för Windows PowerShell- [Inställningar](/powershell/module/microsoft.powershell.core/about/about_preference_variables) i dina runbooks för att kontrol lera svaret på data som skickas till olika utgående data strömmar. I följande tabell visas de inställningar för variabler som kan användas i Runbooks, med standardvärdena och giltiga värden. Ytterligare värden är tillgängliga för Preferences-variablerna när de används i Windows PowerShell utanför Azure Automation.

| Variabel | Standardvärde | Giltiga värden |
|:--- |:--- |:--- |
| `WarningPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `ErrorActionPreference` |Fortsätt |Stoppa<br>Fortsätt<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppa<br>Fortsätt<br>SilentlyContinue |

I nästa tabell visas beteendet för de preferens variabel värden som är giltiga i Runbooks.

| Värde | Beteende |
|:--- |:--- |
| Fortsätt |Loggar meddelandet och fortsätter att köra Runbooken. |
| SilentlyContinue |Fortsätter att köra Runbooken utan att logga meddelandet. Det här värdet innebär att meddelandet ignoreras. |
| Stoppa |Loggar meddelandet och gör uppehåll i körningen av Runbooken. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Hämta utdata och meddelanden för Runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Hämta utdata och meddelanden för Runbook i Azure Portal

Du kan visa information om ett Runbook-jobb i Azure Portal på fliken **jobb** för runbooken. Jobb sammanfattningen visar indataparametrarna och [utdataströmmen](#use-the-output-stream), förutom allmän information om jobbet och eventuella undantag som har inträffat. Jobb historiken innehåller meddelanden från utdataströmmen och [varnings-och fel strömmar](#write-output-to-warning-and-error-streams). Den innehåller också meddelanden från [utförlig data ström](#write-output-to-verbose-stream) och [förlopps poster](#handle-progress-records) om runbooken har kon figurer ATS för att logga utförliga och förlopps poster.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Hämta Runbook-utdata och meddelanden i Windows PowerShell

I Windows PowerShell kan du hämta utdata och meddelanden från en Runbook med hjälp av cmdleten [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) . Denna cmdlet kräver jobbets ID och har en parameter som kallas för `Stream` att ange den data ström som ska hämtas. Du kan ange ett värde för den här parametern för att hämta alla strömmar för jobbet.

I följande exempel startas en exempel-Runbook och väntar sedan på att den ska slutföras. När runbook har slutfört körningen samlar skriptet in Runbook-utdata från jobbet.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Hämta Runbook-utdata och meddelanden i grafiska runbooks

För grafiska runbooks är extra loggning av utdata och meddelanden tillgängligt i form av spårning på aktivitets nivå. Det finns två nivåer av spårning: Basic och detaljerad. Med den grundläggande spårningen visas start-och slut tid för varje aktivitet i runbooken plus information som rör eventuella aktivitets försök. Några exempel är antalet försök och start tiden för aktiviteten. Detaljerad spårning innehåller grundläggande spårnings funktioner plus loggning av indata och utdata för varje aktivitet. 

Spårning på aktivitets nivå skriver poster med hjälp av utförlig data ström. Därför måste du Aktivera utförlig loggning när du aktiverar spårning. För grafiska runbooks med spårning aktive rad behöver du inte logga förlopps poster. Grundläggande spårning har samma syfte och är mer informativt.

![Grafiskt redigerings jobb för jobb visning](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Du kan se den avbildning som aktiverar utförlig loggning och spårning för grafiska runbooks som gör mycket mer information tillgänglig i vyn **jobb strömmar** för produktion. Denna extra information kan vara nödvändig för att felsöka produktions problem med en Runbook. 

Men om du inte behöver den här informationen för att följa förloppet för en Runbook för fel sökning kan du fortsätta att spåra inaktive ras som en allmän praxis. Spårnings posterna kan vara särskilt många. Med grafisk Runbook-spårning kan du hämta två till fyra poster per aktivitet, beroende på din konfiguration av Basic eller detaljerad spårning.

**Så här aktiverar du spårning på aktivitets nivå:**

1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .
3. På sidan Runbooks väljer du en grafisk Runbook från listan över Runbooks.
4. Under **Inställningar** klickar du på **loggning och spårning**.
5. På sidan loggning och spårning under **logg utförliga poster** klickar du på **på** för att aktivera utförlig loggning.
6. Under **spårning på aktivitets nivå** ändrar du spårnings nivån till **Basic** eller **detaljerad**, baserat på den spårnings nivå som du behöver.<br>

   ![Grafisk redigering av loggnings-och spårnings sida](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Hämta utdata och meddelanden för Runbook i Microsoft Azure övervaka loggar

Azure Automation kan skicka Runbook-jobbets status och jobb strömmar till arbets ytan Log Analytics. Azure Monitor stöder loggar som gör att du kan:

* Få insikt om dina automatiserings jobb.
* Utlös ett e-postmeddelande eller en avisering baserat på status för Runbook-jobb, till exempel misslyckad eller pausad.
* Skriv avancerade frågor över jobb strömmar.
* Korrelera jobb mellan Automation-konton.
* Visualisera jobb historik.

Mer information om hur du konfigurerar integrering med Azure Monitor loggar för att samla in, korrelera och agera på jobb data finns i [status för vidarebefordra jobb och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Runbooks finns [i hantera Runbooks i Azure Automation](manage-runbooks.md).
* Om du inte är bekant med PowerShell-skript kan du läsa mer i [PowerShell](/powershell/scripting/overview) -dokumentationen.
* Referens för Azure Automation PowerShell-cmdlet finns i [AZ. Automation](/powershell/module/az.automation).
