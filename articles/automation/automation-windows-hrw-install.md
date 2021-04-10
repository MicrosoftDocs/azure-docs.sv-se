---
title: Distribuera en Windows-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du distribuerar en Hybrid Runbook Worker som du kan använda för att köra Runbooks på Windows-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.openlocfilehash: 0a28266210fd8b6f0b731b972f00aa3d413c0d0c
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027745"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda användar Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på en Azure-eller icke-Azure-dator, inklusive servrar som är registrerade med [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md). Från datorn eller servern som är värd för rollen kan du köra Runbooks direkt mot den och mot resurser i miljön för att hantera de lokala resurserna.

Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera angivna datorer. I den här artikeln beskrivs hur du distribuerar en användare Hybrid Runbook Worker på en Windows-dator, hur du tar bort en arbets grupp och hur du tar bort en Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har följande innan du börjar.

### <a name="a-log-analytics-workspace"></a>En Log Analytics arbets yta

Hybrid Runbook Worker-rollen beror på en Azure Monitor Log Analytics arbets yta för att installera och konfigurera rollen. Du kan skapa den genom [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Om du inte har en Azure Monitor Log Analytics arbets yta granskar du [rikt linjerna för Azure Monitor logg design](../azure-monitor/logs/design-logs-deployment.md) innan du skapar arbets ytan.

### <a name="log-analytics-agent"></a>Log Analytics-agent

Hybrid Runbook Worker-rollen kräver att [Log Analytics agenten](../azure-monitor/agents/log-analytics-agent.md) för det Windows-operativsystem som stöds. För servrar eller datorer som ligger utanför Azure kan du installera Log Analytics-agenten med hjälp av [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Windows operativ system som stöds

Funktionen Hybrid Runbook Worker stöder följande operativ system:

* Windows Server 2019 (inklusive Server Core)
* Windows Server 2016, version 1709 och 1803 (exklusive Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inklusive multi-session) och Pro
* Windows 8 Enterprise och Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimikrav

Minimi kraven för ett Windows-system och användar Hybrid Runbook Worker:

* Windows PowerShell 5,1 ([Hämta WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core stöds inte.
* .NET Framework 4.6.2 eller senare
* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="network-configuration"></a>Konfiguration av nätverk

Nätverks krav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Lägga till en dator i en Hybrid Runbook Worker grupp

Du kan lägga till arbets datorn i en Hybrid Runbook Worker grupp i ett av dina Automation-konton. För datorer som är värdar för system hybrid Runbook Worker som hanteras av Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker grupp medlemskapet.

>[!NOTE]
>Azure Automation [uppdateringshantering](./update-management/overview.md) installerar automatiskt system hybrid Runbook Worker på en Azure-eller icke-Azure-dator som är aktive rad för uppdateringshantering. Den här arbetaren är dock inte registrerad med några Hybrid Runbook Worker grupper i ditt Automation-konto. Om du vill köra Runbooks på dessa datorer måste du lägga till dem i en Hybrid Runbook Worker grupp. Följ steg 6 under avsnittet [manuell distribution](#manual-deployment) för att lägga till det i en grupp.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Aktivera för hantering med Azure Automation tillstånds konfiguration

Information om hur du aktiverar datorer för hantering med Azure Automation tillstånds konfiguration finns i [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

> [!NOTE]
> Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC) måste du lägga till datorerna som DSC-noder.

## <a name="installation-options"></a>Installationsalternativ

Om du vill installera och konfigurera en Windows-användare Hybrid Runbook Worker kan du använda någon av följande metoder.

* Använd ett angivet PowerShell-skript för att helt [Automatisera](#automated-deployment) processen med att konfigurera en eller flera Windows-datorer. Detta är den rekommenderade metoden för datorer i ditt data Center eller en annan moln miljö.
* Importera Automation-lösningen manuellt, installera Log Analytics-agenten för Windows och konfigurera arbets rollen på datorn.

## <a name="automated-deployment"></a>Automatiserad distribution

Det finns två metoder för att automatiskt distribuera en Hybrid Runbook Worker. Du kan importera en Runbook från Runbook-galleriet i Azure Portal och köra den, eller så kan du manuellt hämta ett skript från PowerShell-galleriet.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importera en Runbook från Runbook-galleriet

Import proceduren beskrivs i detalj i [importera runbooks från GitHub med Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Namnet på den Runbook som ska importeras är **skapa Automation Windows-HybridWorker**.

Runbooken använder följande parametrar.

| Parameter | Status | Beskrivning |
| ------- | ----- | ----------- |
| `Location` | Obligatorisk | Platsen för Log Analytics arbets ytan. |
| `ResourceGroupName` | Obligatorisk | Resurs gruppen för ditt Automation-konto. |
| `AccountName` | Obligatorisk | Namnet på Automation-kontot där hybrid körnings arbets tagaren ska registreras. |
| `CreateLA` | Obligatorisk | Om värdet är true, använder värdet för `WorkspaceName` för att skapa en Log Analytics-arbetsyta. Om värdet är false måste värdet för `WorkspaceName` referera till en befintlig arbets yta. |
| `LAlocation` | Valfritt | Den plats där Log Analytics arbets ytan kommer att skapas eller där den redan finns. |
| `WorkspaceName` | Valfritt | Namnet på Log Analytics arbets ytan som ska användas. |
| `CreateVM` | Obligatorisk | Om det här värdet är sant används värdet `VMName` som namn på en ny virtuell dator. Om det är falskt använder `VMName` du för att hitta och registrera en befintlig virtuell dator. |
| `VMName` | Valfritt | Namnet på den virtuella dator som antingen har skapats eller registrerats, beroende på värdet för `CreateVM` . |
| `VMImage` | Valfritt | Namnet på den virtuella dator avbildning som ska skapas. |
| `VMlocation` | Valfritt | Platsen för den virtuella dator som antingen har skapats eller registrerats. Om den här platsen inte anges används värdet för `LAlocation` . |
| `RegisterHW` | Obligatorisk | Om det här värdet är sant registrerar du den virtuella datorn som en hybrid Worker. |
| `WorkerGroupName` | Obligatorisk | Namnet på Hybrid Worker gruppen. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Hämta ett skript från PowerShell-galleriet

Den här automatiserade distributions metoden använder PowerShell-skriptet **New-OnPremiseHybridWorker.ps1** för att automatisera och konfigurera Windows hybrid Runbook Worker-rollen. Den utför följande:

* Installerar nödvändiga moduler
* Loggar in med ditt Azure-konto
* Kontrollerar att den angivna resurs gruppen och automation-kontot finns
* Skapar referenser till Automation-kontots attribut
* Skapar en Azure Monitor Log Analytics arbets yta om inget anges
* Aktivera Azure Automation-lösningen i arbets ytan
* Hämta och installera Log Analytics agent för Windows
* Registrera datorn som Hybrid Runbook Worker

Utför följande steg för att installera rollen på din Windows-dator med hjälp av skriptet.

1. Ladda ned **New-OnPremiseHybridWorker.ps1** -skriptet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). När du har hämtat skriptet kopierar du eller kör det på mål datorn. Skriptet använder följande parametrar.

    | Parameter | Status | Beskrivning |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obligatorisk | Namnet på resurs gruppen som är kopplad till ditt Automation-konto. |
    | `AutomationAccountName` | Obligatorisk | Namnet på ditt Automation-konto.
    | `Credential` | Valfritt | De autentiseringsuppgifter som ska användas när du loggar in i Azure-miljön. |
    | `HybridGroupName` | Obligatorisk | Namnet på en Hybrid Runbook Worker grupp som du anger som mål för de Runbooks som har stöd för det här scenariot. |
    | `OMSResourceGroupName` | Valfritt | Namnet på resurs gruppen för Log Analytics arbets ytan. Om den här resurs gruppen inte anges används värdet för `AAResourceGroupName` . |
    | `SubscriptionID` | Obligatorisk | Identifieraren för den Azure-prenumeration som är kopplad till ditt Automation-konto. |
    | `TenantID` | Valfritt | Identifieraren för den klient organisation som är kopplad till ditt Automation-konto. |
    | `WorkspaceName` | Valfritt | Namnet på Log Analytics arbets ytan. Om du inte har en Log Analytics arbets yta, skapar skriptet och konfigurerar ett. |

1. Öppna en upphöjd 64-bitars PowerShell-kommandotolk.

1. Från PowerShell-Kommandotolken bläddrar du till den mapp som innehåller skriptet som du laddade ned. Ändra värdena för parametrarna,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` och `WorkspaceName` . Kör sedan skriptet.

    Du uppmanas att autentisera med Azure när du har kört skriptet. Du måste logga in med ett konto som är medlem i rollen **prenumerations administratörer** och som är medadministratör för prenumerationen.

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

1. Du uppmanas att godkänna att installera NuGet och att autentisera med dina Azure-autentiseringsuppgifter. Om du inte har den senaste versionen av NuGet kan du ladda ned den från [tillgängliga NuGet-distributions versioner](https://www.nuget.org/downloads).

1. Verifiera distributionen när skriptet har körts. På sidan **hybrid Runbook Worker grupper** i ditt Automation-konto, på fliken **användare hybrid Runbook Worker-grupp** , visas den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen från listan på sidan genom att välja **hybrid arbetare** på menyn till vänster. På sidan **hybrid arbetare** kan du se varje medlem i gruppen i listan.

## <a name="manual-deployment"></a>Manuell distribution

Utför följande steg för att installera och konfigurera en Windows-Hybrid Runbook Worker.

1. Aktivera Azure Automation lösning på arbets ytan Log Analytics genom att köra följande kommando i en upphöjd PowerShell-kommandotolk eller i Cloud Shell i [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Distribuera Log Analytics-agenten till mål datorn.

    * För virtuella Azure-datorer installerar du Log Analytics agent för Windows med hjälp av [tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md). Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics-arbetsyta. Du kan använda en Azure Resource Manager mall, PowerShell eller Azure Policy för att tilldela en inbyggd princip [för att distribuera Log Analytics agent för virtuella *Linux* -eller *Windows* -datorer](../governance/policy/samples/built-in-policies.md#monitoring) . När agenten har installerats kan datorn läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.
    
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

1. Verifiera att agenten rapporterar till arbets ytan

    Log Analytics-agenten för Windows ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

    När agenten har anslutit till Log Analytics arbets ytan efter några minuter kan du köra följande fråga för att kontrol lera att den skickar pulsslags data till arbets ytan.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    I Sök resultaten bör du se pulsslags poster för datorn, vilket indikerar att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslags post till den tilldelade arbets ytan. Använd följande steg för att slutföra Agent installationen och installationen.

1. Bekräfta versionen av Hybrid Runbook Worker på den dator som är värd för Log Analytics agent, bläddra till `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` och anteckna i undermappen **version** . Den här mappen visas på datorn flera minuter efter det att lösningen har Aktiver ATS i arbets ytan.

1. Installera Runbook-miljön och Anslut till Azure Automation. När du konfigurerar en agent så att den rapporterar till en Log Analytics arbets yta och importerar **Automation** -lösningen, pushas lösningen nedåt i `HybridRegistration` PowerShell-modulen. Den här modulen innehåller `Add-HybridRunbookWorker` cmdleten. Använd denna cmdlet för att installera Runbook-miljön på datorn och registrera den med Azure Automation.

    Öppna en PowerShell-session i administratörs läge och kör följande kommandon för att importera modulen.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Kör `Add-HybridRunbookWorker` cmdleten och ange värdena för parametrarna `Url` , `Key` och `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Du kan hämta den information som krävs för parametrarna `Url` och `Key` från sidan **nycklar** i ditt Automation-konto. Välj **nycklar** under avsnittet **konto inställningar** på sidans vänstra sida.

    ![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `Url`Kopiera värdet för **URL** för parametern.

    * För `Key` parametern kopierar du värdet för **primär åtkomst nyckel**.

    * För `GroupName` parametern använder du namnet på Hybrid Runbook Workers gruppen. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.

    * Om det behövs anger du `Verbose` parametern för att få information om installationen.

1. Verifiera distributionen när kommandot har slutförts. På sidan **hybrid Runbook Worker grupper** i ditt Automation-konto, på fliken **användare hybrid Runbook Worker-grupp** , visas den nya eller befintliga gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen från listan på sidan genom att välja **hybrid arbetare** på menyn till vänster. På sidan **hybrid arbetare** kan du se varje medlem i gruppen i listan.

## <a name="install-powershell-modules"></a>Installera PowerShell-moduler

Runbooks kan använda alla aktiviteter och cmdletar som definierats i modulerna som är installerade i din Azure Automation-miljö. Eftersom dessa moduler inte distribueras automatiskt till lokala datorer måste du installera dem manuellt. Undantaget är Azure-modulen. Den här modulen installeras som standard och ger åtkomst till cmdletar för alla Azure-tjänster och-aktiviteter för Azure Automation.

Eftersom det primära syftet med Hybrid Runbook Worker är att hantera lokala resurser, behöver du förmodligen installera modulerna som stöder dessa resurser, särskilt `PowerShellGet` modulen. Information om hur du installerar Windows PowerShell-moduler finns i [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduler som är installerade måste finnas på en plats som refereras av `PSModulePath` miljövariabeln så att hybrid Worker kan importera dem automatiskt. Mer information finns [i installera moduler i PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker

1. I Azure Portal går du till ditt Automation-konto.

1. Under **konto inställningar** väljer du **nycklar** och noterar värdena för **URL** och **primär åtkomst nyckel**.

1. Öppna en PowerShell-session i administratörs läge och kör följande kommando med URL: en och primär åtkomst nyckel värden. Använd `Verbose` parametern för en detaljerad logg över borttagnings processen. Om du vill ta bort inaktuella datorer från Hybrid Worker gruppen använder du den valfria `machineName` parametern.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en Hybrid Runbook Worker grupp måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen. Använd sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure Portal.

1. Välj **hybrid Worker-grupper** under **process automatisering**. Välj den grupp som du vill ta bort. Sidan Egenskaper för gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. På egenskaps sidan för den valda gruppen väljer du **ta bort**. Ett meddelande som uppmanar dig att bekräfta åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem](troubleshoot/hybrid-runbook-worker.md#general).
