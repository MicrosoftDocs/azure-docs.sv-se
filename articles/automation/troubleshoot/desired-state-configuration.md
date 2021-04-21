---
title: Felsöka Azure Automation State Configuration problem
description: Den här artikeln beskriver hur du felsöker och Azure Automation State Configuration problem.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91c4780981851b62027fecf18da2c3b78625f272
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831212"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Felsöka Azure Automation State Configuration problem

Den här artikeln innehåller information om felsökning och lösning av problem som uppstår när du kompilerar eller distribuerar konfigurationer i Azure Automation State Configuration. Allmän information om State Configuration finns i [Azure Automation State Configuration översikt.](../automation-dsc-overview.md)

## <a name="diagnose-an-issue"></a>Diagnostisera ett problem

När du får ett kompilerings- eller distributionsfel för konfigurationen kan du diagnostisera problemet med hjälp av följande steg.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Kontrollera att konfigurationen kompileras korrekt på den lokala datorn

Azure Automation State Configuration bygger på PowerShell Desired State Configuration (DSC). Du hittar dokumentationen för DSC-språket och -syntaxen i [PowerShell DSC Docs](/powershell/scripting/overview).

Genom att kompilera en DSC-konfiguration på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - Moduler som saknas.
   - Syntaxfel.
   - Logikfel.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visa DSC-loggar på noden

Om konfigurationen kompileras korrekt, men misslyckas när den tillämpas på en nod, hittar du detaljerad information i DSC-loggarna. Information om var du hittar loggarna finns i [Var finns DSC-händelseloggarna?](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)

Modulen [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) kan hjälpa dig att parsa detaljerad information från DSC-loggarna. Om du kontaktar supporten behöver de dessa loggar för att diagnostisera problemet.

Du kan installera modulen `xDscDiagnostics` på den lokala datorn genom att följa anvisningarna i Installera modulen stabil [version.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Om du vill `xDscDiagnostics` installera modulen på azure-datorn använder [du Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). Du kan också använda **kommandoalternativet Kör** i Azure Portal genom att följa stegen i Köra PowerShell-skript på din virtuella [Windows-dator med Kör kommando](../../virtual-machines/windows/run-command.md).

Information om hur du **använder xDscDiagnostics finns** i [Använda xDscDiagnostics för att analysera DSC-loggar](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Se även [xDscDiagnostics-cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Kontrollera att noderna och Automation-arbetsytan har nödvändiga moduler

DSC är beroende av moduler som är installerade på noden. När du använder Azure Automation State Configuration importerar du alla nödvändiga moduler till ditt Automation-konto genom att följa stegen i [Importera moduler.](../shared-resources/modules.md#import-modules) Konfigurationer kan också vara beroende av specifika versioner av moduler. Mer information finns i [Felsöka moduler.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: En konfiguration med specialtecken kan inte tas bort från portalen

### <a name="issue"></a>Problem

När du försöker ta bort en DSC-konfiguration från portalen visas följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt problem som ska lösas.

### <a name="resolution"></a>Lösning

Använd [cmdleten Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) för att ta bort konfigurationen.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Det gick inte att registrera DSC-agenten

### <a name="issue"></a>Problem

När [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) eller en annan DSC-cmdlet visas felet:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Orsak

Det här felet orsakas vanligtvis av en brandvägg, att datorn ligger bakom en proxyserver eller andra nätverksfel.

### <a name="resolution"></a>Lösning

Kontrollera att datorn har åtkomst till rätt slutpunkter för DSC och försök igen. En lista över portar och adresser som behövs finns i [Nätverksplanering.](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporter returnerar svarskoden Unauthorized (Obehörig)

### <a name="issue"></a>Problem

När du registrerar en nod Azure Automation State Configuration får du något av följande felmeddelanden:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Orsak

Det här problemet orsakas av ett felaktigt eller utgånget certifikat. Se [Registrera om en nod.](../automation-dsc-onboarding.md#re-register-a-node)

Det här problemet kan också bero på att proxykonfigurationen inte tillåter åtkomst till ***.azure-automation.net**. Mer information finns i [Konfiguration av privata nätverk.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Lösning

Använd följande steg för att registrera om den misslyckade DSC-noden.

#### <a name="step-1-unregister-the-node"></a>Steg 1: Avregistrera noden

1. I den Azure Portal går du till **Home**  >  **Automation Accounts** > (ditt Automation-konto) > State Configuration **(DSC).**
1. Välj **Noder** och välj den nod som har problem.
1. Välj **Avregistrera för** att avregistrera noden.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Steg 2: Avinstallera DSC-tillägget från noden

1. I den Azure Portal går du till **Start virtuell**  >  **dator >** (nod misslyckas) > **Tillägg**.
1. Välj **Microsoft.Powershell.DSC**, PowerShell DSC-tillägget.
1. Välj **Avinstallera** för att avinstallera tillägget.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Steg 3: Ta bort alla skadade eller utgångna certifikat från noden

Kör följande kommandon på noden som misslyckas från en upphöjd PowerShell-kommandotolk:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Steg 4: Registrera om noden som misslyckas

1. I den Azure Portal går du till **Home**  >  **Automation Accounts** > (ditt Automation-konto) > State Configuration **(DSC).**
1. Välj **Noder**.
1. Välj **Lägg till**.
1. Välj noden som misslyckas.
1. Välj **Anslut** och välj önskade alternativ.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Noden har statusen "Hittades inte"

### <a name="issue"></a>Problem

Noden har en rapport med statusen Misslyckades och innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Orsak

Det här felet inträffar vanligtvis när noden har tilldelats ett konfigurationsnamn, till exempel **ABC**, i stället för ett namn på en nodkonfiguration (MOF-fil), till **exempel ABC. WebServer**.

### <a name="resolution"></a>Lösning

* Se till att du tilldelar noden med nodens konfigurationsnamn och inte konfigurationsnamnet.
* Du kan tilldela en nodkonfiguration till en nod med hjälp Azure Portal eller med en PowerShell-cmdlet.

  * I den Azure Portal går du till **Home**  >  **Automation-konton** > (ditt Automation-konto) > **State Configuration (DSC).** Välj sedan en nod och välj **Tilldela nodkonfiguration**.
  * Använd [cmdleten Set-AzAutomationDscNode.](/powershell/module/Az.Automation/Set-AzAutomationDscNode)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) produceras när en konfiguration kompilerades

### <a name="issue"></a>Problem

DSC-kompileringsjobbet pausas med felet:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Orsak

När uttrycket efter `Node` nyckelordet i DSC-konfigurationen utvärderas till `$null` skapas inga nodkonfigurationer.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet:

* Kontrollera att uttrycket bredvid nyckelordet `Node` i konfigurationsdefinitionen inte utvärderar till Null.
* Om du använder [ConfigurationData när](../automation-dsc-compile.md) du kompilerar konfigurationen ska du se till att skicka de värden som konfigurationen förväntar sig från konfigurationsdata.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: DSC-nodrapporten fastnar i pågående tillstånd

### <a name="issue"></a>Problem

DSC-agenten matar ut:

```error
No instance found with given property values
```

### <a name="cause"></a>Orsak

Du har uppgraderat din version Windows Management Framework (WMF) och har skadat Windows Management Instrumentation (WMI).

### <a name="resolution"></a>Lösning

Följ anvisningarna i [Kända problem och begränsningar i DSC.](/powershell/scripting/wmf/known-issues/known-issues-dsc)

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Det går inte att använda autentiseringsuppgifter i en DSC-konfiguration

### <a name="issue"></a>Problem

DSC-kompileringsjobbet pausas med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Orsak

Du har använt en autentiseringsidentifiering i en konfiguration men inte angett rätt till sant `ConfigurationData` `PSDscAllowPlainTextPassword` för varje nodkonfiguration.

### <a name="resolution"></a>Lösning

Se till att skicka in korrekt `ConfigurationData` för att ange till true för varje `PSDscAllowPlainTextPassword` nodkonfiguration som nämns i konfigurationen. Se [Kompilera DSC-konfigurationer i Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: Fel vid bearbetning av tillägg vid aktivering av en dator från ett DSC-tillägg

### <a name="issue"></a>Problem

När du aktiverar en dator med hjälp av ett DSC-tillägg inträffar ett fel som innehåller felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Orsak

Det här felet uppstår vanligtvis när noden tilldelas ett nodkonfigurationsnamn som inte finns i tjänsten.

### <a name="resolution"></a>Lösning

* Se till att du tilldelar noden ett namn som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera nodkonfigurationsnamnet, vilket resulterar i att noden aktiverar men inte tilldelar en nodkonfiguration.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenario: "Ett eller flera fel inträffade" vid registrering av en nod med hjälp av PowerShell

### <a name="issue"></a>Problem

När du registrerar en nod med [hjälp av Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) eller [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)får du följande fel:

```error
One or more errors occurred.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker registrera en nod i en annan prenumeration än den som används av Automation-kontot.

### <a name="resolution"></a>Lösning

Hantera noden mellan prenumerationer som om den har definierats för ett separat moln eller lokalt. Registrera noden med något av följande alternativ för att aktivera datorer:

* Windows: [Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenario: Felmeddelandet "Etableringen misslyckades"

### <a name="issue"></a>Problem

När du registrerar en nod visas felet:

```error
Provisioning has failed
```

### <a name="cause"></a>Orsak

Det här meddelandet visas när det är problem med anslutningen mellan noden och Azure.

### <a name="resolution"></a>Lösning

Kontrollera om noden finns i ett virtuellt privat nätverk (VPN) eller har andra problem med att ansluta till Azure. Se [Felsöka problem med funktionsdistribution.](onboarding.md)

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Fel med ett allmänt fel vid tillämpning av en konfiguration i Linux

### <a name="issue"></a>Problem

När du tillämpar en konfiguration i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Orsak

Om **/tmp-platsen** är inställd på `noexec` kan inte den aktuella versionen av DSC tillämpa konfigurationer.

### <a name="resolution"></a>Lösning

Ta bort `noexec` alternativet från **/tmp-platsen.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Nodkonfigurationsnamn som överlappar kan resultera i en felaktig version

### <a name="issue"></a>Problem

När du använder ett enda konfigurationsskript för att generera flera nodkonfigurationer och vissa nodkonfigurationsnamn är delmängder av andra namn kan kompileringstjänsten till slut tilldela fel konfiguration. Det här problemet uppstår bara när du använder ett enda skript för att generera konfigurationer med konfigurationsdata per nod och endast när namnet överlappar i början av strängen. Ett exempel är ett enda konfigurationsskript som används för att generera konfigurationer baserat på noddata som skickas som en hash-tabell med hjälp av cmdlets, och noddata innehåller servrar med namnet **server** och **1server**.

### <a name="cause"></a>Orsak

Det här är ett känt problem med kompileringstjänsten.

### <a name="resolution"></a>Lösning

Den bästa lösningen är att kompilera lokalt eller i en CI/CD-pipeline och ladda upp nodkonfigurationens MOF-filer direkt till tjänsten. Om kompilering i tjänsten är ett krav är nästa bästa lösning att dela upp kompileringsjobben så att namnen inte överlappar varandra.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Gateway-timeoutfel vid överföring av DSC-konfiguration

#### <a name="issue"></a>Problem

Du får ett `GatewayTimeout` felmeddelande när du laddar upp en DSC-konfiguration. 

### <a name="cause"></a>Orsak

DSC-konfigurationer som tar lång tid att kompilera kan orsaka det här felet.

### <a name="resolution"></a>Lösning

Du kan göra dina DSC-konfigurationer parsa snabbare genom att uttryckligen inkludera `ModuleName` parametern för [import-DSCResource-anrop.](/powershell/scripting/dsc/configurations/import-dscresource)

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure för att förbättra kundupplevelsen. Azure Support ansluter Azure-communityn till svar, support och experter.
* Skapa en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
