---
title: Felsöka konfigurations problem med Azure Automation tillstånd
description: Den här artikeln beskriver hur du felsöker och löser konfigurations problem med Azure Automation tillstånd.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896536"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Felsöka konfigurations problem med Azure Automation tillstånd

Den här artikeln innehåller information om hur du felsöker och löser problem som uppstår när du kompilerar eller distribuerar konfigurationer i Azure Automation tillstånds konfiguration. Allmän information om tillstånds konfigurations funktionen finns i [Översikt över Azure Automation tillstånds konfiguration](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnostisera ett problem

Här följer några steg som kan hjälpa dig att diagnostisera problemet när du får ett kompileringsfel eller distributions fel för konfigurationen.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. kontrol lera att konfigurationen kompileras på den lokala datorn

Azure Automation tillstånds konfiguration bygger på PowerShell-önskad tillstånds konfiguration (DSC). Du hittar dokumentationen för DSC-språket och syntaxen i [POWERSHELL DSC-dokumenten](/powershell/scripting/overview).

Genom att kompilera en DSC-konfiguration på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - Moduler som saknas.
   - Syntaxfel.
   - Logic-fel.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visa DSC-loggar på noden

Om konfigurationen kompileras korrekt, men inte fungerar när den används på en nod, kan du hitta detaljerad information i DSC-loggarna. Information om var du hittar dessa loggar finns i [var DSC-händelseloggen](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) -modulen kan hjälpa dig att parsa detaljerad information från DSC-loggarna. Om du kontaktar supporten kräver de dessa loggar för att diagnostisera problemet.

Du kan installera `xDscDiagnostics` modulen på den lokala datorn genom att följa anvisningarna i [installera modulen stabil version](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

`xDscDiagnostics`Använd [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand)för att installera modulen på din Azure-dator. Du kan också använda alternativet **Kör kommando** i Azure Portal genom att följa stegen i [köra PowerShell-skript i din virtuella Windows-dator med kommandot kör](../../virtual-machines/windows/run-command.md).

Information om hur du använder **xDscDiagnostics** finns i [använda xDscDiagnostics för att analysera DSC-loggar](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Se även [xDscDiagnostics-cmdletar](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. kontrol lera att noderna och automation-arbetsytan har nödvändiga moduler

DSC är beroende av moduler som är installerade på noden. När du använder Azure Automation tillstånds konfiguration importerar du alla obligatoriska moduler till ditt Automation-konto genom att följa stegen i [importera moduler](../shared-resources/modules.md#import-modules). Konfigurationer kan också vara beroende av vissa versioner av moduler. Mer information finns i [Felsöka moduler](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: en konfiguration med specialtecken kan inte tas bort från portalen

### <a name="issue"></a>Problem

När du försöker ta bort en DSC-konfiguration från portalen visas följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt problem som har planer ATS att lösas.

### <a name="resolution"></a>Lösning

Använd cmdleten [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) för att ta bort konfigurationen.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: det gick inte att registrera DSC-agenten

### <a name="issue"></a>Problem

När [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) eller en annan DSC-cmdlet visas följande fel:

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

Det här felet orsakas normalt av en brand vägg, datorn är bakom en proxyserver eller andra nätverks fel.

### <a name="resolution"></a>Lösning

Kontrol lera att datorn har åtkomst till rätt slut punkter för DSC och försök igen. En lista över portar och adresser som behövs finns i [nätverks planering](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: status rapporter returnerar svars koden obehörig

### <a name="issue"></a>Problem

När du registrerar en nod med Azure Automation tillstånds konfiguration visas något av följande fel meddelanden:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Orsak

Det här problemet orsakas av ett felaktigt eller utgånget certifikat. Se [Omregistrera en nod](../automation-dsc-onboarding.md#re-register-a-node).

Det här problemet kan även bero på att en proxykonfiguration inte tillåter åtkomst till **_. Azure-Automation.net_*. Mer information finns i [konfiguration av privata nätverk](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Lösning

Använd följande steg för att omregistrera den felande DSC-noden.

#### <a name="step-1-unregister-the-node"></a>Steg 1: avregistrera noden

1. I Azure Portal går du till **Start**  >  **Automation-konton** > (ditt Automation-konto) > **tillstånds konfiguration (DSC)**.
1. Välj **noder** och välj noden som har problem.
1. Välj **avregistrera** för att avregistrera noden.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Steg 2: Avinstallera DSC-tillägget från noden

1. I Azure Portal går du till **Start sidan** för  >  **virtuella datorer** > (misslyckad nod) > **tillägg**.
1. Välj **Microsoft. PowerShell. DSC**, PowerShell DSC-tillägget.
1. Välj **Avinstallera** för att avinstallera tillägget.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Steg 3: ta bort alla dåliga eller utgångna certifikat från noden

Kör följande kommandon på den felaktiga noden från en upphöjd PowerShell-prompt:

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

#### <a name="step-4-reregister-the-failing-node"></a>Steg 4: registrera om den felaktiga noden

1. I Azure Portal går du till **Start**  >  **Automation-konton** > (ditt Automation-konto) > **tillstånds konfiguration (DSC)**.
1. Välj **noder**.
1. Välj **Lägg till**.
1. Välj noden som misslyckats.
1. Välj **Anslut** och välj önskade alternativ.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: noden har statusen misslyckades med fel meddelandet "hittades inte"

### <a name="issue"></a>Problem

Noden har en rapport med statusen Misslyckad och innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Orsak

Det här felet uppstår vanligt vis när noden tilldelas till ett konfigurations namn, till exempel **ABC**, i stället för en Node-KONFIGURATIONSFIL (MOF-fil), till exempel **ABC. -Webbserver**.

### <a name="resolution"></a>Lösning

* Kontrol lera att du har tilldelats noden med konfigurations namnet för noden och inte konfigurations namnet.
* Du kan tilldela en nods konfiguration till en nod med hjälp av Azure Portal eller med en PowerShell-cmdlet.

  * I Azure Portal går du till **Start**  >  **Automation-konton** > (ditt Automation-konto) > **tillstånds konfiguration (DSC)**. Välj sedan en nod och välj **tilldela konfiguration av nod**.
  * Använd cmdleten [set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: inga nodkonfigurationer (MOF-filer) skapades när en konfiguration kompilerades

### <a name="issue"></a>Problem

DSC-kompileringen pausas med felet:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Orsak

När uttrycket efter `Node` nyckelordet i DSC-konfigurationen utvärderas till skapas `$null` inga nodkonfigurationer.

### <a name="resolution"></a>Lösning

Åtgärda problemet med någon av följande lösningar:

* Se till att uttrycket bredvid `Node` nyckelordet i konfigurations definitionen inte utvärderas till null.
* Om du skickar [ConfigurationData](../automation-dsc-compile.md) när du kompilerar konfigurationen kontrollerar du att du skickar de värden som konfigurationen förväntar sig från konfigurations data.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: DSC-nodens rapport blir fastnat i status pågår

### <a name="issue"></a>Problem

DSC-agentens utdata:

```error
No instance found with given property values
```

### <a name="cause"></a>Orsak

Du har uppgraderat Windows Management Framework (WMF)-versionen och har skadat Windows Management Instrumentation (WMI).

### <a name="resolution"></a>Lösning

Följ anvisningarna i [kända problem och begränsningar i DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: det går inte att använda autentiseringsuppgifter i en DSC-konfiguration

### <a name="issue"></a>Problem

Ditt DSC-kompilator jobb har pausats med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration men angav inte korrekt för att `ConfigurationData` anges `PSDscAllowPlainTextPassword` till sant för varje konfiguration av noden.

### <a name="resolution"></a>Lösning

Kontrol lera att du `ConfigurationData` har angett `PSDscAllowPlainTextPassword` till true för varje nods konfiguration som nämns i konfigurationen. Mer information finns [i kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: fel vid bearbetning av "fel bearbetnings tillägg" vid aktivering av en dator från ett DSC-tillägg

### <a name="issue"></a>Problem

När du aktiverar en dator med hjälp av ett DSC-tillägg uppstår ett fel som innehåller felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Orsak

Det här felet uppstår vanligt vis när noden tilldelas ett konfigurations namn för noden som inte finns i tjänsten.

### <a name="resolution"></a>Lösning

* Kontrol lera att du har tilldelats noden med ett namn som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera konfigurations namnet för noden, vilket innebär att du aktiverar noden men inte tilldelar en konfiguration av en nod.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenario: ett eller flera fel inträffade när en nod registrerades med hjälp av PowerShell

### <a name="issue"></a>Problem

När du registrerar en nod genom att använda [register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) eller [Registrera-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)får du följande fel meddelande:

```error
One or more errors occurred.
```

### <a name="cause"></a>Orsak

Felet uppstår när du försöker registrera en nod i en separat prenumeration som används av Automation-kontot.

### <a name="resolution"></a>Lösning

Behandlar noden över prenumerationer som om den har definierats för ett separat moln eller lokalt. Registrera noden genom att använda något av följande alternativ för att aktivera datorer:

* Windows: [fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenario: fel meddelandet "etableringen misslyckades"

### <a name="issue"></a>Problem

När du registrerar en nod visas felet:

```error
Provisioning has failed
```

### <a name="cause"></a>Orsak

Det här meddelandet inträffar när det är problem med anslutningen mellan noden och Azure.

### <a name="resolution"></a>Lösning

Ta reda på om din nod finns i ett virtuellt privat nätverk (VPN) eller har andra problem med att ansluta till Azure. Se [fel sökning av problem med funktions distribution](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: fel med ett allmänt fel vid tillämpning av en konfiguration i Linux

### <a name="issue"></a>Problem

När du använder en konfiguration i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Orsak

Om **katalogen/tmp** -platsen är inställd på `noexec` , kan den aktuella versionen av DSC inte tillämpa konfigurationer.

### <a name="resolution"></a>Lösning

Ta bort `noexec` alternativet från **katalogen/tmp** -platsen.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: konfigurations namn för noder som överlappar kan resultera i en dålig version

### <a name="issue"></a>Problem

När du använder ett enda konfigurations skript för att generera flera nodkonfigurationer och vissa noders konfigurations namn är del mängder av andra namn, kan compiler-tjänsten sluta tilldela fel konfiguration. Det här problemet uppstår bara när du använder ett enda skript för att generera konfigurationer med konfigurations data per nod, och endast när namnet överlappar inträffar i början av strängen. Ett exempel är ett enda konfigurations skript som används för att generera konfigurationer baserade på Node-data som skickas som en hash-fil med hjälp av cmdletar, och Node-data innehåller servrar med namnet **Server** och **1server**.

### <a name="cause"></a>Orsak

Detta är ett känt problem med Compilation service.

### <a name="resolution"></a>Lösning

Den bästa lösningen är att kompilera lokalt eller i en CI/CD-pipeline och ladda upp MOF-filerna för nodens konfiguration direkt till tjänsten. Om kompileringen i tjänsten är ett krav, är nästa bästa lösning att dela upp Compilation-jobben så att det inte finns några överlappande i namn.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Gateway-timeout-fel vid uppladdning av DSC-konfiguration

#### <a name="issue"></a>Problem

Du får ett `GatewayTimeout` fel meddelande när du laddar upp en DSC-konfiguration. 

### <a name="cause"></a>Orsak

DSC-konfigurationer som tar lång tid att kompilera kan orsaka det här felet.

### <a name="resolution"></a>Lösning

Du kan göra så att dina DSC-konfigurationer kan parsas snabbare genom att uttryckligen inkludera `ModuleName` parametern för eventuella [import-dscresource Keyword Supports-](/powershell/scripting/dsc/configurations/import-dscresource) anrop.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
