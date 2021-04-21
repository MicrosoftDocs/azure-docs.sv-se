---
title: Felsöka Azure Automation runbook-problem
description: Den här artikeln beskriver hur du felsöker och löser problem Azure Automation runbooks.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831230"
---
# <a name="troubleshoot-runbook-issues"></a>Felsöka runbook-problem

 I den här artikeln beskrivs runbook-problem som kan uppstå och hur du löser dem. Allmän information finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnostisera runbook-problem

När du får fel under runbook-körningen i Azure Automation kan du använda följande steg för att diagnostisera problemen:

1. Kontrollera att runbook-skriptet körs korrekt på den lokala datorn.

    Språkreferenser och inlärningsmoduler finns i [PowerShell Docs](/powershell/scripting/overview) eller [Python Docs.](https://docs.python.org/3/) Om du kör skriptet lokalt kan du identifiera och lösa vanliga fel, till exempel:

      * Moduler som saknas
      * Syntaxfel
      * Logikfel

1. Undersök [runbook-felströmmar](../automation-runbook-output-and-messages.md#runbook-output).

    Titta på dessa strömmar för specifika meddelanden och jämför dem med felen som dokumenteras i den här artikeln.

1. Se till att noderna och Automation-arbetsytan har de moduler som krävs.

    Om din runbook importerar några moduler kontrollerar du att de är tillgängliga för ditt Automation-konto med hjälp av stegen i [Importera moduler](../shared-resources/modules.md#import-modules). Uppdatera dina PowerShell-moduler till den senaste versionen genom att följa [anvisningarna i Uppdatera Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md). Mer felsökningsinformation finns i [Felsöka moduler.](shared-resources.md#modules)

1. Om runbooken pausas eller misslyckas oväntat:

    * [Förnya certifikatet om](../manage-runas-account.md#cert-renewal) Kör som-kontot har upphört att gälla.
    * [Förnya webhooken](../automation-webhooks.md#renew-a-webhook) om du försöker använda en utgången webhook för att starta runbooken.
    * [Kontrollera jobbstatusen](../automation-runbook-execution.md#job-statuses) för att fastställa aktuella runbook-statusar och några möjliga orsaker till problemet.
    * [Lägg till ytterligare utdata](../automation-runbook-output-and-messages.md#working-with-message-streams) i runbooken för att identifiera vad som händer innan runbooken pausas.
    * [Hantera eventuella undantag](../automation-runbook-execution.md#exceptions) som jobbet yftar med.

1. Gör det här steget om runbook-jobbet eller miljön på Hybrid Runbook Worker inte svarar.

    Om du kör dina runbooks på en Hybrid Runbook Worker i stället för i Azure Automation kan du behöva felsöka [själva Hybrid Worker.](hybrid-runbook-worker.md)

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenario: Runbook misslyckas med felet Ingen behörighet eller Förbjuden 403

### <a name="issue"></a>Problem

Din runbook misslyckas med felet Ingen behörighet eller Förbjuden 403, eller motsvarande.

### <a name="cause"></a>Orsak

Kör som-konton kanske inte har samma behörigheter mot Azure-resurser som ditt aktuella Automation-konto. 

### <a name="resolution"></a>Lösning

Kontrollera att Kör som-kontot har [behörighet att komma åt alla resurser som](../../role-based-access-control/role-assignments-portal.md) används i skriptet.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: Inloggningen till Azure-kontot misslyckades

### <a name="issue"></a>Problem

Du får något av följande fel när du arbetar med `Connect-AzAccount` cmdleten :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Orsak

De här felen inträffar om autentiseringstillgångens namn inte är giltigt. De kan också inträffa om användarnamnet och lösenordet som du använde för att konfigurera Automation-autentiseringstillgången inte är giltiga.

### <a name="resolution"></a>Lösning

Följ dessa steg för att avgöra vad som är fel:

1. Kontrollera att du inte har några specialtecken. Dessa tecken innehåller tecknet `\@` i automationens resursnamn för autentiseringsuppgifter som du använder för att ansluta till Azure.
1. Kontrollera om du kan använda användarnamnet och lösenordet som lagras i Azure Automation i din lokala PowerShell ISE-redigerare. Kör följande cmdlets i PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Om autentiseringen misslyckas lokalt har du inte ställt in dina autentiseringsuppgifter Azure Active Directory (Azure AD). Om du vill konfigurera Azure AD-kontot korrekt kan du läsa artikeln [Autentisera till Azure med hjälp av Azure Active Directory](../automation-use-azure-ad.md).

1. Om felet verkar vara tillfälligt kan du prova att lägga till logik för omförsök i autentiseringsrutinen för att göra autentiseringen mer robust.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: Kör Login-AzureRMAccount för att logga in

### <a name="issue"></a>Problem

Du får följande felmeddelande när du kör en Runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa när du inte använder ett Kör som-konto eller om Kör som-kontot har upphört att gälla. Mer information finns i Azure Automation [Översikt över Kör som-konton.](../automation-security-overview.md#run-as-accounts)

Det här felet har två primära orsaker:

* Det finns olika versioner av AzureRM- eller Az-modulen.
* Du försöker komma åt resurser i en separat prenumeration.

### <a name="resolution"></a>Lösning

Om du får det här felet när du har uppdaterat en AzureRM- eller Az-modul uppdaterar du alla moduler till samma version.

Om du försöker komma åt resurser i en annan prenumeration följer du dessa steg för att konfigurera behörigheter:

1. Gå till Automation Kör som-kontot och kopiera **Program-ID och** **Tumavtryck.**

    ![Kopiera program-ID och tumavtryck](../media/troubleshoot-runbooks/collect-app-id.png)

1. Gå till prenumerationens **åtkomstkontroll där** Automation-kontot inte *finns och* lägg till en ny rolltilldelning.

    ![Åtkomstkontroll](../media/troubleshoot-runbooks/access-control.png)

1. Lägg till **det program-ID som** samlades in tidigare. Välj **Deltagarbehörigheter.**

    ![Lägg till rolltilldelning](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopiera namnet på prenumerationen.

1. Du kan nu använda följande Runbook-kod för att testa behörigheterna från ditt Automation-konto till den andra prenumerationen. Ersätt `<CertificateThumbprint>` med värdet som kopierades i steg 1. Ersätt `"<SubscriptionName>"` med värdet som kopierades i steg 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: Det går inte att hitta Azure-prenumerationen

### <a name="issue"></a>Problem

Du får följande fel när du arbetar med `Select-AzureSubscription` `Select-AzureRMSubscription` `Select-AzSubscription` cmdleten , eller :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fel

Det här felet kan inträffa om:

* Prenumerationsnamnet är inte giltigt.
* Den Azure AD-användare som försöker hämta prenumerationsinformationen har inte konfigurerats som administratör för prenumerationen.
* Cmdleten är inte tillgänglig.

### <a name="resolution"></a>Lösning

Följ de här stegen för att avgöra om du har autentiserats för Azure och har åtkomst till prenumerationen som du försöker välja:

1. Kontrollera att skriptet fungerar fristående genom att testa det utanför Azure Automation.
1. Kontrollera att skriptet kör cmdleten [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) innan du kör `Select-*` cmdleten.
1. Lägg `Disable-AzContextAutosave -Scope Process` till i början av din runbook. Denna cmdlet säkerställer att alla autentiseringsuppgifter endast gäller för körningen av den aktuella runbooken.
1. Om du fortfarande ser felmeddelandet ändrar du koden genom att lägga till parametern för `AzContext` och kör sedan `Connect-AzAccount` koden.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks misslyckas när du hanterar flera prenumerationer

### <a name="issue"></a>Problem

När runbooks körs kan runbooken inte hantera Azure-resurser.

### <a name="cause"></a>Orsak

Runbooken använder inte rätt kontext när den körs. Detta kan beror på att runbooken av misstag försöker komma åt den felaktiga prenumerationen.

Du kan se fel som det här:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Lösning

Prenumerationskontexten kan gå förlorad när en runbook anropar flera runbooks. Följ riktlinjerna nedan för att undvika att försöka komma åt den felaktiga prenumerationen av misstag.

* För att undvika att referera till fel prenumeration inaktiverar du kontextbesparingen i dina Automation-runbooks med hjälp av följande kod i början av varje runbook.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* De Azure PowerShell cmdletarna stöder `-DefaultProfile` parametern . Detta har lagts till i alla Az- och AzureRm-cmdlets för att stödja körning av flera PowerShell-skript i samma process, så att du kan ange kontexten och vilken prenumeration som ska användas för varje cmdlet. Med dina runbooks bör du spara kontextobjektet i din runbook när runbooken skapas (det vill säga när ett konto loggar in) och varje gång det ändras och referera till kontexten när du anger en Az-cmdlet.

   > [!NOTE]
   > Du bör skicka ett kontextobjekt även när du manipulerar kontexten direkt med hjälp av cmdlets som [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) eller [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription).

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
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: Autentisering till Azure misslyckas eftersom multifaktorautentisering är aktiverat

### <a name="issue"></a>Problem

Du får följande fel när du autentiserar till Azure med ditt Användarnamn och lösenord för Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Orsak

Om du har multifaktorautentisering på ditt Azure-konto kan du inte använda en Azure Active Directory för att autentisera till Azure. I stället måste du använda ett certifikat eller ett tjänsthuvudnamn för att autentisera.

### <a name="resolution"></a>Lösning

Om du vill använda ett klassiskt Kör som-konto med cmdlets för den klassiska Azure-distributionsmodellen kan du se Skapa ett klassiskt [Kör som-konto för att hantera Azure-tjänster.](../automation-create-standalone-account.md#create-a-classic-run-as-account) Om du vill använda ett huvudnamn för Azure Resource Manager cmdlets kan du se Skapa tjänstens huvudnamn med [Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) och Autentisera ett huvudnamn för tjänsten [med Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenario: Runbook misslyckas med felmeddelandet "En uppgift avbröts"

### <a name="issue"></a>Problem

Din runbook misslyckas med ett fel som liknar följande exempel:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Orsak

Det här felet kan orsakas av att inaktuella Azure-moduler används.

### <a name="resolution"></a>Lösning

Du kan lösa det här felet genom att uppdatera dina Azure-moduler till den senaste versionen:

1. I ditt Automation-konto väljer **du Moduler** och sedan **Uppdatera Azure-moduler.**
1. Uppdateringen tar ungefär 15 minuter. När det är klart kör du runbooken som misslyckades igen.

Mer information om hur du uppdaterar moduler finns i [Uppdatera Azure-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: Termen känns inte igen som namnet på en cmdlet, funktion eller ett skript

### <a name="issue"></a>Problem

Din runbook misslyckas med ett fel som liknar följande exempel:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande skäl:

* Modulen som innehåller cmdleten importeras inte till Automation-kontot.
* Modulen som innehåller cmdleten importeras men är in date.

### <a name="resolution"></a>Lösning

Gör något av följande för att lösa det här felet:

* En Azure-modul finns i [Uppdatera Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md) för att lära dig hur du uppdaterar dina moduler i ditt Automation-konto.
* För en icke-Azure-modul kontrollerar du att modulen har importerats till ditt Automation-konto.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: Cmdleten misslyckas i PnP PowerShell-runbooken på Azure Automation

### <a name="issue"></a>Problem

När en runbook skriver ett PowerShell-genererat PnP-objekt till Azure Automation direkt kan cmdlet-utdata inte strömma tillbaka till Automation.

### <a name="cause"></a>Orsak

Det här problemet uppstår oftast när Azure Automation bearbetar runbooks som anropar PnP PowerShell-cmdlets, till exempel , utan att `add-pnplistitem` fånga returobjekten.

### <a name="resolution"></a>Lösning

Redigera dina skript för att tilldela returvärden till variabler så att cmdletarna inte försöker skriva hela objekt till standardutdata. Ett skript kan omdirigera utdataströmmen till en cmdlet, som du ser här.

```azurecli
  $null = add-pnplistitem
```

Om skriptet parsar cmdlet-utdata måste skriptet lagra utdata i en variabel och ändra variabeln i stället för att bara strömma utdata.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdleten känns inte igen när en runbook körs

### <a name="issue"></a>Problem

Runbook-jobbet misslyckas med felet:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när PowerShell-motorn inte kan hitta cmdleten som du använder i din runbook. Det är möjligt att modulen som innehåller cmdleten saknas i kontot, att det finns en namnkonflikt med ett runbook-namn eller att cmdleten även finns i en annan modul och Automation inte kan matcha namnet.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet:

* Kontrollera att du har angett cmdlet-namnet korrekt.
* Kontrollera att cmdleten finns i ditt Automation-konto och att det inte finns några konflikter. Kontrollera om cmdleten finns genom att öppna en runbook i redigeringsläge och söka efter den cmdlet som du vill hitta i biblioteket eller köra `Get-Command <CommandName>` . När du har verifierat att cmdleten är tillgänglig för kontot och att det inte finns några namnkonflikter med andra cmdlets eller runbooks lägger du till cmdleten på arbetsytan. Kontrollera att du använder en giltig parameter som angetts i din runbook.
* Om du har en namnkonflikt och cmdleten är tillgänglig i två olika moduler kan du lösa problemet med hjälp av det fullständigt kvalificerade namnet för cmdleten. Du kan till exempel använda `ModuleName\CmdletName`.
* Om du kör runbook lokalt i en Hybrid Worker-grupp ser du till att modulen och cmdleten är installerade på den dator som är värd för Hybrid Worker.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: Felaktig objektreferens vid anrop till Add-AzAccount

### <a name="issue"></a>Problem

Du får det här felet när du arbetar `Add-AzAccount` med , vilket är ett alias för `Connect-AzAccount` cmdleten:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa om runbooken inte gör rätt innan den anropar för `Add-AzAccount` att lägga till Automation-kontot. Ett exempel på ett av de nödvändiga stegen är att logga in med ett Kör som-konto. Rätt åtgärder att använda i din runbook finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: Objektreferensen är inte inställd på en instans av ett objekt

### <a name="issue"></a>Problem

Du får följande fel när du anropar en underordnad runbook med `Wait` parametern och utdataströmmen innehåller ett -objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Orsak

Om dataströmmen innehåller `Start-AzAutomationRunbook` objekt, hanterar inte utdataströmmen på rätt sätt.

### <a name="resolution"></a>Lösning

Implementera en avsökningslogik och använd cmdleten [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) för att hämta utdata. Ett exempel på den här logiken definieras här:

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook misslyckas på grund av deserialiserade objekt

### <a name="issue"></a>Problem

Runbooken misslyckas med felet:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Orsak

Om din runbook är ett PowerShell-arbetsflöde lagrar den komplexa objekt i ett deserialiserat format för att bevara runbook-tillståndet om arbetsflödet pausas.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet:

* Om du rör komplexa objekt från en cmdlet till en annan omsluter du dessa cmdlets i en `InlineScript` aktivitet.
* Skicka det namn eller värde som du behöver från det komplexa objektet i stället för att skicka hela objektet.
* Använd en PowerShell-runbook i stället för en PowerShell Workflow-runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: 400 – Felaktig begäran vid anrop av en webhook

### <a name="issue"></a>Problem

När du försöker anropa en webhook för en Azure Automation runbook får du följande felmeddelande:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Orsak

Den webhook som du försöker anropa är antingen inaktiverad eller har upphört att gälla. 

### <a name="resolution"></a>Lösning

Om webhooken är inaktiverad kan du återaktivera den via Azure Portal. Om webhooken har upphört att gälla måste du ta bort den och sedan skapa den på nytt. Du kan bara [förnya en webhook](../automation-webhooks.md#renew-a-webhook) om den inte redan har upphört att gälla. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: Begärandehastigheten är för närvarande för hög

### <a name="issue"></a>Problem

Du får följande felmeddelande när du kör `Get-AzAutomationJobOutput` cmdleten :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa när jobbutdata hämtas från en runbook som har [många utförliga strömmar](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Lösning

Gör något av följande för att lösa det här felet:

* Redigera runbooken och minska antalet jobbströmmar som den flöden.
* Minska antalet strömmar som ska hämtas när du kör cmdleten . Om du vill göra detta kan du ange värdet för parametern för `Stream` cmdleten [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) så att endast utdataströmmar hämtas. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenario: Runbook-jobbet misslyckas eftersom den tilldelade kvoten har överskridits

### <a name="issue"></a>Problem

Runbook-jobbet misslyckas med felet:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Orsak

Det här felet uppstår när jobbkörningen överskrider den kostnadsfria kvoten på 500 minuter för ditt konto. Den här kvoten gäller för alla typer av jobbkörningsuppgifter. Vissa av dessa uppgifter testar ett jobb, startar ett jobb från portalen, kör ett jobb med hjälp av webhooks eller schemalägger ett jobb som ska köras med antingen Azure Portal eller ditt datacenter. Mer information om priser för Automation finns i [Priser för Automation.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Lösning

Om du vill använda mer än 500 minuters bearbetning per månad ändrar du din prenumeration från den kostnadsfria nivån till basic-nivån:

1. Logga in på din Azure-prenumeration.
1. Välj det Automation-konto som ska uppgraderas.
1. Välj **Inställningar** och sedan **Prissättning.**
1. Välj **Aktivera** längst ned på sidan för att uppgradera ditt konto till Basic-nivån.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scenario: Runbook-utdataström som är större än 1 MB

### <a name="issue"></a>Problem

Din runbook som körs i Azure-sandbox-miljön misslyckas med följande fel:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Orsak

Det här felet beror på att runbooken försökte skriva för mycket undantagsdata till utdataströmmen.

### <a name="resolution"></a>Lösning

Det finns en gräns på 1 MB för jobbets utdataström. Se till att din runbook omsluter anrop till en körbar fil eller en delprocess med hjälp av `try` `catch` blocken och . Om åtgärderna kastar ett undantag ska koden skriva meddelandet från undantaget till en Automation-variabel. Den här tekniken förhindrar att meddelandet skrivs till jobbets utdataström. För Hybrid Runbook Worker jobb som körs visas utdataströmmen trunkerad till 1 MB utan felmeddelande.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: Runbook-jobbets startförsök gjordes tre gånger, men det går inte att starta varje gång

### <a name="issue"></a>Problem

Runbooken misslyckas med följande fel:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Orsak

Det här felet uppstår på grund av något av följande problem:

* **Minnesgräns.** Ett jobb kan misslyckas om det använder mer än 400 MB minne. De dokumenterade gränserna för minne som allokerats till en sandbox-miljö finns i [Automation-tjänstens gränser.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* **Nätverkssocketar.** Sandbox-miljöerna i Azure är begränsade till 1 000 samtidiga nätverkssocketar. Mer information finns i [Tjänstbegränsningar för Automation.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)

* **Modulkompatibel.** Modulberoenden kanske inte stämmer. I det här fallet returnerar din runbook vanligtvis ett `Command not found` - eller `Cannot bind parameter` -meddelande.

* **Ingen autentisering med Active Directory för sandbox-miljö.** Din runbook försökte anropa en körbar fil eller en delprocess som körs i en Azure-sandbox-miljö. Konfiguration av runbooks för autentisering med Azure AD med hjälp av Azure Active Directory Authentication Library (ADAL) stöds inte.

### <a name="resolution"></a>Lösning

* **Minnesgräns, nätverkssocketar.** Föreslagna sätt att arbeta inom minnesgränserna är att dela upp arbetsbelastningen mellan flera runbooks, bearbeta mindre data i minnet, undvika att skriva onödiga utdata från dina runbooks och fundera över hur många kontrollpunkter som skrivs till dina PowerShell-arbetsflödes-runbooks. Använd metoden clear, till exempel `$myVar.clear` , för att rensa variabler och använda för att köra `[GC]::Collect` skräpinsamling omedelbart. De här åtgärderna minskar runbookens minnesfotavtryck under körning.

* **Modulkompatibel.** Uppdatera dina Azure-moduler genom att följa stegen [i Uppdatera Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

* **Ingen autentisering med Active Directory för sandbox-miljö.** När du autentiserar till Azure AD med en runbook ser du till att Azure AD-modulen är tillgänglig i ditt Automation-konto. Se till att ge Kör som-kontot de behörigheter som krävs för att utföra de uppgifter som runbook automatiserar.

  Om din runbook inte kan anropa en körbar fil eller delprocess som körs i en Sandbox-miljö i Azure använder du runbooken på en [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers begränsas inte av de minnes- och nätverksgränser som azure-sandbox-miljöerna har.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-jobbet misslyckas med felmeddelandet "Det går inte att anropa metoden"

### <a name="issue"></a>Problem

Du får följande felmeddelande när du startar ett PowerShell-jobb i en runbook som körs i Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Orsak

Det här felet kan tyda på att runbooks som körs i en Azure-sandbox-miljö inte kan köras i [fullständigt språkläge.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Lösning

Det finns två sätt att lösa det här felet:

* I stället för [att använda Start-Job](/powershell/module/microsoft.powershell.core/start-job)använder [du Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta runbooken.
* Prova att köra runbooken på en Hybrid Runbook Worker.

Mer information om det här beteendet och andra beteenden för Azure Automation runbooks finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: En långvarig runbook kan inte slutföras

### <a name="issue"></a>Problem

Din runbook visas i tillståndet Stoppad efter att ha körts i tre timmar. Du kan också få det här felet:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Det här beteendet är enligt design i Sandbox-miljöerna i Azure [på](../automation-runbook-execution.md#fair-share) grund av övervakningen av tidsbegränsningar för processer i Azure Automation. Om en process körs längre än tre timmar stoppas en runbook automatiskt av tidsresursen. Statusen för en runbook som går längre än tidsgränsen för tidsbegränsade tid för tidsbegränsade delningar varierar beroende på runbooktyp. PowerShell- och Python-runbooks har statusen Stoppad. Runbook-flöden för PowerShell-arbetsflöden är inställda på Misslyckades.

### <a name="cause"></a>Orsak

Runbooken kördes över den tretimmarsgräns som tillåts av tidsbegränsade resurser i en Azure-sandbox-miljö.

### <a name="resolution"></a>Lösning

En rekommenderad lösning är att köra runbooken på en [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers begränsas inte av den runbook-gräns på tre timmar som azure-sandbox-miljöerna har. Runbooks som körs på Hybrid Runbook Workers bör utvecklas för att stödja omstartsbeteenden om det finns oväntade problem med lokal infrastruktur.

En annan lösning är att optimera runbooken genom att [skapa underordnade runbooks.](../automation-child-runbooks.md) Om din runbook loopar igenom samma funktion på flera resurser, till exempel i en databasåtgärd på flera databaser, kan du flytta funktionen till en underordnad runbook. Varje underordnad runbook körs parallellt i en separat process. Det här beteendet minskar den totala tiden som den överordnade runbooken ska slutföras.

PowerShell-cmdletarna som aktiverar det underordnade Runbook-scenariot är:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Med den här cmdleten kan du starta en runbook och skicka parametrar till runbooken.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Om det finns åtgärder som måste utföras när den underordnade runbooken har slutförts kan du med den här cmdleten kontrollera jobbstatusen för varje underordnad runbook.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: Fel i jobbströmmar om get_SerializationSettings metod

### <a name="issue"></a>Problem

Du ser följande fel i jobbströmmarna för en runbook:

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

Det här felet orsakas förmodligen av en ofullständig migrering från AzureRM till Az-moduler i din runbook. Den här situationen kan Azure Automation att starta ett runbook-jobb med hjälp av endast AzureRM-moduler och sedan starta ett annat jobb med hjälp av endast Az-moduler, vilket leder till en sandbox-krasch.

### <a name="resolution"></a>Lösning

Vi rekommenderar inte att du använder Az- och AzureRM-cmdlets i samma runbook. Mer information om korrekt användning av moduler finns i Migrera [till Az-moduler.](../shared-resources/modules.md#migrate-to-az-modules)

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenario: Åtkomst nekad när du använder Azure Sandbox för runbook eller program

### <a name="issue"></a>Problem

När din runbook eller ditt program försöker köra i en Sandbox-miljö i Azure nekar miljön åtkomst.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa eftersom Azure-sandbox-miljöerna förhindrar åtkomst till alla COM-servrar utanför processen. Ett sandbox-program eller en runbook kan till exempel inte anropa Windows Management Instrumentation (WMI) eller till Windows Installer -tjänsten (msiserver.exe).

### <a name="resolution"></a>Lösning

Mer information om hur du använder azure-sandbox-miljöer finns i [Runbook-körningsmiljö.](../automation-runbook-execution.md#runbook-execution-environment)

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenario: Ogiltig statuskod för förbjuden vid användning Key Vault inuti en runbook

### <a name="issue"></a>Problem

När du försöker komma Azure Key Vault via en Azure Automation runbook visas följande fel:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Orsak

Möjliga orsaker till det här problemet är:

* Använder inte ett Kör som-konto.
* Otillräcklig behörighet.

### <a name="resolution"></a>Lösning

#### <a name="not-using-a-run-as-account"></a>Inte använda ett Kör som-konto

Följ [steg 5 – Lägg till autentisering för](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) att hantera Azure-resurser för att säkerställa att du använder ett Kör som-konto för att få åtkomst Key Vault.

#### <a name="insufficient-permissions"></a>Otillräckliga behörigheter

[Lägg till behörigheter i Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) att ditt Kör som-konto har tillräcklig behörighet för att komma åt Key Vault.

## <a name="recommended-documents"></a>Rekommenderade dokument

* [Runbook-körning i Azure Automation](../automation-runbook-execution.md)
* [Starta en runbook i Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure konto för att förbättra kundupplevelsen. Azure Support dig till Azure-communityn för svar, support och experter.
* Om du behöver mer hjälp kan du skapa en azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
