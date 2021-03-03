---
title: Felsöka Azure Automation Runbook-problem
description: Den här artikeln beskriver hur du felsöker och löser problem med Azure Automation runbooks.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: 1ff5adf3ec974cc922d73cf5993a78722ca1b591
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723817"
---
# <a name="troubleshoot-runbook-issues"></a>Felsöka runbook-problem

 Den här artikeln beskriver Runbook-problem som kan uppstå och hur du kan lösa dem. Allmän information finns i [Run Book Execution i Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnostisera Runbook-problem

När du får fel meddelanden under Runbook-körningen i Azure Automation kan du använda följande steg för att diagnostisera problemen:

1. Se till att Runbook-skriptet körs korrekt på den lokala datorn.

    För språk referens-och inlärnings moduler, se [PowerShell-dokumenten](/powershell/scripting/overview) eller [python-dokumenten](https://docs.python.org/3/). Att köra skriptet lokalt kan identifiera och lösa vanliga fel, till exempel:

      * Moduler som saknas
      * Syntaxfel
      * Logic-fel

1. Undersök [fel strömmar](../automation-runbook-output-and-messages.md#runbook-output)för Runbook.

    Titta på dessa strömmar för vissa meddelanden och jämför dem med de fel som dokumenteras i den här artikeln.

1. Se till att noderna och automation-arbetsytan har de moduler som krävs.

    Om din Runbook importerar några moduler kontrollerar du att de är tillgängliga för ditt Automation-konto genom att följa stegen i [importera moduler](../shared-resources/modules.md#import-modules). Uppdatera dina PowerShell-moduler till den senaste versionen genom att följa anvisningarna i [Update Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md). Mer felsöknings information finns i [Felsöka moduler](shared-resources.md#modules).

1. Om din Runbook har pausats eller om oväntade fel uppstår:

    * [Förnya certifikatet](../manage-runas-account.md#cert-renewal) om kör som-kontot har upphört att gälla.
    * [Förnya webhooken](../automation-webhooks.md#renew-a-webhook) om du försöker använda en upphört webhook för att starta runbooken.
    * [Kontrol lera jobb status](../automation-runbook-execution.md#job-statuses) för att fastställa aktuella Runbook-statusar och vissa möjliga orsaker till problemet.
    * [Lägg till ytterligare utdata](../automation-runbook-output-and-messages.md#working-with-message-streams) till runbooken för att identifiera vad som händer innan runbooken pausas.
    * [Hantera eventuella undantag](../automation-runbook-execution.md#exceptions) som genereras av ditt jobb.

1. Gör det här steget om Runbook-jobbet eller miljön på Hybrid Runbook Worker inte svarar.

    Om du kör dina runbooks på en Hybrid Runbook Worker i stället för i Azure Automation kan du behöva [Felsöka själva hybrid Worker](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenario: Runbook Miss lyckas med fel meddelandet No permission eller förbjuden 403

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med en behörighet som inte är förbjuden eller har ett otillåtet 403-fel eller motsvarande.

### <a name="cause"></a>Orsak

Kör som-konton kanske inte har samma behörigheter för Azure-resurser som det aktuella Automation-kontot. 

### <a name="resolution"></a>Lösning

Se till att ditt kör som-konto har [åtkomst behörighet till alla resurser](../../role-based-access-control/role-assignments-portal.md) som används i skriptet.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: det gick inte att logga in på Azure-kontot

### <a name="issue"></a>Problem

Du får ett av följande fel när du arbetar med `Connect-AzAccount` cmdleten:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Orsak

Felen uppstår om inloggnings namnet är ogiltigt. De kan också inträffa om användar namnet och lösen ordet som du använde för att konfigurera inloggnings uppgifterna för Automation inte är giltiga.

### <a name="resolution"></a>Lösning

Följ dessa steg för att fastställa vad som är fel:

1. Kontrol lera att du inte har några specialtecken. De här tecknen innehåller det `\@` tecken i inloggnings namnet för Automation-autentiseringsuppgifter som du använder för att ansluta till Azure.
1. Kontrol lera om du kan använda det användar namn och lösen ord som lagras i Azure Automation autentiseringsuppgiften i din lokala PowerShell ISE-redigerare. Kör följande cmdlets i PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Om autentiseringen Miss lyckas lokalt har du inte konfigurerat dina Azure Active Directory (Azure AD)-autentiseringsuppgifterna korrekt. Om du vill få Azure AD-kontot konfigurerat korrekt läser du artikeln [autentisera till Azure med hjälp av Azure Active Directory](../automation-use-azure-ad.md).

1. Om felet verkar vara tillfälligt kan du försöka lägga till logik för återförsök för autentisering för att göra autentiseringen stabilare.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: kör Login-AzureRMAccount för att logga in

### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör en Runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa om du inte använder ett Kör som-konto eller om kör som-kontot har upphört att gälla. Mer information finns i [Översikt över Azure Automation kör som-konton](../automation-security-overview.md#run-as-accounts).

Det här felet har två primära orsaker:

* Det finns olika versioner av AzureRM-eller AZ-modulen.
* Du försöker komma åt resurser i en separat prenumeration.

### <a name="resolution"></a>Lösning

Om du får det här felet när du har uppdaterat en AzureRM-eller AZ-modul uppdaterar du alla moduler till samma version.

Om du försöker komma åt resurser i en annan prenumeration följer du dessa steg för att konfigurera behörigheter:

1. Gå till kör som-kontot för Automation och kopiera **program-ID** och **tumavtryck**.

    ![Kopiera program-ID och tumavtryck](../media/troubleshoot-runbooks/collect-app-id.png)

1. Gå till prenumerationens **åtkomst kontroll** där Automation-kontot *inte* finns och Lägg till en ny roll tilldelning.

    ![Åtkomstkontroll](../media/troubleshoot-runbooks/access-control.png)

1. Lägg till det **program-ID** som samlats in tidigare. Välj **deltagar** behörighet.

    ![Lägg till rolltilldelning](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopiera namnet på prenumerationen.

1. Du kan nu använda följande Runbook-kod för att testa behörigheterna från ditt Automation-konto till den andra prenumerationen. Ersätt `<CertificateThumbprint>` med värdet som kopieras i steg 1. Ersätt `"<SubscriptionName>"` med värdet som kopieras i steg 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: det går inte att hitta Azure-prenumerationen

### <a name="issue"></a>Problem

Du får följande fel meddelande när du arbetar med `Select-AzureSubscription` `Select-AzureRMSubscription` cmdleten, eller `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fel

Det här felet kan inträffa om:

* Prenumerations namnet är inte giltigt.
* Den Azure AD-användare som försöker hämta prenumerations informationen har inte kon figurer ATS som administratör för prenumerationen.
* Cmdleten är inte tillgänglig.

### <a name="resolution"></a>Lösning

Följ dessa steg för att avgöra om du har autentiserat till Azure och har åtkomst till den prenumeration som du försöker välja:

1. Kontrol lera att skriptet fungerar fristående genom att testa det utanför Azure Automation.
1. Kontrol lera att skriptet kör cmdleten [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) innan du kör `Select-*` cmdleten.
1. Lägg till `Disable-AzContextAutosave –Scope Process` i början av din Runbook. Den här cmdleten säkerställer att alla autentiseringsuppgifter endast gäller för körningen av den aktuella runbooken.
1. Om fel meddelandet fortfarande visas ändrar du koden genom att lägga till `AzContext` parametern för `Connect-AzAccount` och sedan köra koden.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks fungerar inte vid hantering av flera prenumerationer

### <a name="issue"></a>Problem

När Runbooks körs kan inte Runbook Hantera Azure-resurser.

### <a name="cause"></a>Orsak

Runbooken använder inte rätt kontext vid körning. Detta kan bero på att runbooken av misstag försöker komma åt den felaktiga prenumerationen.

Du kan se fel som den här:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Lösning

Prenumerations kontexten kan gå förlorad när en Runbook anropar flera Runbooks. För att undvika oavsiktlig försök att komma åt den felaktiga prenumerationen bör du följa anvisningarna nedan.

* Undvik att referera till fel prenumeration genom att inaktivera kontext sparande i dina Automation-runbooks med hjälp av följande kod i början av varje Runbook.

   ```azurepowershell-interactive
   Disable-AzContextAutosave –Scope Process
   ```

* Azure PowerShell-cmdlet: arna stöder `-DefaultProfile` parametern. Detta lades till i alla AZ-och AzureRm-cmdletar som stöd för körning av flera PowerShell-skript i samma process, så att du kan ange kontexten och vilken prenumeration som ska användas för varje cmdlet. Med dina runbooks bör du spara kontext objekt i din Runbook när Runbook skapas (det vill säga när ett konto loggar in) och varje gång det ändras och referera till kontexten när du anger en AZ-cmdlet.

   > [!NOTE]
   > Du bör skicka ett kontext objekt även när du ändrar kontexten direkt med hjälp av cmdletar som [set-AzContext](/powershell/module/az.accounts/Set-AzContext) eller [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription).

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: autentisering till Azure Miss lyckas eftersom multifaktorautentisering är aktiverat

### <a name="issue"></a>Problem

Du får följande fel när du autentiserar till Azure med ditt användar namn och lösen ord för Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Orsak

Om du har multifaktorautentisering på ditt Azure-konto kan du inte använda en Azure Active Directory användare för att autentisera till Azure. I stället måste du använda ett certifikat eller ett huvud namn för tjänsten för att autentisera.

### <a name="resolution"></a>Lösning

Om du vill använda ett klassiskt kör som-konto med Azures klassiska cmdlets för distributions modell, se [skapa ett klassiskt kör som-konto för att hantera Azure-tjänster](../automation-create-standalone-account.md#create-a-classic-run-as-account). Information om hur du använder ett huvud namn för tjänsten med Azure Resource Manager cmdlets finns i [skapa tjänstens huvud namn med Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) och [autentisera ett huvud namn för tjänsten med Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenario: Runbook Miss lyckas med fel meddelandet "en uppgift avbröts"

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Orsak

Det här felet kan orsakas av att inaktuella Azure-moduler används.

### <a name="resolution"></a>Lösning

Du kan lösa det här felet genom att uppdatera Azure-moduler till den senaste versionen:

1. Välj **moduler** i ditt Automation-konto och välj sedan **Uppdatera Azure-moduler**.
1. Uppdateringen tar ungefär 15 minuter. Kör den Runbook som misslyckades när den är klar.

Mer information om hur du uppdaterar dina moduler finns [i Uppdatera Azure-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: termen känns inte igen som namnet på en cmdlet, funktion eller skript

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med ett fel som liknar följande exempel:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande orsaker:

* Modulen som innehåller cmdleten importeras inte till Automation-kontot.
* Modulen som innehåller cmdleten importeras men är inaktuell.

### <a name="resolution"></a>Lösning

Utför en av följande åtgärder för att lösa det här felet:

* En Azure-modul finns i [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md) för att lära dig hur du uppdaterar dina moduler i ditt Automation-konto.
* För en icke-Azure-modul kontrollerar du att modulen har importer ATS till ditt Automation-konto.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: cmdleten Miss lyckas i PnP PowerShell-Runbook på Azure Automation

### <a name="issue"></a>Problem

När en Runbook skriver ett PnP-genererat objekt till Azure Automation utdata direkt, kan inte cmdlet-utdata strömma tillbaka till Automation.

### <a name="cause"></a>Orsak

Det här problemet uppstår vanligt vis när Azure Automation bearbetar Runbooks som anropar PnP PowerShell-cmdlets, till exempel `add-pnplistitem` , utan att fånga in retur objekt.

### <a name="resolution"></a>Lösning

Redigera skripten för att tilldela eventuella retur värden till variabler så att-cmdletarna inte försöker skriva hela objekt till standardutdata. Ett skript kan omdirigera utdataströmmen till en cmdlet, som visas här.

```azurecli
  $null = add-pnplistitem
```

Om skriptet parsar utdata för cmdleten måste skriptet lagra utdata i en variabel och manipulera variabeln i stället för att helt enkelt strömma utdata.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: cmdleten känns inte igen när en Runbook kördes

### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Orsak

Det här felet beror på att PowerShell-motorn inte kan hitta den cmdlet som du använder i din Runbook. Det är möjligt att modulen som innehåller cmdleten saknas från kontot. det finns en namn konflikt med ett Runbook-namn, eller så finns cmdleten även i en annan modul och automation kan inte matcha namnet.

### <a name="resolution"></a>Lösning

Åtgärda problemet med någon av följande lösningar:

* Kontrol lera att du har angett rätt cmdlet-namn.
* Se till att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Du kan kontrol lera om cmdleten finns genom att öppna en Runbook i redigerings läge och söka efter den cmdlet som du vill söka efter i biblioteket eller köra `Get-Command <CommandName>` . När du har verifierat att cmdleten är tillgänglig för kontot och att det inte finns något namn i konflikt med andra cmdletar eller Runbooks, lägger du till cmdleten på arbets ytan. Kontrol lera att du använder en giltig parameter uppsättning i din Runbook.
* Om du har en namn konflikt och cmdleten är tillgänglig i två olika moduler, löser du problemet genom att använda det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda `ModuleName\CmdletName`.
* Om du kör en Runbook lokalt i en hybrid Worker-grupp kontrollerar du att modulen och cmdleten är installerade på den dator som är värd för Hybrid Worker.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: felaktig objekt referens för anrop till Add-AzAccount

### <a name="issue"></a>Problem

Du får det här felet när du arbetar med `Add-AzAccount` , vilket är ett alias för `Connect-AzAccount` cmdleten:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa om runbooken inte gör rätt steg innan du anropar `Add-AzAccount` för att lägga till Automation-kontot. Ett exempel på en av de nödvändiga stegen är att logga in med ett Kör som-konto. Information om vilka åtgärder som ska användas i din Runbook finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: objekt referensen har inte angetts till en instans av ett objekt

### <a name="issue"></a>Problem

Du får följande fel meddelande när du anropar en underordnad Runbook med `Wait` parametern och utdataströmmen innehåller ett objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

Om data strömmen innehåller objekt, `Start-AzAutomationRunbook` hanterar inte utdataströmmen korrekt.

### <a name="resolution"></a>Lösning

Implementera en avsöknings logik och Använd cmdleten [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) för att hämta utdata. Ett exempel på den här logiken definieras här:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook Miss lyckas på grund av ett avserialiserat objekt

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med felet:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Orsak

Om din Runbook är ett PowerShell-arbetsflöde lagrar den komplexa objekt i ett avserialiserat format för att bevara ditt Runbook-tillstånd om arbets flödet har pausats.

### <a name="resolution"></a>Lösning

Åtgärda problemet med någon av följande lösningar:

* Om du flyttar komplexa objekt från en cmdlet till en annan kan du omsluta dessa cmdletar i en `InlineScript` aktivitet.
* Skicka det namn eller värde som du behöver från det komplexa objektet i stället för att skicka hela objektet.
* Använd en PowerShell-Runbook i stället för en PowerShell Workflow-Runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: 400 felaktig status för begäran vid anrop till en webhook

### <a name="issue"></a>Problem

När du försöker anropa en webhook för en Azure Automation Runbook får du följande fel meddelande:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Orsak

Webhooken som du försöker anropa är antingen inaktive rad eller har upphört att gälla. 

### <a name="resolution"></a>Lösning

Om webhooken är inaktive rad kan du återaktivera den via Azure Portal. Om webhooken har upphört att gälla måste du ta bort och sedan återskapa den. Du kan bara [förnya en webhook](../automation-webhooks.md#renew-a-webhook) om den inte redan har gått ut. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: den begärda frekvensen är för stor

### <a name="issue"></a>Problem

Du får följande fel meddelande när du kör `Get-AzAutomationJobOutput` cmdleten:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Orsak

Felet kan uppstå när jobbets utdata hämtas från en Runbook som har många [utförliga data strömmar](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Lösning

Gör något av följande för att lösa det här felet:

* Redigera runbooken och minska antalet jobb strömmar som den avger.
* Minska antalet strömmar som ska hämtas när cmdleten körs. Om du vill göra detta kan du ange värdet för `Stream` parametern för [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) -cmdlet: en så att endast utgående strömmar hämtas. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenario: ett Runbook-jobb Miss lyckas eftersom den tilldelade kvoten överskreds

### <a name="issue"></a>Problem

Ditt Runbook-jobb Miss lyckas med felet:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Orsak

Felet uppstår när jobb körningen överskrider den kostnads fria kvoten på 500 minuter för ditt konto. Den här kvoten gäller för alla typer av jobb körnings aktiviteter. Några av dessa uppgifter testar ett jobb, startar ett jobb från portalen, kör ett jobb genom att använda Webhooks eller schemalägga ett jobb som ska köras med hjälp av antingen Azure Portal eller ditt data Center. Läs mer om prissättning för Automation i avsnittet om [automatiserings priser](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuters bearbetning per månad ändrar du din prenumeration från den kostnads fria nivån till Basic-nivån:

1. Logga in på din Azure-prenumeration.
1. Välj det Automation-konto som ska uppgraderas.
1. Välj **Inställningar** och välj sedan **prissättning**.
1. Välj **Aktivera** på sidan längst ned för att uppgradera ditt konto till Basic-nivån.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scenario: Runbook-utdataström som är större än 1 MB

### <a name="issue"></a>Problem

Din Runbook som körs i Azure sandbox Miss lyckas med följande fel:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Orsak

Det här felet beror på att runbooken försökte skriva för mycket undantags data till utdataströmmen.

### <a name="resolution"></a>Lösning

Det finns en gräns på 1 MB för jobbets utgående data ström. Se till att din Runbook avstår anrop till en körbar fil eller under process genom att använda `try` och `catch` blockera. Om åtgärderna genererar ett undantag ska koden skriva meddelandet från undantaget till en Automation-variabel. Den här tekniken förhindrar att meddelandet skrivs in i utdataströmmen för jobb. För att Hybrid Runbook Worker jobb körs, trunkeras den utgående strömmen till 1 MB utan fel meddelande.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: Runbook-jobbets start försök tre gånger, men kunde inte startas varje gång

### <a name="issue"></a>Problem

Din Runbook Miss lyckas med följande fel:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Orsak

Felet beror på ett av följande problem:

* **Minnes gräns.** Ett jobb kan Miss förorsakat om det använder mer än 400 MB minne. De dokumenterade gränserna för minne som allokerats till en sandbox finns i [begränsningar för Automation-tjänster](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* **Nätverks platser.** Azure-sand lådor är begränsade till 1 000 samtidiga nätverks platser. Mer information finns i [begränsningar för Automation-tjänster](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Modulen är inkompatibel.** Beroenden för modulen kan vara felaktiga. I det här fallet returnerar din Runbook vanligt vis ett `Command not found` eller `Cannot bind parameter` meddelande.

* **Ingen autentisering med Active Directory för sandbox.** Din Runbook försökte anropa en körbar fil eller under process som körs i en Azure-sandbox. Det finns inte stöd för att konfigurera Runbooks för autentisering med Azure AD med hjälp av Azure Active Directory Authentication Library (ADAL).

### <a name="resolution"></a>Lösning

* **Minnes gräns, nätverks platser.** Föreslagna sätt att arbeta inom minnes gränserna är att dela arbets belastningen mellan flera Runbooks, bearbeta mindre data i minnet, undvika att skriva onödiga utdata från dina runbooks och fundera över hur många kontroll punkter som skrivs in i PowerShell Workflow-Runbooks. Använd Clear-metoden, till exempel `$myVar.clear` , för att rensa variabler och använda `[GC]::Collect` för att köra skräp insamling direkt. De här åtgärderna minskar din Runbooks minnes storlek under körning.

* **Modulen är inkompatibel.** Uppdatera dina Azure-moduler genom att följa stegen i [så här uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

* **Ingen autentisering med Active Directory för sandbox.** När du autentiserar till Azure AD med en Runbook kontrollerar du att Azure AD-modulen är tillgänglig i ditt Automation-konto. Se till att ge kör som-kontot de behörigheter som krävs för att utföra de uppgifter som Runbook automatiserar.

  Om din Runbook inte kan anropa en körbar fil eller under process som körs i en Azure-sandbox, använder du runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker begränsas inte av de minnes-och nätverks gränser som Azure-sandbox har.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-jobbet Miss lyckas med fel meddelandet "det går inte att anropa metoden"

### <a name="issue"></a>Problem

Du får följande fel meddelande när du startar ett PowerShell-jobb i en Runbook som körs i Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Orsak

Det här felet kan betyda att Runbooks som körs i ett Azure-sandbox inte kan köras i det [fullständiga språk läget](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet:

* I stället för att använda [Start-Job](/powershell/module/microsoft.powershell.core/start-job)använder du [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta runbooken.
* Försök att köra runbooken på en Hybrid Runbook Worker.

Mer information om det här beteendet och andra beteenden för Azure Automation runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: en tids krävande Runbook slutförs inte

### <a name="issue"></a>Problem

Din Runbook visas i stoppat tillstånd efter att ha körts i tre timmar. Du kan också få det här felet:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Det här beteendet är avsiktligt i Azure-sand lådor på grund av [rättvis resurs](../automation-runbook-execution.md#fair-share) övervakning av processer inom Azure Automation. Om en process körs längre än tre timmar stoppar den verkliga delningen en Runbook automatiskt. Status för en Runbook som går förbi den verkliga delnings tiden skiljer sig åt efter Runbook-typ. PowerShell-och python-Runbooks anges till stoppad status. PowerShell Workflow-Runbooks är inställda på misslyckade.

### <a name="cause"></a>Orsak

Runbooken kördes över den gräns på tre timmar som tillåts av rättvis resurs i en Azure-sandbox.

### <a name="resolution"></a>Lösning

En rekommenderad lösning är att köra runbooken på en [hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker begränsas inte av den tre timmars verkliga aktie Runbook-gränsen som Azure-sandbox har. Runbooks som körs på Hybrid Runbook Worker bör utvecklas för att stödja omstarts beteenden om det uppstår oväntade problem med lokal infrastruktur.

En annan lösning är att optimera runbooken genom att skapa [underordnade Runbooks](../automation-child-runbooks.md). Om din Runbook upprepas genom samma funktion på flera resurser, till exempel i en databas åtgärd på flera databaser, kan du flytta funktionen till en underordnad Runbook. Varje underordnad runbook körs parallellt i en separat process. Det här beteendet minskar den totala tiden som den överordnade runbooken slutförs.

PowerShell-cmdletar som aktiverar det underordnade Runbook-scenariot är:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Med den här cmdleten kan du starta en runbook och skicka parametrar till runbooken.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Om det finns åtgärder som måste utföras efter att den underordnade Runbook-flödet har slutförts kan du kontrol lera jobb status för varje underordnad i den här cmdleten.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: fel i jobb strömmar om get_SerializationSettings metoden

### <a name="issue"></a>Problem

Följande fel visas i jobb strömmar för en Runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Orsak

Det här felet beror förmodligen på en ofullständig migrering från AzureRM till AZ-moduler i din Runbook. Den här situationen kan orsaka att Azure Automation startar ett Runbook-jobb genom att endast använda AzureRM-moduler och sedan starta ett annat jobb med hjälp av AZ-moduler som leder till en sand Box krasch.

### <a name="resolution"></a>Lösning

Vi rekommenderar inte att du använder AZ-och AzureRM-cmdletar i samma Runbook. Mer information om hur du använder modulerna korrekt finns i [migrera till AZ-moduler](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenario: åtkomst nekas när Azure-sandbox används för Runbook eller program

### <a name="issue"></a>Problem

När din Runbook eller ditt program försöker köras i ett Azure-sandbox nekar-miljön åtkomst.

### <a name="cause"></a>Orsak

Det här problemet kan uppstå eftersom Azure-sand lådor förhindrar åtkomst till alla out-of-process COM-servrar. Ett begränsat program eller en Runbook kan till exempel inte anropa till Windows Management Instrumentation (WMI) eller till Windows Installer-tjänsten (msiserver.exe).

### <a name="resolution"></a>Lösning

Mer information om hur du använder Azure-sand lådor finns i [Runbook Execution Environment](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenario: ogiltig status kod för förbjuden användning av Key Vault i en Runbook

### <a name="issue"></a>Problem

När du försöker komma åt Azure Key Vault via en Azure Automation Runbook får du följande fel meddelande:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Orsak

Möjliga orsaker till det här problemet är:

* Använder inget kör som-konto.
* Otillräcklig behörighet.

### <a name="resolution"></a>Lösning

#### <a name="not-using-a-run-as-account"></a>Inte använda ett Kör som-konto

Följ [steg 5 – Lägg till autentisering för att hantera Azure-resurser](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) för att se till att du använder ett Kör som-konto för att få åtkomst till Key Vault.

#### <a name="insufficient-permissions"></a>Otillräckliga behörigheter

[Lägg till behörigheter till Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) för att säkerställa att ditt kör som-konto har tillräcklig behörighet för att komma åt Key Vault.

## <a name="recommended-documents"></a>Rekommenderade dokument

* [Runbook-körning i Azure Automation](../automation-runbook-execution.md)
* [Starta en Runbook i Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter dig till Azure-communityn för svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
