---
title: Felsöka Azure Automation Hybrid Runbook Worker problem
description: Den här artikeln beskriver hur du felsöker och löser problem som uppstår med Azure Automation hybrid Runbook Worker.
services: automation
ms.subservice: ''
ms.date: 11/25/2019
ms.topic: troubleshooting
ms.openlocfilehash: 214501c447632232dc00b61643ea21083bd0e4ac
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896502"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Felsöka problem med Hybrid Runbook Worker

Den här artikeln innehåller information om hur du felsöker och löser problem med Azure Automation hybrid Runbook Worker. Allmän information finns i [hybrid Runbook Worker översikt](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Azure Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. För Windows är den här agenten Log Analytics agent för Windows. För Linux är det Log Analytics-agenten för Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Runbook-körningen Miss lyckas

#### <a name="issue"></a>Problem

Runbook-körningen Miss lyckas och följande fel meddelande visas:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Din Runbook har pausats strax efter det att den försökt köras tre gånger. Det finns villkor som kan avbryta Runbook-flödet från att slutföras. Det relaterade fel meddelandet kanske inte innehåller ytterligare information.

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Runbooks kan inte autentiseras med lokala resurser.
* Hybrid Worker ligger bakom en proxy eller brand vägg.
* Datorn som kon figurer ATS för att köra Hybrid Runbook Worker uppfyller inte minimi kraven för maskin vara.

#### <a name="resolution"></a>Lösning

Kontrol lera att datorn har utgående åtkomst till **\* . Azure-Automation.net** på port 443.

Datorer som kör Hybrid Runbook Worker bör uppfylla minimi kraven för maskin vara innan arbets tagaren har kon figurer ATS för att vara värd för den här funktionen. Runbooks och bakgrunds processen som de använder kan orsaka att systemet överanvänds och orsakar fördröjningar eller tids gränser för Runbook-jobbet.

Bekräfta att datorn som ska köra Hybrid Runbook Worker funktionen uppfyller minimi kraven för maskin vara. Om det gör det kan du övervaka processor-och minnes användning för att fastställa eventuella korrelationer mellan prestandan för Hybrid Runbook Worker processer och Windows. Eventuell minnes-eller processor belastning kan indikera att du behöver uppgradera resurser. Du kan också välja en annan beräknings resurs som uppfyller minimi kraven och skala när arbets belastningen kräver att en ökning är nödvändig.

Kontrol lera händelse loggen för **Microsoft-SMA** för en motsvarande händelse med beskrivningen `Win32 Process Exited with code [4294967295]` . Orsaken till det här felet är att du inte har konfigurerat autentisering i dina runbooks eller angett kör som-autentiseringsuppgifterna för Hybrid Runbook Worker gruppen. Granska Runbook-behörigheter i [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md) för att bekräfta att du har konfigurerat autentisering för dina runbooks korrekt.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: händelse 15011 i Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Hybrid Runbook Worker tar emot händelse 15011, vilket indikerar att ett frågeresultat inte är giltigt. Följande fel visas när arbets tagaren försöker öppna en anslutning med [signal servern](/aspnet/core/signalr/introduction).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker har inte kon figurer ATS korrekt för den automatiserade funktions distributionen, till exempel för Uppdateringshantering. Distributionen innehåller en del som ansluter den virtuella datorn till Log Analytics-arbetsytan. PowerShell-skriptet söker efter arbets ytan i prenumerationen med det angivna namnet. I det här fallet är Log Analytics arbets ytan i en annan prenumeration. Skriptet kan inte hitta arbets ytan och försöker skapa en, men namnet är redan upptaget. Därför Miss lyckas distributionen.

#### <a name="resolution"></a>Lösning

Det finns två alternativ för att lösa problemet:

* Ändra PowerShell-skriptet för att söka efter Log Analytics arbets ytan i en annan prenumeration. Det här är en bättre lösning att använda om du planerar att distribuera många Hybrid Runbook Worker datorer i framtiden.

* Konfigurera arbets datorn manuellt så att den körs i ett Orchestrator-läge. Kör sedan en Runbook som skapats i Azure Automation kontot på arbets tagaren för att testa funktionen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: virtuella Windows Azure-datorer tas automatiskt bort från en hybrid Worker-grupp

#### <a name="issue"></a>Problem

Du kan inte se Hybrid Runbook Worker eller virtuella datorer när arbets datorn har inaktiverats under en längre tid.

#### <a name="cause"></a>Orsak

Den Hybrid Runbook Worker datorn har inte Pingat Azure Automation i mer än 30 dagar. Därför har Automation rensat Hybrid Runbook Workers gruppen eller systemets arbets grupp. 

#### <a name="resolution"></a>Lösning

Starta arbets datorn och rereregister den med Azure Automation. Instruktioner för hur du installerar Runbook-miljön och ansluter till Azure Automation finns i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: det gick inte att hitta något certifikat i certifikat arkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En Runbook som körs på en Hybrid Runbook Worker Miss lyckas med följande fel meddelande:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Orsak

Felet uppstår när du försöker använda ett [Kör som-konto](../manage-runas-account.md) i en Runbook som körs på en hybrid Runbook Worker där certifikatet för kör som-kontot inte finns. Hybrid Runbook Worker har inte certifikat till gången lokalt som standard. Kör som-kontot kräver att den här till gången fungerar korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker är en virtuell Azure-dator kan du i stället använda [Runbook-autentisering med hanterade identiteter](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) . Det här scenariot fören klar autentiseringen genom att du kan autentisera till Azure-resurser med hjälp av den hanterade identiteten för den virtuella Azure-datorn i stället för kör som-kontot När Hybrid Runbook Worker är en lokal dator måste du installera kör som-användarkontot på datorn. Information om hur du installerar certifikatet finns i stegen för att köra PowerShell Runbook **export-RunAsCertificateToHybridWorker** i [köra runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: fel 403 vid registrering av en Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Det går inte att arbeta med den första registrerings fasen och du får följande fel meddelande (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Orsak

Följande problem är möjliga orsaker:

* Det finns ett felangett arbetsyte-ID eller en nyckel för arbets ytan (primär) i agentens inställningar. 
* Hybrid Runbook Worker kan inte ladda ned konfigurationen, vilket leder till ett konto länknings fel. När Azure aktiverar funktioner på datorer stöder den bara vissa regioner för länkning av en Log Analytics arbets yta och ett Automation-konto. Det är också möjligt att ett felaktigt datum eller tid har angetts på datorn. Om tiden är +/-15 minuter från den aktuella tiden, Miss lyckas funktions distributionen.

#### <a name="resolution"></a>Lösning

##### <a name="mistyped-workspace-id-or-key"></a>Felangett arbetsyte-ID eller nyckel
För att kontrol lera att agentens arbetsyte-ID eller arbets ytans nyckel skrevs in, se [lägga till eller ta bort en arbets yta – Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) för Windows-agenten eller [lägga till eller ta bort en arbets yta – Linux-Agent](../../azure-monitor/platform/agent-manage.md#linux-agent) för Linux-agenten Se till att välja hela strängen från Azure Portal och kopiera och klistra in den noggrant.

##### <a name="configuration-not-downloaded"></a>Konfigurationen laddas inte ned

Din Log Analytics arbets yta och Automation-konto måste vara i en länkad region. En lista över regioner som stöds finns i [Azure Automation och Log Analytics mappningar för arbets ytor](../how-to/region-mappings.md).

Du kan också behöva uppdatera datum-eller tids zonen på din dator. Om du väljer ett anpassat tidsintervall kontrollerar du att intervallet är UTC, vilket kan skilja sig från den lokala tids zonen.

## <a name="linux"></a>Linux

Linux-Hybrid Runbook Worker är beroende av [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md) för att kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om registreringen av arbets processen Miss lyckas kan det hända att det finns några möjliga orsaker till felet.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenario: Linux Hybrid Runbook Worker tar emot ett lösen ord när en Runbook signeras

#### <a name="issue"></a>Problem

Om du kör `sudo` kommandot för ett Linux-hybrid Runbook Worker hämtas en oväntad prompt för ett lösen ord.

#### <a name="cause"></a>Orsak

**Nxautomationuser** -kontot för Log Analytics agent för Linux har inte kon figurer ATS korrekt i **sudoers** -filen. Hybrid Runbook Worker behöver lämplig konfiguration av konto behörigheter och andra data så att de kan signera Runbooks på Linux Runbook Worker.

#### <a name="resolution"></a>Lösning

* Kontrol lera att den Hybrid Runbook Worker har den körbara filen GnuPG (GPG) på datorn.

* Verifiera konfigurationen av **nxautomationuser** -kontot i **sudoers** -filen. Se [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics agent för Linux körs inte

#### <a name="issue"></a>Problem

Log Analytics agenten för Linux körs inte.

#### <a name="cause"></a>Orsak

Om agenten inte körs förhindrar Linux-Hybrid Runbook Worker att kommunicera med Azure Automation. Agenten kanske inte körs av olika orsaker.

#### <a name="resolution"></a>Lösning

 Kontrol lera att agenten körs genom att ange kommandot `ps -ef | grep python` . Du bör se utdata som liknar följande. Python-processerna med **nxautomation** -användarkontot. Om funktionen Azure Automation inte är aktive rad körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux-Hybrid Runbook Worker. De finns i/var/opt/Microsoft/omsagent/State/automationworker/-katalogen.

* **OMS. conf**: Worker Manager-processen. Den startas direkt från DSC.
* **Worker. conf**: den automatiskt registrerade hybrid Worker-processen. Den startas av Worker Manager. Den här processen används av Uppdateringshantering och är transparent för användaren. Den här processen finns inte om Uppdateringshantering inte har Aktiver ATS på datorn.
* **gör det själv/Worker. conf**: gör det själv hybrid Worker-processen. GÖR det själv hybrid Worker-processen används för att köra användar-Runbooks på Hybrid Runbook Worker. Det skiljer sig bara från den automatiskt registrerade hybrid Worker-processen i den nyckel information som den använder en annan konfiguration. Den här processen finns inte om Azure Automation är inaktive rad och gör det själv Linux Hybrid Worker inte är registrerad.

Om agenten inte körs kör du följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: den angivna klassen finns inte

Om du ser fel meddelandet `The specified class does not exist..` i **/var/opt/microsoft/omsconfig/omsconfig.log** måste Log Analytics-agenten för Linux uppdateras. Kör följande kommando för att installera om agenten.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av [Log Analytics-agenten för att Windows](../../azure-monitor/platform/log-analytics-agent.md) ska kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om det inte går att registrera arbets tagaren, innehåller det här avsnittet några möjliga orsaker.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: Log Analytics agenten för Windows körs inte

#### <a name="issue"></a>Problem

`healthservice`Körs inte på den hybrid Runbook Worker datorn.

#### <a name="cause"></a>Orsak

Om Log Analytics för Windows-tjänsten inte körs kan Hybrid Runbook Worker inte kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrol lera att agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice` . Om tjänsten har stoppats anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenario: händelse 4502 i Operations Manager loggen

#### <a name="issue"></a>Problem

I händelse loggen **program-och tjänst loggar \ Operations Manager** visas händelse 4502 och ett händelse meddelande som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` följande beskrivning:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Orsak

Det här problemet kan bero på att proxyn eller nätverks brand väggen blockerar kommunikationen till Microsoft Azure. Kontrol lera att datorn har utgående åtkomst till **\* . Azure-Automation.net** på port 443.

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybrid Worker på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrol lera om det finns några varnings-eller fel händelser i händelse loggarna **program och tjänster Logs\Microsoft-SMA\Operations** och **program-och tjänst loggar \ Operations Manager** . Dessa loggar indikerar en anslutning eller någon annan typ av problem som påverkar aktivering av rollen för att Azure Automation, eller ett problem som påträffas under normala åtgärder. Mer hjälp fel söknings problem med Log Analytics agent finns i [Felsöka problem med Log Analytics Windows-agenten](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybrid Worker skickar [Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) till Azure Automation på samma sätt som Runbook-jobb som körs i molnet skickar utdata och meddelanden. Du kan aktivera utförlig och förlopps strömmar precis som du gör för Runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenario: Orchestrator.Sandbox.exe kan inte ansluta till Microsoft 365 via proxy

#### <a name="issue"></a>Problem

Ett skript som körs på en Windows-Hybrid Runbook Worker kan inte ansluta som förväntat Microsoft 365 på ett Orchestrator-sandbox. Skriptet använder [Connect-MSOLService](/powershell/module/msonline/connect-msolservice) för anslutning. 

Om du justerar **Orchestrator.Sandbox.exe.config** för att ställa in proxyn och bypass-listan, ansluts inte sand lådan ändå korrekt. En **Powershell_ise.exe.config** -fil med samma inställningar för proxy och kringgå lista verkar fungera som förväntat. Service Management Automation (SMA) loggar och PowerShell-loggar tillhandahåller inte någon information om proxyn.

#### <a name="cause"></a>Orsak

Anslutningen till Active Directory Federation Services (AD FS) (AD FS) på servern kan inte kringgå proxyn. Kom ihåg att en PowerShell-sandbox körs som den loggade användaren. Ett Orchestrator-Sandbox är dock mycket anpassat och kan ignorera **Orchestrator.Sandbox.exe.config** fil inställningar. Den har särskild kod för hantering av dator-eller Log Analytics-Gent, men inte för hantering av andra anpassade proxyinställningar. 

#### <a name="resolution"></a>Lösning

Du kan lösa problemet för Orchestrator sandbox genom att migrera skriptet så att det använder Azure Active Directory moduler i stället för MSOnline-modulen för PowerShell-cmdletar. Mer information finns i [Migrera från Orchestrator till Azure Automation (beta)](../automation-orchestrator-migration.md).

Om du vill fortsätta att använda MSOnline-modulens cmdlets ändrar du skriptet för att använda [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command). Ange värden för `ComputerName` parametrarna och `Credential` . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Den här kod ändringen startar en helt ny PowerShell-session under kontexten för de angivna autentiseringsuppgifterna. Det bör göra det möjligt för trafiken att flöda genom en proxyserver som autentiserar den aktiva användaren.

>[!NOTE]
>Den här lösningen gör det onödigt att ändra sand boxens konfigurations fil. Även om du lyckas att göra konfigurations filen till ditt skript rensas filen varje gång som Hybrid Runbook Worker agenten uppdateras.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker inte rapportering

#### <a name="issue"></a>Problem

Hybrid Runbook Worker datorn körs, men inga pulsslags data visas för datorn i arbets ytan.

I följande exempel fråga visas datorerna i en arbets yta och deras senaste pulsslag:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett skadat cacheminne på Hybrid Runbook Worker.

#### <a name="resolution"></a>Lösning

Lös problemet genom att logga in på Hybrid Runbook Worker och köra följande skript. Det här skriptet stoppar Log Analytics agent för Windows, tar bort dess cacheminne och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker att ladda ned konfigurationen igen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: du kan inte lägga till en Windows-Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker lägga till en Hybrid Runbook Worker med hjälp av `Add-HybridRunbookWorker` cmdleten:

```error
Machine is already registered
```

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om datorn redan har registrerats med ett annat Automation-konto eller om du försöker lägga till Hybrid Runbook Worker igen efter att ha tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet genom att ta bort följande register nyckel, starta om `HealthService` och prova `Add-HybridRunbookWorker` cmdleten igen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: du kan inte lägga till en Linux-Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker lägga till en Hybrid Runbook Worker med hjälp av python- `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` skriptet:

```error
Unable to register, an existing worker was found. Please deregister any existing worker and try again.
```

Dessutom försöker avregistrera en Hybrid Runbook Worker med hjälp av `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` python-skriptet:

```error
Failed to deregister worker. [response_status=404]
```

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om datorn redan har registrerats med ett annat Automation-konto, om Azure Hybrid Worker-gruppen har tagits bort eller om du försöker lägga till Hybrid Runbook Worker igen när du har tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet så här:

1. Ta bort agenten `sudo sh onboard_agent.sh --purge` .

1. Kör dessa kommandon:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Återställa agenten `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Vänta tills mappen har `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` fyllts i.

1. Prova `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` python-skriptet igen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
