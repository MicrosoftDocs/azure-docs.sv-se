---
title: Distribuera ett Linux-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du installerar en Azure Automation Hybrid Runbook Worker för att köra Runbooks på Linux-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.subservice: process-automation
ms.date: 02/18/2021
ms.topic: conceptual
ms.openlocfilehash: 543ae640871699c7e1fffda46463752483ff6a4e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708925"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera ett Linux-Hybrid Runbook Worker

Du kan använda funktionen för användar Hybrid Runbook Worker i Azure Automation för att köra Runbooks direkt på Azure-eller icke-Azure-datorer, inklusive servrar som är registrerade med [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md). Från datorn eller servern som är värd för rollen kan du köra Runbooks direkt och mot resurser i miljön för att hantera de lokala resurserna.

Linux-Hybrid Runbook Worker Kör Runbooks som en särskild användare som kan utökas för att köra kommandon som behöver förhöjda. Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera angivna datorer. Den här artikeln beskriver hur du installerar Hybrid Runbook Worker på en Linux-dator, hur du tar bort en arbets grupp och hur du tar bort en Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har följande innan du börjar.

### <a name="a-log-analytics-workspace"></a>En Log Analytics arbets yta

Hybrid Runbook Worker-rollen beror på en Azure Monitor Log Analytics arbets yta för att installera och konfigurera rollen. Du kan skapa den genom [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Om du inte har en Azure Monitor Log Analytics arbets yta granskar du [rikt linjerna för Azure Monitor logg design](../azure-monitor/logs/design-logs-deployment.md) innan du skapar arbets ytan.

### <a name="log-analytics-agent"></a>Log Analytics-agent

Hybrid Runbook Worker-rollen kräver [Log Analytics-agenten](../azure-monitor/agents/log-analytics-agent.md) för det Linux-operativsystem som stöds. För servrar eller datorer som ligger utanför Azure kan du installera Log Analytics-agenten med hjälp av [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md).

>[!NOTE]
>När du har installerat Log Analytics agent för Linux bör du inte ändra behörigheterna för `sudoers.d` mappen eller dess ägarskap. Sudo-behörighet krävs för **nxautomation** -kontot, vilket är den användar kontext som hybrid Runbook Worker körs under. Behörigheterna bör inte tas bort. Att begränsa detta till vissa mappar eller kommandon kan resultera i en ändring.
>

### <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande distributioner. Alla operativ system antas vara x64. x86 stöds inte för något operativ system.

* Amazon Linux 2012,09 till 2015,09
* CentOS Linux 5, 6, 7 och 8
* Oracle Linux 5, 6 och 7
* Red Hat Enterprise Linux Server 5, 6, 7 och 8
* Debian GNU/Linux 6, 7 och 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS och 18,04 LTS
* SUSE Linux Enterprise Server 12 och 15

> [!IMPORTANT]
> Innan du aktiverar funktionen Uppdateringshantering, som är beroende av system Hybrid Runbook Worker-rollen, bekräftar du de distributioner som den stöder [här](update-management/overview.md#supported-operating-systems).

### <a name="minimum-requirements"></a>Minimikrav

Minimi kraven för Linux-system och användar Hybrid Runbook Worker:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1,0 (TLS 1,1 och TLS 1,2 stöds)|
|Curl | webb klient för sväng | 7.15.5|
|Python – ctypes | Python 2. x krävs |
|PAM | Pluggable Authentication Modules|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | För att köra PowerShell-Runbooks måste PowerShell-kärnan installeras. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Lägga till en dator i en Hybrid Runbook Worker grupp

Du kan lägga till arbets datorn i en Hybrid Runbook Worker grupp i ett av dina Automation-konton. För datorer som är värdar för system hybrid Runbook Worker som hanteras av Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker grupp medlemskapet.

>[!NOTE]
>Azure Automation [uppdateringshantering](./update-management/overview.md) installerar automatiskt system hybrid Runbook Worker på en Azure-eller icke-Azure-dator som är aktive rad för uppdateringshantering. Den här arbetaren är dock inte registrerad med några Hybrid Runbook Worker grupper i ditt Automation-konto. Om du vill köra Runbooks på dessa datorer måste du lägga till dem i en Hybrid Runbook Worker grupp. Följ steg 4 under avsnittet [Installera ett Linux-hybrid Runbook Worker](#install-a-linux-hybrid-runbook-worker) för att lägga till det i en grupp.

## <a name="supported-linux-hardening"></a>Linux-härdning som stöds

Följande stöds inte ännu:

* CIS

## <a name="supported-runbook-types"></a>Runbook-typer som stöds

Linux hybrid Runbook Worker stöder en begränsad uppsättning Runbook-typer i Azure Automation, och de beskrivs i följande tabell.

|Typ av Runbook | Stöds |
|-------------|-----------|
|Python 2 |Ja |
|PowerShell |Ja<sup>1</sup> |
|PowerShell-arbetsflöde |Inga |
|Grafisk |Inga |
|Grafiskt PowerShell-arbetsflöde |Inga |

<sup>1</sup> PowerShell-Runbooks kräver att PowerShell Core installeras på Linux-datorn. Se [Installera PowerShell Core på Linux](/powershell/scripting/install/installing-powershell-core-on-linux) för att lära dig hur du installerar det.

### <a name="network-configuration"></a>Konfiguration av nätverk

Nätverks krav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installera en Linux-Hybrid Runbook Worker

Utför följande steg för att installera och konfigurera en Linux-Hybrid Runbook Worker.

1. Aktivera Azure Automation lösning på arbets ytan Log Analytics genom att köra följande kommando i en upphöjd PowerShell-kommandotolk eller i Cloud Shell i [Azure Portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Distribuera Log Analytics-agenten till mål datorn.

    * För virtuella Azure-datorer installerar du Log Analytics agent för Linux med hjälp av [tillägget för virtuella datorer för Linux](../virtual-machines/extensions/oms-linux.md). Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics-arbetsyta. Du kan använda en Azure Resource Manager mall, Azure CLI eller Azure Policy för att tilldela en inbyggd princip [för att distribuera Log Analytics agent för virtuella *Linux* -eller *Windows* -datorer](../governance/policy/samples/built-in-policies.md#monitoring) . När agenten har installerats kan datorn läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.

    * För datorer som inte är Azure-datorer kan du installera Log Analytics-agenten med hjälp av [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md). Arc-aktiverade servrar har stöd för distribution av Log Analytics agenten med följande metoder:

        - Använda ramverket för VM-tillägg.

            Med den här funktionen i Azure Arc-aktiverade servrar kan du distribuera Log Analytics-agentens VM-tillägg till en icke-Azure Windows-och/eller Linux-server. VM-tillägg kan hanteras med hjälp av följande metoder på dina hybrid datorer eller servrar som hanteras av Arc-aktiverade servrar:

            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager-mallar](../azure-arc/servers/manage-vm-extensions-template.md)

        - Använda Azure Policy.

            Med den här metoden använder du Azure Policy [distribuera Log Analytics agent till Linux-eller Windows Azure Arc-datorer](../governance/policy/samples/built-in-policies.md#monitoring) inbyggda principer för att granska om den aktiverade servern har Log Analytics-agenten installerad. Om agenten inte är installerad distribueras den automatiskt med hjälp av en reparations uppgift. Alternativt, om du planerar att övervaka datorerna med Azure Monitor for VMs, använder du i stället alternativet för [att aktivera Azure Monitor for VMS](../governance/policy/samples/built-in-initiatives.md#monitoring) för att installera och konfigurera Log Analytics agenten.

        Vi rekommenderar att du installerar Log Analytics agent för Windows eller Linux med Azure Policy.

    > [!NOTE]
    > Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC) måste du lägga till datorerna som DSC-noder.

    > [!NOTE]
    > [Nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) med motsvarande sudo-behörigheter måste finnas under installationen av Linux-hybrid Worker. Om du försöker installera Worker och kontot inte finns eller inte har rätt behörighet, Miss lyckas installationen.

3. Verifiera att agenten rapporterar till arbets ytan.

    Log Analytics-agenten för Linux ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

    När agenten har anslutit till Log Analytics arbets ytan efter några minuter kan du köra följande fråga för att kontrol lera att den skickar pulsslags data till arbets ytan.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    I Sök resultaten bör du se pulsslags poster för datorn, vilket indikerar att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslags post till den tilldelade arbets ytan.

4. Kör följande kommando för att lägga till datorn i en hybrid Runbook Worker grupp och ange värden för parametrarna `-w` , `-k` , `-g` , och `-e` .

    Du kan hämta den information som krävs för parametrar `-k` och `-e` från sidan **nycklar** i ditt Automation-konto. Välj **nycklar** under avsnittet **konto inställningar** på sidans vänstra sida.

    ![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e`Kopiera värdet för **URL** för parametern.

    * För `-k` parametern kopierar du värdet för **primär åtkomst nyckel**.

    * För `-g` parametern anger du namnet på den hybrid Runbook Worker grupp som den nya Linux hybrid Runbook Work ska ansluta till. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns skapas den med det namnet.

    * För `-w` parametern anger du Log Analytics arbetsyte-ID.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verifiera distributionen när skriptet har slutförts. På sidan **hybrid Runbook Worker grupper** i ditt Automation-konto, på fliken **användare hybrid Runbook Worker-grupp** , visas den nya eller befintliga gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen från listan på sidan genom att välja **hybrid arbetare** på menyn till vänster. På sidan **hybrid arbetare** kan du se varje medlem i gruppen i listan.

    > [!NOTE]
    > Om du använder tillägget Log Analytics virtuell dator för Linux för en virtuell Azure-dator rekommenderar vi att du ställer in `autoUpgradeMinorVersion` till `false` as-versioner för automatisk uppgradering kan orsaka problem med hybrid Runbook Worker. Information om hur du uppgraderar tillägget manuellt finns i [Azure CLI-distribution](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Inaktivera verifiering av signatur

Som standard kräver Linux hybrid Runbook Worker verifiering av signatur. Om du kör en osignerad Runbook mot en arbets grupp visas ett `Signature validation failed` fel meddelande. Kör följande kommando för att inaktivera verifiering av signatur. Ersätt den andra parametern med ditt Log Analytics arbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker

Du kan använda kommandot `ls /var/opt/microsoft/omsagent` på Hybrid Runbook Worker för att hämta arbetsyte-ID: t. En mapp skapas med namnet med arbetsyte-ID: t.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Det här skriptet tar inte bort Log Analytics agenten för Linux från datorn. Den tar bara bort funktionaliteten och konfigurationen av Hybrid Runbook Workers rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en Hybrid Runbook Worker grupp Linux-datorer använder du samma steg som för en Windows hybrid Worker-grupp. Se [ta bort en hybrid Worker grupp](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem – Linux](troubleshoot/hybrid-runbook-worker.md#linux).