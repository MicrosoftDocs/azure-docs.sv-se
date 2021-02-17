---
title: Felsök problem med Windows Update-agenten i Azure Automation
description: Den här artikeln beskriver hur du felsöker och löser problem med Windows Update-agenten under Uppdateringshantering.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580887"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Felsöka problem med uppdateringsagenten i Windows

Det kan finnas många orsaker till att datorn inte visas som klar (felfri) under en Uppdateringshantering distribution. Du kan kontrol lera hälso tillståndet för en Windows Hybrid Runbook Worker-agent för att fastställa det underliggande problemet. Följande är tre beredskaps tillstånd för en dator:

* Klart: Hybrid Runbook Worker har distribuerats och setts senast för en timme sedan.
* Frånkopplad: Hybrid Runbook Worker distribueras och lästes senast en timme sedan.
* Inte konfigurerad: det Hybrid Runbook Worker inte att hitta eller slutföra distributionen.

> [!NOTE]
> Det kan finnas en liten fördröjning mellan det Azure Portal visar och datorns aktuella tillstånd.

Den här artikeln beskriver hur du kör fel sökaren för Azure-datorer från Azure Portal och datorer som inte är Azure-datorer i [scenariot offline](#troubleshoot-offline).

> [!NOTE]
> Fel söknings skriptet innehåller nu kontroller för Windows Server Update Services (WSUS) och för nycklar för automatisk nedladdning och installation.

## <a name="start-the-troubleshooter"></a>Starta fel sökaren

För Azure-datorer kan du starta sidan Felsök uppdaterings agent genom att välja länken **Felsök** under kolumnen **Uppdatera agent beredskap** i portalen. För datorer som inte är Azure-datorer går länken till den här artikeln. Se [Felsöka offline](#troubleshoot-offline) för att felsöka en dator som inte är en Azure-dator.

![Skärm bild av Uppdateringshantering listan över virtuella datorer](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Om du vill kontrol lera hälso tillståndet för den Hybrid Runbook Worker måste den virtuella datorn köras. Om den virtuella datorn inte körs visas knappen **starta den virtuella datorn** .

På sidan Felsök uppdaterings agent väljer du **Kör kontroller** för att starta fel sökaren. Fel sökaren använder [Kör-kommandot](../../virtual-machines/windows/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När fel sökaren är färdig returneras resultatet av kontrollerna.

![Skärm bild av sidan Felsök uppdaterings agent](../media/update-agent-issues/troubleshoot-page.png)

Resultaten visas på sidan när de är klara. I avsnittet kontroller visas vad som ingår i varje kontroll.

![Skärm bild av kontrollerna Felsök uppdaterings agent](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kravkontroller

### <a name="operating-system"></a>Operativsystem

Operativ system kontrollen verifierar om Hybrid Runbook Worker kör något av de operativ system som visas i nästa tabell.

|Operativsystem  |Kommentarer  |
|---------|---------|
|Windows Server 2012 och senare |.NET Framework 4,6 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Windows PowerShell 5,1 krävs.  ([Ladda ned Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework kontrollen kontrollerar att systemet har [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) eller senare installerat.

### <a name="wmf-51"></a>WMF 5.1

WMF-kontrollen verifierar att systemet har den version av Windows Management Framework (WMF) som krävs, som är [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Den här kontrollen avgör om du använder TLS 1,2 för att kryptera din kommunikation. TLS 1,0 stöds inte längre av plattformen. Använd TLS 1,2 för att kommunicera med Uppdateringshantering.

## <a name="connectivity-checks"></a>Anslutnings kontroller

### <a name="registration-endpoint"></a>Registrerings slut punkt

Den här kontrollen avgör om agenten kan kommunicera korrekt med Agent tjänsten.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med registrerings slut punkten. En lista över adresser och portar som ska öppnas finns i [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Åtgärds slut punkt

Den här kontrollen avgör om agenten kan kommunicera korrekt med jobbets körnings data tjänst.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med jobb körnings data tjänsten. En lista över adresser och portar som ska öppnas finns i [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Hälso kontroller för VM-tjänst

### <a name="monitoring-agent-service-status"></a>Övervaknings agent tjänst status

Den här kontrollen avgör om Log Analytics agent för Windows ( `healthservice` ) körs på datorn. Mer information om hur du felsöker tjänsten finns i [Log Analytics agent för Windows körs inte](hybrid-runbook-worker.md#mma-not-running).

Information om hur du installerar om Log Analytics agent för Windows finns i [Installera agenten för Windows](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Övervaka agent tjänst händelser

Den här kontrollen avgör om några 4502-händelser visas i Azure Operations Manager-loggen på datorn under de senaste 24 timmarna.

Mer information om den här händelsen finns [i händelse 4502 i Operations Manager loggen](hybrid-runbook-worker.md#event-4502) för den här händelsen.

## <a name="access-permissions-checks"></a>Åtkomst behörighets kontroller

> [!NOTE]
> Fel sökaren dirigerar för närvarande inte trafik via en proxyserver om en sådan har kon figurer ATS.

### <a name="crypto-folder-access"></a>Åtkomst till krypto-mapp

Åtkomst kontrollen för krypto avgör om det lokala system kontot har åtkomst till C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda fel sökaren på en Hybrid Runbook Worker offline genom att köra skriptet lokalt. Hämta följande skript från GitHub: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). För att köra skriptet måste du ha WMF 4,0 eller senare installerat. Information om hur du hämtar den senaste versionen av PowerShell finns i [installera olika versioner av PowerShell](/powershell/scripting/install/installing-powershell).

Utdata från det här skriptet ser ut som i följande exempel:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Nästa steg

[Felsök hybrid Runbook Worker problem](hybrid-runbook-worker.md).
