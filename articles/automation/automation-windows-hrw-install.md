---
title: Distribuera en Windows-Hybrid Runbook Worker i Azure Automation
description: Den här artikeln beskriver hur du distribuerar en Hybrid Runbook Worker som du kan använda för att köra Runbooks på Windows-baserade datorer i ditt lokala data Center eller i moln miljön.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 90b16f10cfa94ce427a9f6249c72842f8ef6278a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270588"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuera en Windows-Hybrid Runbook Worker

Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera angivna datorer. Den här artikeln beskriver hur du distribuerar en Hybrid Runbook Worker på en Windows-dator, hur du tar bort en arbets grupp och hur du tar bort en Hybrid Runbook Worker grupp.

När du har distribuerat en Runbook Worker granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

## <a name="prerequisites"></a>Krav

Kontrol lera att du har följande innan du börjar.

### <a name="a-log-analytics-workspace"></a>En Log Analytics arbets yta

Hybrid Runbook Worker-rollen beror på en Azure Monitor Log Analytics arbets yta för att installera och konfigurera rollen. Du kan skapa den genom [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)eller i [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Om du inte har en Azure Monitor Log Analytics arbets yta granskar du [rikt linjerna för Azure Monitor logg design](../azure-monitor/platform/design-logs-deployment.md) innan du skapar arbets ytan.

Om du har en arbets yta, men inte är länkad till ditt Automation-konto, så lägger en automatiserings funktion till funktioner för Azure Automation, inklusive stöd för Hybrid Runbook Worker. När du aktiverar någon av de Azure Automation funktionerna i Log Analytics-arbetsytan, specifikt [uppdateringshantering](update-management/update-mgmt-overview.md) eller [ändringsspårning och inventering](change-tracking.md), flyttas Worker-komponenterna automatiskt till agent datorn.

   Om du vill lägga till funktionen Uppdateringshantering i din arbets yta kör du följande PowerShell-cmdlet:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Om du vill lägga till funktionen Ändringsspårning och inventering i arbets ytan kör du följande PowerShell-cmdlet:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics-agent

Hybrid Runbook Worker-rollen kräver att [Log Analytics agenten](../azure-monitor/platform/log-analytics-agent.md) för det Windows-operativsystem som stöds.

### <a name="supported-windows-operating-system"></a>Windows operativ system som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-Hybrid Runbook Worker:

* Windows Server 2019
* Windows Server 2016, version 1709 och 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inklusive multi-session) och Pro
* Windows 8 Enterprise och Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimikrav

Minimi kraven för en Windows-Hybrid Runbook Worker är:

* Windows PowerShell 5,1 eller senare ([Hämta WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 eller senare
* Två kärnor
* 4 GB RAM
* Port 443 (utgående)

### <a name="network-configuration"></a>Konfiguration av nätverk

Information om hur du får fler nätverks krav för Hybrid Runbook Worker finns i [Konfigurera nätverket](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Lägga till en dator i en Hybrid Runbook Worker grupp

Du kan lägga till arbets datorn i en Hybrid Runbook Worker grupp i ditt Automation-konto. Observera att du måste ha stöd för Automation-runbooks så länge du använder samma konto för både Azure Automation-funktionen och Hybrid Runbook Workers grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

>[!NOTE]
>När du aktiverar Azure Automation [uppdateringshantering](update-management/update-mgmt-overview.md) konfigureras automatiskt en Windows-dator som är ansluten till din Log Analytics arbets yta som en hybrid Runbook Worker för att stödja hanteringen av sina operativ system uppdateringar. Den här arbetaren är dock inte registrerad med Hybrid Runbook Worker grupper som redan har definierats i ditt Automation-konto.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Aktivera datorer för hantering med Azure Automation tillstånds konfiguration

Information om hur du aktiverar datorer för hantering med Azure Automation tillstånds konfiguration finns i [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

> [!NOTE]
> Om du vill hantera konfigurationen av datorer som stöder Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC) måste du lägga till datorerna som DSC-noder.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Installations alternativ för Windows Hybrid Runbook Worker

Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker kan du använda någon av följande metoder.

* För virtuella Azure-datorer installerar du Log Analytics agent för Windows med hjälp av [tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md). Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics arbets yta med en Azure Resource Manager mall eller PowerShell. När agenten har installerats kan den virtuella datorn läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.

* För virtuella datorer som inte är Azure-datorer installerar du Log Analytics agent för Windows med hjälp av de distributions alternativ som beskrivs i artikeln [Anslut Windows-datorer till Azure Monitor](../azure-monitor/platform/agent-windows.md) . Du kan upprepa den här processen för flera datorer för att lägga till flera arbetare i din miljö. När agenten har installerats kan de virtuella datorerna läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto.

* Använd ett angivet PowerShell-skript för att helt [Automatisera](#automated-deployment) processen med att konfigurera en eller flera Windows-datorer. Detta är den rekommenderade metoden för datorer i ditt data Center eller en annan moln miljö.

## <a name="automated-deployment"></a>Automatiserad distribution

Utför följande steg på mål datorn för att automatisera installationen och konfigurationen av Windows Hybrid Worker-rollen med hjälp av PowerShell-skriptet **New-OnPremiseHybridWorker.ps1**. Skriptet utför följande steg:

* Installerar nödvändiga moduler
* Loggar in med ditt Azure-konto
* Kontrollerar att den angivna resurs gruppen och automation-kontot finns
* Skapar referenser till Automation-kontots attribut
* Skapar en Azure Monitor Log Analytics arbets yta om inget anges
* Aktivera Azure Automation-lösningen i arbets ytan
* Hämta och installera Log Analytics agent för Windows
* Registrera datorn som Hybrid Runbook Worker

### <a name="step-1---download-the-powershell-script"></a>Steg 1 – Ladda ned PowerShell-skriptet

Ladda ned **New-OnPremiseHybridWorker.ps1** -skriptet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). När du har hämtat skriptet kopierar du eller kör det på mål datorn. **New-OnPremiseHybridWorker.ps1** -skriptet använder de parametrar som beskrivs nedan under körningen.

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

> [!NOTE]
> När du aktiverar funktioner stöder Azure Automation bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Steg 2 – öppna Windows PowerShell kommando rads gränssnitt

Från **Start-menyn** klickar du på **Start**, skriver **PowerShell**, högerklickar på **Windows PowerShell**och klickar sedan på **Kör som administratör**.

### <a name="step-3---run-the-powershell-script"></a>Steg 3 – kör PowerShell-skriptet

I kommando rads gränssnittet för PowerShell bläddrar du till den mapp som innehåller skriptet som du har laddat ned. Ändra värdena för parametrarna,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` och `WorkspaceName` . Kör sedan skriptet.

Du uppmanas att autentisera med Azure när du har kört skriptet. Du måste logga in med ett konto som är medlem i rollen prenumerations administratörer och som är medadministratör för prenumerationen.

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

### <a name="step-4---install-nuget"></a>Steg 4 – installera NuGet

Du uppmanas att godkänna att installera NuGet och att autentisera med dina Azure-autentiseringsuppgifter. Om du inte har den senaste versionen av NuGet kan du ladda ned den från [tillgängliga NuGet-distributions versioner](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Steg 5 – kontrol lera distributionen

När skriptet är klart visar sidan Hybrid Worker grupper i ditt Automation-konto den nya gruppen och antalet medlemmar. Om det är en befintlig grupp ökar antalet medlemmar. Du kan välja gruppen från listan på sidan Hybrid Worker grupper och välja panelen **hybrid personal** . På sidan hybrid arbetare kan du se varje medlem i gruppen i listan.

## <a name="manual-deployment"></a>Manuell distribution

Utför följande steg för att installera och konfigurera en Windows-Hybrid Runbook Worker.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Steg 1 – Verifiera att agenten rapporterar till arbets ytan

Log Analytics-agenten för Windows ansluter datorer till en Azure Monitor Log Analytics-arbetsyta. När du installerar agenten på datorn och ansluter den till din arbets yta laddar den automatiskt ned de komponenter som krävs för Hybrid Runbook Worker.

När agenten har anslutit till Log Analytics arbets ytan efter några minuter kan du köra följande fråga för att kontrol lera att den skickar pulsslags data till arbets ytan.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

I Sök resultaten bör du se pulsslags poster för datorn, vilket indikerar att den är ansluten och rapporterar till tjänsten. Som standard vidarebefordrar varje agent en pulsslags post till den tilldelade arbets ytan. Använd följande steg för att slutföra Agent installationen och installationen.

1. Aktivera funktionen för att lägga till agent datorn. För Uppdateringshantering och virtuella Azure-datorer, se [aktivera uppdateringshantering från ett Automation-konto](update-management/update-mgmt-enable-automation-account.md), [Aktivera uppdateringshantering genom att bläddra bland Azure Portal](update-management/update-mgmt-enable-portal.md), [Aktivera uppdateringshantering från en Runbook](update-management/update-mgmt-enable-runbook.md)eller [Aktivera uppdateringshantering från en virtuell Azure-dator](update-management/update-mgmt-enable-vm.md). För Ändringsspårning och virtuella Azure-datorer, se [Aktivera virtuella Azure-datorer](automation-enable-changes-from-auto-acct.md#enable-azure-vms)och för icke-virtuella datorer i Azure se [Aktivera datorer i arbets ytan](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Om du vill bekräfta versionen av Hybrid Runbook Worker bläddrar du till `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` och noterar undermappen **version** .

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Steg 2 – Installera Runbook-miljön och Anslut till Azure Automation

När du konfigurerar en agent att rapportera till en Log Analytics arbets yta, pushas den Azure Automation-funktionen nedåt i `HybridRegistration` PowerShell-modulen, som innehåller `Add-HybridRunbookWorker` cmdleten. Använd denna cmdlet för att installera Runbook-miljön på datorn och registrera den med Azure Automation.

Öppna en PowerShell-session i administratörs läge och kör följande kommandon för att importera modulen.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Kör nu `Add-HybridRunbookWorker` cmdleten med hjälp av följande syntax.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Du kan hämta den information som krävs för parametrarna `Url` och `Key` från sidan **nycklar** i ditt Automation-konto. Välj **nycklar** under avsnittet **konto inställningar** på sidans vänstra sida.

![Sidan hantera nycklar](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* `Url`Kopiera värdet för **URL**för parametern.

* För `Key` parametern kopierar du värdet för **primär åtkomst nyckel**.

* För `GroupName` parametern använder du namnet på Hybrid Runbook Workers gruppen. Om den här gruppen redan finns i Automation-kontot, läggs den aktuella datorn till i den. Om den här gruppen inte finns läggs den till.

* Om det behövs anger du `Verbose` parametern för att få information om installationen.

### <a name="step-3----install-powershell-modules"></a>Steg 3 – Installera PowerShell-moduler

Runbooks kan använda alla aktiviteter och cmdletar som definierats i modulerna som är installerade i din Azure Automation-miljö. Eftersom dessa moduler inte distribueras automatiskt till lokala datorer måste du installera dem manuellt. Undantaget är Azure-modulen. Den här modulen installeras som standard och ger åtkomst till cmdletar för alla Azure-tjänster och-aktiviteter för Azure Automation.

Eftersom det primära syftet med Hybrid Runbook Worker är att hantera lokala resurser, behöver du förmodligen installera modulerna som stöder dessa resurser, särskilt `PowerShellGet` modulen. Information om hur du installerar Windows PowerShell-moduler finns i [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduler som är installerade måste finnas på en plats som refereras av `PSModulePath` miljövariabeln så att hybrid Worker kan importera dem automatiskt. Mer information finns [i installera moduler i PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Ta bort Hybrid Runbook Worker från en lokal Windows-dator

1. I Azure Portal går du till ditt Automation-konto.

2. Under **konto inställningar**väljer du **nycklar** och noterar värdena för **URL** och **primär åtkomst nyckel**.

3. Öppna en PowerShell-session i administratörs läge och kör följande kommando med URL: en och primär åtkomst nyckel värden. Använd `Verbose` parametern för en detaljerad logg över borttagnings processen. Om du vill ta bort inaktuella datorer från Hybrid Worker gruppen använder du den valfria `machineName` parametern.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en Hybrid Runbook Worker grupp måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen. Använd sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure Portal.

2. Välj **hybrid Worker-grupper** under **process automatisering**. Välj den grupp som du vill ta bort. Sidan Egenskaper för gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. På egenskaps sidan för den valda gruppen väljer du **ta bort**. Ett meddelande som uppmanar dig att bekräfta åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem](troubleshoot/hybrid-runbook-worker.md#general).
