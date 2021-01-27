---
title: Felsöka problem med Linux-uppdaterings agenten i Azure Automation
description: Den här artikeln beskriver hur du felsöker och löser problem med Windows Update-agenten för Linux i Uppdateringshantering.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 2fd92d79a3322b17f528194b9d39c26bf4c93b0c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896265"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Felsöka problem med uppdateringsagenten i Linux

Det kan finnas många orsaker till att datorn inte visas som klar (felfri) i Uppdateringshantering. Du kan kontrol lera hälso tillståndet för en Linux Hybrid Runbook Worker-agent för att fastställa det underliggande problemet. Följande är tre beredskaps tillstånd för en dator:

* Klart: Hybrid Runbook Worker har distribuerats och setts senast för en timme sedan.
* Frånkopplad: Hybrid Runbook Worker distribueras och lästes senast en timme sedan.
* Inte konfigurerat: det Hybrid Runbook Worker inte att hitta eller så har distributionen inte slutat.

> [!NOTE]
> Det kan finnas en liten fördröjning mellan det Azure Portal visar och datorns aktuella tillstånd.

Den här artikeln beskriver hur du kör fel sökaren för Azure-datorer från Azure Portal och datorer som inte är Azure-datorer i [scenariot offline](#troubleshoot-offline).

> [!NOTE]
> Fel söknings skriptet dirigerar för närvarande inte trafik via en proxyserver om en sådan har kon figurer ATS.

## <a name="start-the-troubleshooter"></a>Starta fel sökaren

För Azure-datorer väljer du **fel söknings** länken under kolumnen **Uppdatera agent beredskap** i portalen för att öppna sidan Felsök uppdaterings agent. För datorer som inte är Azure-datorer går länken till den här artikeln. Information om hur du felsöker en icke-Azure-dator finns i anvisningarna i avsnittet "Felsöka offline".

![Sidan VM-lista](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrollerna kräver att den virtuella datorn körs. Om den virtuella datorn inte körs visas **starta den virtuella datorn** .

På sidan Felsök uppdaterings agent väljer du **Kör kontroller** för att starta fel sökaren. Fel sökaren använder [Kör-kommandot](../../virtual-machines/linux/run-command.md) för att köra ett skript på datorn för att verifiera beroenden. När fel sökaren är färdig returneras resultatet av kontrollerna.

![Fel söknings sida](../media/update-agent-issues-linux/troubleshoot-page.png)

När kontrollerna är klara returneras resultatet i fönstret. Kryss avsnitten innehåller information om vad varje kontroll söker efter.

![Uppdatera agent kontroller Sidan](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kravkontroller

### <a name="operating-system"></a>Operativsystem

Operativ system kontrollen verifierar om Hybrid Runbook Worker kör något av följande operativ system.

|Operativsystem  |Kommentarer  |
|---------|---------|
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificerings baserad uppdatering kräver "yum" för att returnera säkerhets data, vilket CentOS inte har gjort.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

## <a name="monitoring-agent-service-health-checks"></a>Övervaka Agent tjänstens hälso kontroller

### <a name="log-analytics-agent"></a>Log Analytics-agent

Den här kontrollen säkerställer att Log Analytics-agenten för Linux är installerad. Anvisningar om hur du installerar den finns i [Installera agenten för Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Log Analytics agent status

Den här kontrollen säkerställer att Log Analytics-agenten för Linux körs. Om agenten inte körs kan du köra följande kommando för att försöka starta om den. Mer information om hur du felsöker agenten finns i [Linux-felsöka hybrid Runbook Worker problem](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Den här kontrollen avgör om agenten rapporterar till flera arbets ytor. Uppdateringshantering stöder inte multihoming.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den här kontrollen kontrollerar om Log Analytics-agenten för Linux har Hybrid Runbook Worker-paketet. Det här paketet krävs för att Uppdateringshantering ska fungera. Mer information finns i [Log Analytics agent för Linux körs inte](hybrid-runbook-worker.md#oms-agent-not-running).

Uppdateringshantering laddar ned Hybrid Runbook Worker paket från slut punkten för åtgärder. Om Hybrid Runbook Worker inte körs och [slut punkts](#operations-endpoint) kontrollen för åtgärder Miss lyckas kan uppdateringen Miss lyckas.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker status

Den här kontrollen säkerställer att Hybrid Runbook Worker körs på datorn. Processerna i exemplet nedan bör finnas om Hybrid Runbook Worker körs på rätt sätt.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Anslutnings kontroller

### <a name="general-internet-connectivity"></a>Allmän Internet anslutning

Den här kontrollen ser till att datorn har åtkomst till Internet.

### <a name="registration-endpoint"></a>Registrerings slut punkt

Den här kontrollen avgör om Hybrid Runbook Worker kan kommunicera korrekt med Azure Automation i arbets ytan Log Analytics.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med registrerings slut punkten. En lista över adresser och portar som ska öppnas finns i [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Åtgärds slut punkt

Den här kontrollen avgör om Log Analytics-agenten kan kommunicera korrekt med jobbets körnings data tjänst.

Proxy-och brand Väggs konfigurationer måste tillåta att Hybrid Runbook Worker agent kommunicerar med jobb körnings data tjänsten. En lista över adresser och portar som ska öppnas finns i [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics slut punkt 1

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

### <a name="log-analytics-endpoint-2"></a>Log Analytics slut punkt 2

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

### <a name="log-analytics-endpoint-3"></a>Log Analytics slut punkt 3

Den här kontrollen kontrollerar att datorn har åtkomst till de slut punkter som krävs av Log Analytics agenten.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Felsöka offline

Du kan använda fel sökaren offline på en Hybrid Runbook Worker genom att köra skriptet lokalt. Du kan hitta python-skriptet [UM_Linux_Troubleshooter_Offline. py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py)i GitHub. Ett exempel på utdata från det här skriptet visas i följande exempel:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Nästa steg

[Felsök hybrid Runbook Worker problem](hybrid-runbook-worker.md).
