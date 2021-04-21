---
title: Felsöka Azure Automation Hybrid Runbook Worker problem
description: Den här artikeln beskriver hur du felsöker och löser problem som uppstår Azure Automation Hybrid Runbook Workers.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 906e38b28015bd70cf1c97ba9323094d64a12c94
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830852"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Felsöka problem med Hybrid Runbook Worker

Den här artikeln innehåller information om felsökning och lösning av problem med Azure Automation Hybrid Runbook Workers. Allmän information finns i [Hybrid Runbook Worker översikt.](../automation-hybrid-runbook-worker.md)

## <a name="general"></a>Allmänt

Vilken Hybrid Runbook Worker är beroende av att en agent kommunicerar med ditt Azure Automation konto för att registrera arbetsrollen, ta emot runbook-jobb och rapportera status. För Windows är den här agenten Log Analytics-agenten för Windows. För Linux är det Log Analytics-agenten för Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Runbook-körningen misslyckas

#### <a name="issue"></a>Problem

Runbook-körningen misslyckas och du får följande felmeddelande:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Din runbook pausas strax efter att den har försökt att köra tre gånger. Det finns villkor som kan avbryta att runbooken slutförs. Det relaterade felmeddelandet kanske inte innehåller någon ytterligare information.

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Runbooks kan inte autentiseras med lokala resurser.
* Hybrid Worker-arbetaren finns bakom en proxy eller brandvägg.
* Den dator som är konfigurerad för Hybrid Runbook Worker uppfyller inte minimikraven för maskinvara.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har utgående åtkomst till **\* .azure-automation.net** på port 443.

Datorer som kör Hybrid Runbook Worker måste uppfylla minimikraven för maskinvara innan arbetsrollen konfigureras att vara värd för den här funktionen. Runbooks och den bakgrundsprocess som de använder kan orsaka att systemet överutanvänds och orsaka fördröjningar eller tidsgränser för runbook-jobb.

Bekräfta att datorn som ska köra Hybrid Runbook Worker-funktionen uppfyller minimikraven för maskinvaran. I så fall kan du övervaka processor- och minnesanvändningen för att fastställa korrelation mellan prestandan Hybrid Runbook Worker processer och Windows. Minnes- eller processorbelastning kan tyda på behovet av att uppgradera resurser. Du kan också välja en annan beräkningsresurs som stöder minimikraven och skala när arbetsbelastningskraven indikerar att en ökning är nödvändig.

Kontrollera **microsoft-SMA-händelseloggen** för en motsvarande händelse med beskrivningen `Win32 Process Exited with code [4294967295]` . Orsaken till det här felet är att du inte har konfigurerat autentisering i dina runbooks eller angett kör som-autentiseringsuppgifterna för Hybrid Runbook Worker gruppen. Granska runbook-behörigheter [i Köra runbooks på en Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) för att bekräfta att du har korrekt konfigurerad autentisering för dina runbooks.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: Händelse 15011 i Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Den Hybrid Runbook Worker tar emot händelse 15011, vilket indikerar att ett frågeresultat inte är giltigt. Följande fel visas när arbetaren försöker öppna en anslutning med [SignalR-servern](/aspnet/core/signalr/introduction).

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Orsak

Den Hybrid Runbook Worker har inte konfigurerats korrekt för den automatiserade funktionsdistributionen, till exempel för Uppdateringshantering. Distributionen innehåller en del som ansluter den virtuella datorn till Log Analytics-arbetsytan. PowerShell-skriptet söker efter arbetsytan i prenumerationen med det angivna namnet. I det här fallet finns Log Analytics-arbetsytan i en annan prenumeration. Skriptet kan inte hitta arbetsytan och försöker skapa en, men namnet har redan tagits. Därför misslyckas distributionen.

#### <a name="resolution"></a>Lösning

Du har två alternativ för att lösa det här problemet:

* Ändra PowerShell-skriptet så att det söker efter Log Analytics-arbetsytan i en annan prenumeration. Det här är en bra lösning om du planerar att distribuera Hybrid Runbook Worker datorer i framtiden.

* Konfigurera arbetsdatorn manuellt så att den körs i en Orchestrator-sandbox-miljö. Kör sedan en runbook som skapats i Azure Automation-kontot på arbetsrollen för att testa funktionerna.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Virtuella Windows Azure-datorer tas bort automatiskt från en Hybrid Worker-grupp

#### <a name="issue"></a>Problem

Du kan inte se Hybrid Runbook Worker virtuella datorer när arbetsdatorn har varit avstängd under en längre tid.

#### <a name="cause"></a>Orsak

Datorn Hybrid Runbook Worker har inte pingat Azure Automation mer än 30 dagar. Därför har Automation rensat Hybrid Runbook Worker eller System Worker-gruppen. 

#### <a name="resolution"></a>Lösning

Starta arbetsdatorn och registrera den sedan på nytt med Azure Automation. Anvisningar om hur du installerar Runbook-miljön och ansluter till Azure Automation finns i [Distribuera en Windows-Hybrid Runbook Worker](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: Inget certifikat hittades i certifikatarkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En runbook som körs på Hybrid Runbook Worker misslyckas med följande felmeddelande:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker använda ett [Kör](../automation-security-overview.md#run-as-accounts) som-konto i en runbook som körs på en Hybrid Runbook Worker där Kör som-kontocertifikatet inte finns. Hybrid Runbook Workers har inte certifikattillgången lokalt som standard. Kör som-kontot kräver att den här tillgången fungerar korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker en virtuell Azure-dator kan du använda [runbook-autentisering med hanterade identiteter i](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) stället. Det här scenariot förenklar autentiseringen genom att låta dig autentisera till Azure-resurser med hjälp av den hanterade identiteten för den virtuella Azure-datorn i stället för Kör som-kontot. När Hybrid Runbook Worker är en lokal dator måste du installera Kör som-kontocertifikatet på datorn. Information om hur du installerar certifikatet finns i stegen för att köra PowerShell-runbooken **Export-RunAsCertificateToHybridWorker** i [Run runbooks på en Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Fel 403 under registreringen av en Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Arbetsprocessens inledande registreringsfasen misslyckas och du får följande fel (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Orsak

Följande problem är möjliga orsaker:

* Det finns ett feltypat arbetsyte-ID eller arbetsytenyckel (primär) i agentens inställningar. 
* Den Hybrid Runbook Worker kan inte ladda ned konfigurationen, vilket orsakar ett kontolänkningsfel. När Azure aktiverar funktioner på datorer stöder det endast vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. Det är också möjligt att ett felaktigt datum eller en felaktig tid har angetts på datorn. Om tiden är +/- 15 minuter från den aktuella tiden misslyckas funktionsdistributionen.

#### <a name="resolution"></a>Lösning

##### <a name="mistyped-workspace-id-or-key"></a>Arbetsyte-ID eller nyckel som är felstavat
Information om hur du kontrollerar om agentens arbetsyte-ID eller arbetsytenyckel är feltypad finns i Lägga till eller ta bort en arbetsyta [– Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) för Windows-agenten eller Lägga till eller ta bort en arbetsyta [– Linux-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) för Linux-agenten. Se till att välja den fullständiga strängen från Azure Portal och kopiera och klistra in den noggrant.

##### <a name="configuration-not-downloaded"></a>Konfigurationen har inte laddats ned

Log Analytics-arbetsytan och Automation-kontot måste finnas i en länkad region. En lista över regioner som stöds finns i Azure Automation [och Log Analytics-arbetsytemappningar.](../how-to/region-mappings.md)

Du kan också behöva uppdatera datorns datum eller tidszon. Om du väljer ett anpassat tidsintervall kontrollerar du att intervallet finns i UTC, vilket kan skilja sig från din lokala tidszon.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Scenario: Set-AzStorageBlobContent misslyckas på en Hybrid Runbook Worker 

#### <a name="issue"></a>Problem

Runbook misslyckas när den försöker köra `Set-AzStorageBlobContent` och du får följande felmeddelande:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Orsak

 Det här felet orsakas av det långa filnamnsbeteendet för anrop som `[System.IO.Path]::GetFullPath()` lägger till UNC-sökvägar.

#### <a name="resolution"></a>Lösning

Som en tillfällig lösning kan du skapa en konfigurationsfil `OrchestratorSandbox.exe.config` med namnet med följande innehåll:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Placera filen i samma mapp som den körbara filen `OrchestratorSandbox.exe` . Exempel:

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Om du uppgraderar agenten tas den här konfigurationsfilen bort och måste återskapas.

## <a name="linux"></a>Linux

Linux-Hybrid Runbook Worker på [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md) för Linux för att kommunicera med ditt Automation-konto för att registrera arbetsrollen, ta emot runbook-jobb och rapportera status. Om registreringen av arbetsrollen misslyckas finns här några möjliga orsaker till felet.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenario: Linux Hybrid Runbook Worker får en fråga om ett lösenord vid signering av en runbook

#### <a name="issue"></a>Problem

Om du `sudo` kör kommandot för en Linux Hybrid Runbook Worker hämtar en oväntad uppmaning om ett lösenord.

#### <a name="cause"></a>Orsak

**Nxautomationuser-kontot** för Log Analytics-agenten för Linux är inte korrekt konfigurerat i **sudoers-filen.** Användaren Hybrid Runbook Worker lämplig konfiguration av kontobehörigheter och andra data så att den kan signera runbooks på Linux Runbook Worker.

#### <a name="resolution"></a>Lösning

* Kontrollera att Hybrid Runbook Worker har den körbara GnuPG-filen (GPG) på datorn.

* Kontrollera konfigurationen av **kontot nxautomationuser** i **sudoers-filen.** Se [Köra runbooks på en Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics-agenten för Linux körs inte

#### <a name="issue"></a>Problem

Log Analytics-agenten för Linux körs inte.

#### <a name="cause"></a>Orsak

Om agenten inte körs förhindrar den att Linux-Hybrid Runbook Worker kommunicerar med Azure Automation. Agenten kanske inte körs av olika orsaker.

#### <a name="resolution"></a>Lösning

 Kontrollera att agenten körs genom att ange kommandot `ps -ef | grep python` . Du bör se utdata som liknar följande. Python bearbetar med **nxautomation-användarkontot.** Om Azure Automation inte är aktiverad körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som har startats för en Linux-Hybrid Runbook Worker. De finns alla i katalogen /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf:** Arbetshanterarens process. Den startas direkt från DSC.
* **worker.conf:** Hybrid Worker-processen som registrerats automatiskt. Den startas av arbetschefen. Den här processen används av Uppdateringshantering och är transparent för användaren. Den här processen finns inte om Uppdateringshantering inte är aktiverat på datorn.
* **diy/worker.conf:** HYBRID WORKER-processen. GÖR-hybrid worker-processen används för att köra användar-runbooks på Hybrid Runbook Worker. Den skiljer sig bara från hybrid worker-processen med automatisk registrering i detalj om att en annan konfiguration används. Den här processen är inte närvarande om Azure Automation är inaktiverad och DIY Linux-Hybrid Worker inte är registrerad.

Om agenten inte körs kör du följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: Den angivna klassen finns inte

Om du ser felmeddelandet `The specified class does not exist..` **i /var/opt/microsoft/omsconfig/omsconfig.log** måste Log Analytics-agenten för Linux uppdateras. Kör följande kommando för att installera om agenten.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows-Hybrid Runbook Worker på [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md) för Windows för att kommunicera med ditt Automation-konto för att registrera arbetsrollen, ta emot runbook-jobb och rapportera status. Om registreringen av arbetsrollen misslyckas innehåller det här avsnittet några möjliga orsaker.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: Log Analytics-agenten för Windows körs inte

#### <a name="issue"></a>Problem

`healthservice`körs inte på den Hybrid Runbook Worker datorn.

#### <a name="cause"></a>Orsak

Om Log Analytics för Windows-tjänsten inte körs kan Hybrid Runbook Worker inte kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrollera att agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice` . Om tjänsten har stoppats anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenario: Händelse 4502 i Operations Manager loggen

#### <a name="issue"></a>Problem

I **händelseloggen Application and Services Logs\Operations Manager** visas händelse 4502 och ett händelsemeddelande `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` med följande beskrivning:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av att proxyn eller nätverksbrandväggen blockerar kommunikationen till Microsoft Azure. Kontrollera att datorn har utgående åtkomst till **\* .azure-automation.net** på port 443.

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje Hybrid Worker på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrollera om det finns några varnings- eller felhändelser i program- och **tjänstloggarna\Microsoft-SMA\Operations-** och Program- och **tjänstloggar\Operations Manager** händelseloggar. Loggarna anger en anslutning eller en annan typ av problem som påverkar aktiveringen av rollen till Azure Automation, eller ett problem som uppstår under normala åtgärder. Mer hjälp med att felsöka problem med Log Analytics-agenten finns i [Felsöka problem med Log Analytics Windows-agenten.](../../azure-monitor/agents/agent-windows-troubleshoot.md)

Hybrid Workers skickar [Runbook-utdata](../automation-runbook-output-and-messages.md) och meddelanden till Azure Automation på samma sätt som runbook-jobb som körs i molnet skickar utdata och meddelanden. Du kan aktivera utförliga dataströmmar och förloppsströmmar precis som du gör för runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenario: Orchestrator.Sandbox.exe kan inte ansluta till Microsoft 365 via proxy

#### <a name="issue"></a>Problem

Ett skript som körs på en Windows Hybrid Runbook Worker kan inte ansluta som förväntat för att Microsoft 365 i en Orchestrator-sandbox. Skriptet använder [Connect-MsolService](/powershell/module/msonline/connect-msolservice) för anslutning. 

Om du **justerarOrchestrator.Sandbox.exe.config** för att ange proxyn och listan över kringkopplingar så ansluts inte sandbox-miljön korrekt. En **Powershell_ise.exe.config-fil** med samma proxyserver och inställningar för att kringgå listan verkar fungera som förväntat. Service Management Automation (SMA)-loggar och PowerShell-loggar ger ingen information om proxy.

#### <a name="cause"></a>Orsak

Anslutningen till Active Directory Federation Services (AD FS) (AD FS) på servern kan inte kringgå proxyn. Kom ihåg att en PowerShell-sandbox-miljö körs som den inloggade användaren. En Orchestrator-sandbox-miljö är dock kraftigt anpassad och kan ignorera **Orchestrator.Sandbox.exe.config** filinställningar. Den har särskild kod för hantering av dator- eller Log Analytics-agentproxyinställningar, men inte för hantering av andra anpassade proxyinställningar. 

#### <a name="resolution"></a>Lösning

Du kan lösa problemet för Sandbox-miljön för Orchestrator genom att migrera skriptet för att använda Azure Active Directory-moduler i stället för MSOnline-modulen för PowerShell-cmdlets. Mer information finns i [Migrera från Orchestrator till Azure Automation (Beta).](../automation-orchestrator-migration.md)

Om du vill fortsätta att använda cmdletarna för MSOnline-modulen ändrar du skriptet så att [Invoke-Command används.](/powershell/module/microsoft.powershell.core/invoke-command) Ange värden för `ComputerName` parametrarna `Credential` och . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Den här kodändringen startar en helt ny PowerShell-session under kontexten för de angivna autentiseringsuppgifterna. Det bör göra det möjligt för trafiken att flöda genom en proxyserver som autentiserar den aktiva användaren.

>[!NOTE]
>Den här lösningen gör det onödigt att ändra sandbox-konfigurationsfilen. Även om du lyckas få konfigurationsfilen att fungera med skriptet rensas filen varje gång Hybrid Runbook Worker agenten uppdateras.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker rapporterar inte

#### <a name="issue"></a>Problem

Din Hybrid Runbook Worker datorn körs, men du ser inte pulsslagsdata för datorn i arbetsytan.

Följande exempelfråga visar datorerna i en arbetsyta och deras senaste pulsslag:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av en skadad cache på Hybrid Runbook Worker.

#### <a name="resolution"></a>Lösning

Lös problemet genom att logga in på Hybrid Runbook Worker och köra följande skript. Det här skriptet stoppar Log Analytics-agenten för Windows, tar bort cacheminnet och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker att ladda ned konfigurationen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: Du kan inte lägga till en Windows-Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Du får följande meddelande när du försöker lägga till en Hybrid Runbook Worker med hjälp av `Add-HybridRunbookWorker` cmdleten :

`Machine is already registered`

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas om datorn redan har registrerats med ett annat Automation-konto eller om du försöker lägga till Hybrid Runbook Worker när du har tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet genom att ta bort följande registernyckel, starta om `HealthService` och prova `Add-HybridRunbookWorker` cmdleten igen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: Du kan inte lägga till en Linux-Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Du får följande meddelande när du försöker lägga till en Hybrid Runbook Worker med hjälp av `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-skriptet:

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Dessutom försöker du avregistrera en Hybrid Runbook Worker med hjälp av `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` Python-skriptet:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om datorn redan har registrerats med ett annat Automation-konto, om Azure Hybrid Worker-gruppen har tagits bort eller om du försöker lägga till Hybrid Runbook Worker igen när du har tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet så här:

1. Ta bort agenten `sudo sh onboard_agent.sh --purge` .

1. Kör dessa kommandon:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Registrera agenten på nya `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` sätt.

1. Vänta tills mappen `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` har fyllts i.

1. Försök med `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-skriptet igen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure för att förbättra kundupplevelsen. Azure Support ansluter Azure-communityn till svar, support och experter.
* Skapa en azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
