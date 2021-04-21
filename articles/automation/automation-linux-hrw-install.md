---
title: Distribuera en Linux-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du installerar Azure Automation Hybrid Runbook Worker för att köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller i molnmiljön.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833660"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuera en Linux-Hybrid Runbook Worker

Du kan använda användarfunktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på Azure- eller icke-Azure-datorn, inklusive servrar som [är registrerade med Azure Arc aktiverade servrar](../azure-arc/servers/overview.md). Från den dator eller server som är värd för rollen kan du köra runbooks direkt och mot resurser i miljön för att hantera de lokala resurserna.

Linux-Hybrid Runbook Worker kör runbooks som en särskild användare som kan höjas för att köra kommandon som behöver utökade privilegier. Azure Automation lagrar och hanterar runbooks och levererar dem sedan till en eller flera angivna datorer. Den här artikeln beskriver hur du installerar Hybrid Runbook Worker på en Linux-dator, hur du tar bort arbetsrollen och hur du tar bort en Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker kan du läsa Run [runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) (Kör runbooks på en Hybrid Runbook Worker) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller i en annan molnmiljö.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande innan du börjar.

### <a name="a-log-analytics-workspace"></a>En Log Analytics-arbetsyta

Vilken Hybrid Runbook Worker roll är beroende av en Azure Monitor Log Analytics-arbetsyta för att installera och konfigurera rollen. Du kan skapa den [via Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Om du inte har en Log Analytics Azure Monitor arbetsyta kan du gå [igenom Azure Monitor Log-design](../azure-monitor/logs/design-logs-deployment.md) innan du skapar arbetsytan.

### <a name="log-analytics-agent"></a>Log Analytics-agent

Rollen Hybrid Runbook Worker Log [Analytics-agenten för](../azure-monitor/agents/log-analytics-agent.md) det Linux-operativsystem som stöds. För servrar eller datorer som finns utanför Azure kan du installera Log Analytics-agenten med hjälp [Azure Arc aktiverade servrarna](../azure-arc/servers/overview.md).

>[!NOTE]
>När du har installerat Log Analytics-agenten för Linux bör du inte ändra behörigheterna för `sudoers.d` mappen eller dess ägarskap. Sudo-behörighet krävs för **nxautomation-kontot,** vilket är användarkontexten som Hybrid Runbook Worker körs under. Behörigheterna bör inte tas bort. Om du begränsar detta till vissa mappar eller kommandon kan det leda till en stor ändring.
>

### <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande distributioner. Alla operativsystem antas vara x64. x86 stöds inte för något operativsystem.

* Amazon Linux 2012.09 till 2015.09
* CentOS Linux 5, 6, 7 och 8
* Oracle Linux 5, 6 och 7
* Red Hat Enterprise Linux Server 5, 6, 7 och 8
* Debian GNU/Linux 6, 7 och 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS och 18.04 LTS
* SUSE Linux Enterprise Server version 12 och 15 (SUSE släppte inte versionerna 13 eller 14)

> [!IMPORTANT]
> Innan du aktiverar funktionen Uppdateringshantering, som är beroende av systemrollen Hybrid Runbook Worker, bekräftar du de distributioner som stöds [här](update-management/overview.md#supported-operating-systems).

### <a name="minimum-requirements"></a>Minimikrav

Minimikraven för ett Linux-system och Hybrid Runbook Worker är:

* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

| **Nödvändigt paket** | **Beskrivning** | **Lägsta version**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotek| 2.5-12 |
|Openssl| OpenSSL-bibliotek | 1.0 (TLS 1.1 och TLS 1.2 stöds)|
|Curl | cURL-webbklient | 7.15.5|
|Python-ctypes | Python 2.x eller Python 3.x krävs |
|PAM | Pluggable Authentication Modules|
| **Valfritt paket** | **Beskrivning** | **Lägsta version**|
| PowerShell Core | Om du vill köra PowerShell-runbooks PowerShell Core måste installeras. Se [Installera PowerShell Core Linux för att](/powershell/scripting/install/installing-powershell-core-on-linux) lära dig hur du installerar det. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Lägga till en dator i Hybrid Runbook Worker grupp

Du kan lägga till arbetsdatorn i en Hybrid Runbook Worker grupp i ett av dina Automation-konton. För datorer som är värdar för systemet Hybrid Runbook Worker som hanteras Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker gruppmedlemskap.

>[!NOTE]
>Azure Automation [Uppdateringshantering](./update-management/overview.md) installerar automatiskt Hybrid Runbook Worker på en Azure- eller icke-Azure-dator som är aktiverad för Uppdateringshantering. Den här arbetsrollen är dock inte registrerad med några Hybrid Runbook Worker grupper i ditt Automation-konto. Om du vill köra dina runbooks på dessa datorer måste du lägga till dem i en Hybrid Runbook Worker grupp. Följ steg 4 under avsnittet Installera en [Linux-Hybrid Runbook Worker lägga](#install-a-linux-hybrid-runbook-worker) till den i en grupp.

## <a name="supported-linux-hardening"></a>Linux-härdning som stöds

Följande stöds inte ännu:

* Cis

## <a name="supported-runbook-types"></a>Runbook-typer som stöds

Linux Hybrid Runbook Workers stöder en begränsad uppsättning runbook-typer i Azure Automation och de beskrivs i följande tabell.

|Runbook-typ | Stöds |
|-------------|-----------|
|Python 3 (förhandsversion)|Ja, krävs endast för dessa distributioner: SUSE LES 15, RHEL 8 och CentOS 8|
|Python 2 |Ja, för distributioner som inte kräver Python 3<sup>1</sup> |
|PowerShell |Ja<sup>2</sup> |
|PowerShell-arbetsflöde |No |
|Grafisk |No |
|Grafiskt PowerShell-arbetsflöde |No |

<sup>1</sup> Se [Linux-operativsystem som stöds.](#supported-linux-operating-systems)

<sup>2</sup> PowerShell-runbooks kräver PowerShell Core installeras på Linux-datorn. Se [Installera PowerShell Core Linux för att](/powershell/scripting/install/installing-powershell-core-on-linux) lära dig hur du installerar det.

### <a name="network-configuration"></a>Konfiguration av nätverk

Nätverkskrav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installera en Linux-Hybrid Runbook Worker

Det finns två metoder för att distribuera en Hybrid Runbook Worker. Du kan importera och köra en runbook från Runbook-galleriet i Azure Portal, eller så kan du manuellt köra en serie PowerShell-kommandon för att utföra samma uppgift.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importera en runbook från Runbook-galleriet

Importproceduren beskrivs i detalj i [Importera runbooks från GitHub med Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Namnet på den runbook som ska importeras är **Create Automation Linux HybridWorker**.

Runbooken använder följande parametrar.

| Parameter | Status | Beskrivning |
| ------- | ----- | ----------- |
| `Location` | Obligatorisk | Platsen för Log Analytics-arbetsytan. |
| `ResourceGroupName` | Obligatorisk | Resursgruppen för ditt Automation-konto. |
| `AccountName` | Obligatorisk | Automation-kontonamnet som Hybrid Run Worker ska registreras i. |
| `CreateLA` | Obligatorisk | Om värdet är true används värdet för för `WorkspaceName` för att skapa en Log Analytics-arbetsyta. Om värdet är false måste värdet `WorkspaceName` för referera till en befintlig arbetsyta. |
| `LAlocation` | Valfritt | Den plats där Log Analytics-arbetsytan kommer att skapas eller där den redan finns. |
| `WorkspaceName` | Valfritt | Namnet på Log Analytics-arbetsytan som ska skapas eller användas. |
| `CreateVM` | Obligatorisk | Om värdet är true använder du värdet `VMName` för som namn på en ny virtuell dator. Om det är falskt använder du `VMName` för att hitta och registrera en befintlig virtuell dator. |
| `VMName` | Valfritt | Namnet på den virtuella dator som antingen skapas eller registreras, beroende på värdet för `CreateVM` . |
| `VMImage` | Valfritt | Namnet på den virtuella datoravbildning som ska skapas. |
| `VMlocation` | Valfritt | Platsen för den virtuella dator som antingen har skapats eller registrerats. Om den här platsen inte anges används `LAlocation` värdet för . |
| `RegisterHW` | Obligatorisk | Om det är sant registrerar du den virtuella datorn som en Hybrid Worker. |
| `WorkerGroupName` | Obligatorisk | Namnet på Hybrid Worker grupp. |

### <a name="manually-run-powershell-commands"></a>Köra PowerShell-kommandon manuellt

Utför följande steg för att installera Hybrid Runbook Worker konfigurera en Linux-Hybrid Runbook Worker.

1. Aktivera Azure Automation i Log Analytics-arbetsytan genom att köra följande kommando i en upphöjd PowerShell-kommandotolk eller i Cloud Shell [i Azure Portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Distribuera Log Analytics-agenten till måldatorn.

    * För virtuella Azure-datorer installerar du Log Analytics-agenten för Linux med hjälp [av tillägget för virtuella datorer för Linux.](../virtual-machines/extensions/oms-linux.md) Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar virtuella datorer på en befintlig Log Analytics-arbetsyta. Du kan använda en Azure Resource Manager mall, Azure CLI eller Azure Policy för att tilldela den inbyggda principen Distribuera [Log Analytics-agenten för *Virtuella Linux-* eller *Windows-datorer.*](../governance/policy/samples/built-in-policies.md#monitoring) När agenten har installerats kan datorn läggas till i en Hybrid Runbook Worker i ditt Automation-konto.

    * För datorer som inte är Azure-datorer kan du installera Log Analytics-agenten [med Azure Arc aktiverade servrar](../azure-arc/servers/overview.md). Arc-aktiverade servrar stöder distribution av Log Analytics-agenten med hjälp av följande metoder:

        - Använda ramverket för VM-tillägg.

            Med den här funktionen Azure Arc-aktiverade servrar kan du distribuera Log Analytics-agentens VM-tillägg till en icke-Azure Windows- och/eller Linux-server. VM-tillägg kan hanteras med följande metoder på dina hybriddatorer eller servrar som hanteras av Arc-aktiverade servrar:

            - Den [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager mallar](../azure-arc/servers/manage-vm-extensions-template.md)

        - Använda Azure Policy.

            Med den här metoden använder du den inbyggda principen Azure Policy Deploy Log Analytics agent to Linux or Windows Azure Arc machines (Distribuera [Log Analytics-agenten](../governance/policy/samples/built-in-policies.md#monitoring) till Linux eller Windows Azure Arc-datorer) för att granska om Log Analytics-agenten är installerad på den Arc-aktiverade servern. Om agenten inte är installerad distribueras den automatiskt med hjälp av en reparationsuppgift. Om du planerar att övervaka datorerna med Azure Monitor for VMs använder du i stället initiativet [Aktivera Azure Monitor for VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) för att installera och konfigurera Log Analytics-agenten.

        Vi rekommenderar att du installerar Log Analytics-agenten för Windows eller Linux med Azure Policy.

    > [!NOTE]
    > Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker med Desired State Configuration (DSC) måste du lägga till datorerna som DSC-noder.

    > [!NOTE]
    > [Nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) med motsvarande sudo-behörigheter måste finnas under installationen av Linux-Hybrid Worker. Om du försöker installera arbetsrollen och kontot inte finns eller inte har rätt behörigheter misslyckas installationen.

3. Kontrollera att agenten rapporterar till arbetsytan.

    Log Analytics-agenten för Linux ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbetsyta hämtas automatiskt de komponenter som krävs för Hybrid Runbook Worker.

    När agenten har anslutit till Log Analytics-arbetsytan efter några minuter kan du köra följande fråga för att kontrollera att den skickar pulsslagsdata till arbetsytan.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    I sökresultaten bör du se pulsslagsposter för datorn som anger att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslagspost till den tilldelade arbetsytan.

4. Kör följande kommando för att lägga till datorn Hybrid Runbook Worker en grupp och ange värdena för `-w` parametrarna , `-k` , och `-g` `-e` .

    Du kan hämta den information som krävs för `-k` parametrar och från sidan `-e` **Nycklar** i ditt Automation-konto. Välj **Nycklar** under **avsnittet Kontoinställningar** till vänster på sidan.

    ![Sidan Hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * För `-e` parametern kopierar du värdet för **URL:en**.

    * För `-k` parametern kopierar du värdet för **PRIMÄR ÅTKOMSTNYCKEL**.

    * För `-g` parametern anger du namnet på den Hybrid Runbook Worker som den nya Linux Hybrid Runbook Worker ska ansluta till. Om den här gruppen redan finns i Automation-kontot läggs den aktuella datorn till i den. Om den här gruppen inte finns skapas den med det namnet.

    * För `-w` parametern anger du id:t för Log Analytics-arbetsytan.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Kontrollera distributionen när skriptet har slutförts. På sidan **Hybrid Runbook Worker grupper** i ditt Automation-konto, under fliken **Hybrid Runbook Workers-användare,** visas den nya eller befintliga gruppen och antalet medlemmar. Om det är en befintlig grupp ökas antalet medlemmar. Du kan välja gruppen i listan på sidan. På menyn till vänster väljer du **Hybrid Workers**. På sidan **Hybrid Workers** kan du se varje medlem i gruppen i listan.

    > [!NOTE]
    > Om du använder Log Analytics-tillägget för virtuella datorer för Linux för en virtuell Azure-dator rekommenderar vi att du anger till som automatiskt uppgraderade versioner kan orsaka problem `autoUpgradeMinorVersion` `false` med Hybrid Runbook Worker. Information om hur du uppgraderar tillägget manuellt finns i [Azure CLI-distribution.](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)

## <a name="turn-off-signature-validation"></a>Inaktivera signaturverifiering

Som standard kräver Linux Hybrid Runbook Workers signaturvalidering. Om du kör en osignerad runbook mot en arbetsroll visas ett `Signature validation failed` fel. Om du vill inaktivera signaturverifiering kör du följande kommando. Ersätt den andra parametern med ditt Log Analytics-arbetsyte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker

Du kan använda kommandot på `ls /var/opt/microsoft/omsagent` den Hybrid Runbook Worker för att hämta arbetsytans ID. En mapp skapas med namnet med arbetsytans ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Det här skriptet tar inte bort Log Analytics-agenten för Linux från datorn. Den tar bara bort funktionerna och konfigurationen för Hybrid Runbook Worker rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill Hybrid Runbook Worker en grupp med Linux-datorer använder du samma steg som för en Windows Hybrid Worker-grupp. Se [Ta bort en Hybrid Worker grupp](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar runbooks för att automatisera processer i ditt lokala datacenter eller i en annan molnmiljö finns i [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker Hybrid Runbook Workers finns i [Felsöka Hybrid Runbook Worker – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
