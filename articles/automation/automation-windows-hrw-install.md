---
title: Distribuera en Windows-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du distribuerar Hybrid Runbook Worker som du kan använda för att köra runbooks på Windows-baserade datorer i ditt lokala datacenter eller din molnmiljö.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830348"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda användarfunktionen Hybrid Runbook Worker i Azure Automation köra runbooks direkt på en Azure- eller icke-Azure-dator, inklusive servrar som är [registrerade med Azure Arc aktiverade servrar](../azure-arc/servers/overview.md). Från den dator eller server som är värd för rollen kan du köra runbooks direkt mot den och mot resurser i miljön för att hantera de lokala resurserna.

Azure Automation lagrar och hanterar runbooks och levererar dem sedan till en eller flera angivna datorer. Den här artikeln beskriver hur du distribuerar en Hybrid Runbook Worker på en Windows-dator, hur du tar bort arbetsrollen och hur du tar bort Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker kan du läsa Run [runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) (Kör runbooks på en Hybrid Runbook Worker) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller i en annan molnmiljö.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande innan du börjar.

### <a name="a-log-analytics-workspace"></a>En Log Analytics-arbetsyta

Vilken Hybrid Runbook Worker roll är beroende av en Azure Monitor Log Analytics-arbetsyta för att installera och konfigurera rollen. Du kan skapa den [via Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Om du inte har en Log Analytics Azure Monitor arbetsyta kan du gå [igenom Azure Monitor Log-design](../azure-monitor/logs/design-logs-deployment.md) innan du skapar arbetsytan.

### <a name="log-analytics-agent"></a>Log Analytics-agent

Rollen Hybrid Runbook Worker Log [Analytics-agenten för](../azure-monitor/agents/log-analytics-agent.md) windows-operativsystemet som stöds. För servrar eller datorer som finns utanför Azure kan du installera Log Analytics-agenten med hjälp [Azure Arc aktiverade servrarna](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Windows-operativsystem som stöds

Funktionen Hybrid Runbook Worker stöder följande operativsystem:

* Windows Server 2019 (inklusive Server Core)
* Windows Server 2016, version 1709 och 1803 (exklusive Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inklusive flera sessioner) och Pro
* Windows 8 Enterprise och Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimikrav

Minimikraven för ett Windows-system och Hybrid Runbook Worker är:

* Windows PowerShell 5.1 ([ladda ned WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core stöds inte.
* .NET Framework 4.6.2 eller senare
* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="network-configuration"></a>Konfiguration av nätverk

Nätverkskrav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Lägga till en dator i en Hybrid Runbook Worker grupp

Du kan lägga till arbetsdatorn i en Hybrid Runbook Worker grupp i ett av dina Automation-konton. För datorer som är värdar för systemet Hybrid Runbook Worker som hanteras av Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker gruppmedlemskap.

>[!NOTE]
>Azure Automation [Uppdateringshantering](./update-management/overview.md) installerar automatiskt systemet Hybrid Runbook Worker på en Azure- eller icke-Azure-dator som är aktiverad för Uppdateringshantering. Den här arbetaren är dock inte registrerad med några Hybrid Runbook Worker grupper i ditt Automation-konto. Om du vill köra dina runbooks på dessa datorer måste du lägga till dem i en Hybrid Runbook Worker grupp. Följ steg 6 under avsnittet Manuell [distribution för att](#manual-deployment) lägga till den i en grupp.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Aktivera för hantering med Azure Automation State Configuration

Information om hur du aktiverar datorer för hantering med Azure Automation State Configuration finns [i Aktivera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker med Desired State Configuration (DSC) måste du lägga till datorerna som DSC-noder.

## <a name="installation-options"></a>Installationsalternativ

Om du vill installera och konfigurera Hybrid Runbook Worker Windows-användare kan du använda någon av följande metoder.

* Använd ett tillhandahållet PowerShell-skript för [att helt automatisera](#automated-deployment) processen med att konfigurera en eller flera Windows-datorer. Det här är den rekommenderade metoden för datorer i ditt datacenter eller en annan molnmiljö.
* Importera Automation-lösningen manuellt, installera Log Analytics-agenten för Windows och konfigurera arbetsrollen på datorn.

## <a name="automated-deployment"></a>Automatiserad distribution

Det finns två metoder för att automatiskt distribuera en Hybrid Runbook Worker. Du kan importera en runbook från Runbook-galleriet i Azure Portal och köra den, eller så kan du manuellt ladda ned ett skript från PowerShell-galleriet.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importera en runbook från Runbook-galleriet

Importproceduren beskrivs i detalj i [Importera runbooks från GitHub med Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Namnet på den runbook som ska importeras är **Create Automation Windows HybridWorker**.

Runbooken använder följande parametrar.

| Parameter | Status | Beskrivning |
| ------- | ----- | ----------- |
| `Location` | Obligatorisk | Platsen för Log Analytics-arbetsytan. |
| `ResourceGroupName` | Obligatorisk | Resursgruppen för ditt Automation-konto. |
| `AccountName` | Obligatorisk | Automation-kontonamnet som Hybrid Run Worker ska registreras i. |
| `CreateLA` | Obligatorisk | Om värdet är true används värdet för för `WorkspaceName` för att skapa en Log Analytics-arbetsyta. Om värdet är false måste värdet `WorkspaceName` för referera till en befintlig arbetsyta. |
| `LAlocation` | Valfritt | Den plats där Log Analytics-arbetsytan kommer att skapas eller där den redan finns. |
| `WorkspaceName` | Valfritt | Namnet på Log Analytics-arbetsytan som ska användas. |
| `CreateVM` | Obligatorisk | Om värdet är true använder du värdet `VMName` för som namn på en ny virtuell dator. Om det är falskt använder du `VMName` för att hitta och registrera en befintlig virtuell dator. |
| `VMName` | Valfritt | Namnet på den virtuella dator som antingen skapas eller registreras, beroende på värdet för `CreateVM` . |
| `VMImage` | Valfritt | Namnet på den virtuella datoravbildning som ska skapas. |
| `VMlocation` | Valfritt | Platsen för den virtuella dator som antingen har skapats eller registrerats. Om den här platsen inte anges används `LAlocation` värdet för . |
| `RegisterHW` | Obligatorisk | Om det är sant registrerar du den virtuella datorn som hybridarbetare. |
| `WorkerGroupName` | Obligatorisk | Namnet på Hybrid Worker grupp. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Ladda ned ett skript från PowerShell-galleriet

Den här automatiserade distributionsmetoden  använder PowerShell-New-OnPremiseHybridWorker.ps1för att automatisera och konfigurera Windows Hybrid Runbook Worker rollen. Den utför följande:

* Installerar de nödvändiga modulerna
* Loggar in med ditt Azure-konto
* Verifierar att det finns en angiven resursgrupp och ett Automation-konto
* Skapar referenser till Automation-kontoattribut
* Skapar en Azure Monitor Log Analytics-arbetsyta om inget annat anges
* Aktivera Azure Automation på arbetsytan
* Ladda ned och installera Log Analytics-agenten för Windows
* Registrera datorn som Hybrid Runbook Worker

Utför följande steg för att installera rollen på din Windows-dator med hjälp av skriptet.

1. Ladda ned **New-OnPremiseHybridWorker.ps1-skriptet** från [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). När du har laddat ned skriptet kopierar eller kör du det på måldatorn. Skriptet använder följande parametrar.

    | Parameter | Status | Beskrivning |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obligatorisk | Namnet på den resursgrupp som är associerad med ditt Automation-konto. |
    | `AutomationAccountName` | Obligatorisk | Namnet på ditt Automation-konto.
    | `Credential` | Valfritt | De autentiseringsuppgifter som ska användas vid inloggning i Azure-miljön. |
    | `HybridGroupName` | Obligatorisk | Namnet på en Hybrid Runbook Worker som du anger som mål för de runbooks som har stöd för det här scenariot. |
    | `OMSResourceGroupName` | Valfritt | Namnet på resursgruppen för Log Analytics-arbetsytan. Om den här resursgruppen inte anges används `AAResourceGroupName` värdet för . |
    | `SubscriptionID` | Obligatorisk | Identifieraren för den Azure-prenumeration som är associerad med ditt Automation-konto. |
    | `TenantID` | Valfritt | Identifierare för klientorganisationen som är associerad med ditt Automation-konto. |
    | `WorkspaceName` | Valfritt | Log Analytics-arbetsytans namn. Om du inte har en Log Analytics-arbetsyta skapar och konfigurerar skriptet en. |

1. Öppna en upphöjd 64-bitars PowerShell-kommandotolk.

1. Från PowerShell-kommandotolken bläddrar du till mappen som innehåller skriptet som du laddade ned. Ändra värdena för `AutomationAccountName` parametrarna , `AAResourceGroupName` , , , , och `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Kör sedan skriptet.

    Du uppmanas att autentisera med Azure när du har kört skriptet. Du måste logga in med ett konto som är medlem i rollen **Prenumerationsadministratörer** och vara medadministratör för prenumerationen.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. Du uppmanas att godkänna att installera NuGet och autentisera med dina autentiseringsuppgifter för Azure. Om du inte har den senaste NuGet-versionen kan du ladda ned den från [Tillgängliga NuGet-distributionsversioner.](https://www.nuget.org/downloads)

1. Kontrollera distributionen när skriptet har slutförts. På sidan **Hybrid Runbook Worker grupper** i ditt Automation-konto, under fliken **Hybrid Runbook Workers-användare,** visas den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökas antalet medlemmar. Du kan välja gruppen i listan på sidan. På menyn till vänster väljer du **Hybrid Workers** . På sidan **Hybrid Workers** kan du se varje medlem i gruppen i listan.

## <a name="manual-deployment"></a>Manuell distribution

Utför följande steg för att installera Hybrid Runbook Worker konfigurera en Windows-Hybrid Runbook Worker.

1. Aktivera Azure Automation i Log Analytics-arbetsytan genom att köra följande kommando i en upphöjd PowerShell-kommandotolk eller i Cloud Shell i [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Distribuera Log Analytics-agenten till måldatorn.

    * För virtuella Azure-datorer installerar du Log Analytics-agenten för Windows med hjälp [av tillägget för virtuella datorer för Windows.](../virtual-machines/extensions/oms-windows.md) Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar virtuella datorer på en befintlig Log Analytics-arbetsyta. Du kan använda en Azure Resource Manager, PowerShell eller Azure Policy tilldela den inbyggda principen Distribuera Log Analytics-agenten för Linux eller virtuella [  *Windows-datorer.*](../governance/policy/samples/built-in-policies.md#monitoring) När agenten har installerats kan datorn läggas till i en Hybrid Runbook Worker i ditt Automation-konto.
    
    * För datorer som inte är Azure-datorer kan du installera Log Analytics-agenten [med Azure Arc aktiverade servrar](../azure-arc/servers/overview.md). Arc-aktiverade servrar stöder distribution av Log Analytics-agenten med hjälp av följande metoder:
    
        - Använda ramverket för VM-tillägg.
        
            Med den här funktionen Azure Arc-aktiverade servrar kan du distribuera Log Analytics-agentens VM-tillägg till en icke-Azure Windows- och/eller Linux-server. VM-tillägg kan hanteras med följande metoder på dina hybriddatorer eller servrar som hanteras av Arc-aktiverade servrar:
        
            - Den [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager mallar](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Använda Azure Policy.
        
            Med den här metoden använder du den inbyggda principen Azure Policy Deploy Log Analytics agent to Linux or Windows Azure Arc machines built-in policy (Distribuera [Log Analytics-agenten](../governance/policy/samples/built-in-policies.md#monitoring) till Linux eller Windows Azure Arc) för att granska om Den Arc-aktiverade servern har Log Analytics-agenten installerad. Om agenten inte är installerad distribueras den automatiskt med hjälp av en reparationsuppgift. Om du planerar att övervaka datorerna med Azure Monitor for VMs använder du i stället initiativet [Aktivera Azure Monitor for VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) för att installera och konfigurera Log Analytics-agenten.

    Vi rekommenderar att du installerar Log Analytics-agenten för Windows eller Linux med Azure Policy.

1. Kontrollera att agenten rapporterar till arbetsytan

    Log Analytics-agenten för Windows ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbetsyta hämtas automatiskt de komponenter som krävs för Hybrid Runbook Worker.

    När agenten har anslutit till Log Analytics-arbetsytan efter några minuter kan du köra följande fråga för att kontrollera att den skickar pulsslagsdata till arbetsytan.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    I sökresultaten bör du se pulsslagsposter för datorn som anger att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslagspost till den tilldelade arbetsytan. Utför agentinstallationen och installationen genom att följa stegen nedan.

1. Bekräfta versionen av den Hybrid Runbook Worker datorn som är värd för Log Analytics-agenten, bläddra `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` till och notera **versionsundermappen.** Den här mappen visas på datorn flera minuter efter att lösningen har aktiverats på arbetsytan.

1. Installera Runbook-miljön och anslut till Azure Automation. När du konfigurerar en agent för att  rapportera till en Log Analytics-arbetsyta och importerar Automation-lösningen, push-lar lösningen `HybridRegistration` ned PowerShell-modulen. Den här modulen innehåller `Add-HybridRunbookWorker` cmdleten . Använd denna cmdlet för att installera Runbook-miljön på datorn och registrera den med Azure Automation.

    Öppna en PowerShell-session i administratörsläge och kör följande kommandon för att importera modulen.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Kör `Add-HybridRunbookWorker` cmdleten och ange värdena för `Url` parametrarna `Key` , och `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Du kan hämta den information som krävs för `Url` parametrarna `Key` och från sidan **Nycklar** i ditt Automation-konto. Välj **Nycklar** under **avsnittet Kontoinställningar** till vänster på sidan.

    ![Sidan Hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * För `Url` parametern kopierar du värdet för **URL:en**.

    * För `Key` parametern kopierar du värdet för **PRIMÄR ÅTKOMSTNYCKEL.**

    * För `GroupName` parametern använder du namnet på den Hybrid Runbook Worker gruppen. Om den här gruppen redan finns i Automation-kontot läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.

    * Om det behövs anger du `Verbose` parametern för att ta emot information om installationen.

1. Kontrollera distributionen när kommandot har slutförts. På **sidan Hybrid Runbook Worker grupper** i ditt Automation-konto, under fliken **Hybrid Runbook Workers-användare,** visas den nya eller befintliga gruppen och antalet medlemmar. Om det är en befintlig grupp ökas antalet medlemmar. Du kan välja gruppen i listan på sidan. På menyn till vänster väljer du **Hybrid Workers**. På sidan **Hybrid Workers** kan du se varje medlem i gruppen i listan.

## <a name="install-powershell-modules"></a>Installera PowerShell-moduler

Runbooks kan använda alla aktiviteter och cmdlets som definierats i modulerna som är installerade i din Azure Automation miljö. Eftersom de här modulerna inte distribueras automatiskt till lokala datorer måste du installera dem manuellt. Undantaget är Azure-modulen. Den här modulen installeras som standard och ger åtkomst till cmdlets för alla Azure-tjänster och -aktiviteter för Azure Automation.

Eftersom det primära syftet med Hybrid Runbook Worker är att hantera lokala resurser behöver du troligen installera modulerna som stöder dessa resurser, särskilt `PowerShellGet` modulen. Information om hur du installerar Windows PowerShell moduler finns i [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduler som är installerade måste finnas på en plats som miljövariabeln refererar `PSModulePath` till så att Hybrid Worker kan importera dem automatiskt. Mer information finns i [Installera moduler i PSModulePath.](/powershell/scripting/developer/module/installing-a-powershell-module)

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker

1. I Azure Portal du till ditt Automation-konto.

1. Under **Kontoinställningar** väljer du **Nycklar** och noterar värdena för **URL och** Primär **åtkomstnyckel.**

1. Öppna en PowerShell-session i administratörsläge och kör följande kommando med dina URL-adresser och värden för primär åtkomstnyckel. Använd `Verbose` parametern för en detaljerad logg över borttagningsprocessen. Om du vill ta bort inaktuella datorer Hybrid Worker din grupp använder du den valfria `machineName` parametern .

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill Hybrid Runbook Worker en grupp måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen. Använd sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure Portal.

1. Välj **Hybrid Worker-grupper** under **Processautomatisering.** Välj den grupp som du vill ta bort. Egenskapssidan för den gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. På egenskapssidan för den valda gruppen väljer du Ta **bort**. Ett meddelande uppmanar dig att bekräfta den här åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller i en annan molnmiljö finns i [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker Hybrid Runbook Workers finns i [Felsöka Hybrid Runbook Worker problem.](troubleshoot/hybrid-runbook-worker.md#general)
