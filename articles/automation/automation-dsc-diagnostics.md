---
title: Integrera med Azure Monitor loggar
description: Den här artikeln beskriver hur du skickar rapporterings data för önskad tillstånds konfiguration från Azure Automation tillstånds konfiguration till Azure Monitor loggar.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 34bbf34d53c44dcef7b8e128a93ee64201423c3e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897045"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrera med Azure Monitor loggar

Azure Automation tillstånds konfiguration behåller nodernas status data i 30 dagar. Du kan skicka nodernas status data till din Log Analytics arbets yta om du vill behålla dessa data under en längre period. Kompatibilitetsstatus visas i Azure Portal eller med PowerShell för noder och för enskilda DSC-resurser i nodkonfigurationer. 

Azure Monitor-loggar ger bättre drifts insyn i konfigurations data för Automation-tillstånd och kan hjälpa dig att lösa incidenter snabbare. Med Azure Monitor loggar kan du:

- Hämta kompatibilitetsinformation för hanterade noder och enskilda resurser.
- Utlös ett e-postmeddelande eller en avisering baserat på status för efterlevnad.
- Skriv avancerade frågor över dina hanterade noder.
- Korrelera kompatibilitetsstatus mellan Automation-konton.
- Använd anpassade vyer och Sök frågor för att visualisera dina Runbook-resultat, status för Runbook-jobb och andra relaterade nyckel indikatorer eller mått.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna börja skicka konfigurations rapporter för automatiserings tillstånd till Azure Monitor loggar behöver du:

- Utgåvan 2016 eller senare av [Azure PowerShell](/powershell/azure/) (v-2.3.0).
- Ett Azure Automation-konto. Mer information finns i [Introduktion till Azure Automation](automation-intro.md).
- En Log Analytics arbets yta med ett tjänst erbjudande för Automation &-kontroll. Mer information finns i [Kom igång med Log Analytics i Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).
- Minst en nod för Azure Automation tillstånds konfiguration. Mer information finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).
- [XDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) -modulen, version 2.7.0.0 eller senare. Installations anvisningar finns i [felsöka Azure Automation önskad tillstånds konfiguration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurera integration med Azure Monitor loggar

Utför följande steg för att börja importera data från Azure Automation tillstånds konfiguration till Azure Monitor loggar:

1. Logga in på ditt Azure-konto i PowerShell. Se [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Hämta resurs-ID för ditt Automation-konto genom att köra följande PowerShell-cmdlet. Om du har fler än ett Automation-konto väljer du resurs-ID för det konto som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Hämta resurs-ID: t för din Log Analytics-arbetsyta genom att köra följande PowerShell-cmdlet. Om du har fler än en arbets yta väljer du resurs-ID för den arbets yta som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Kör följande PowerShell-cmdlet och Ersätt `<AutomationResourceId>` och `<WorkspaceResourceId>` med `ResourceId` värdena från vart och ett av de föregående stegen.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Om du vill stoppa importen av data från Azure Automation tillstånds konfiguration till Azure Monitor loggar kör du följande PowerShell-cmdlet.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visa tillstånds konfigurations loggarna

När du har konfigurerat integration med Azure Monitor loggar för konfigurations data för Automation-tillstånd kan du visa dem genom att välja **loggar** i avsnittet **övervakning** i den vänstra rutan på sidan för tillstånds konfiguration (DSC).

![Loggar](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Fönstret loggs ökning öppnas med en fråge region som är begränsad till din Automation-konto resurs. Du kan söka i tillstånds konfigurations loggarna efter DSC-åtgärder genom att söka i Azure Monitor loggar. Posterna för DSC-åtgärder lagras i `AzureDiagnostics` tabellen. Skriv till exempel följande fråga om du vill hitta noder som inte är kompatibla.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filtrerings information:

* Filtrera på för `DscNodeStatusData` att returnera åtgärder för varje nod i tillstånds konfigurationen.
* Filtrera på för `DscResourceStatusData` att returnera åtgärder för varje DSC-resurs som kallas i nodens konfiguration som tillämpas på resursen. 
* Filtrera på för `DscResourceStatusData` att returnera fel information för alla DSC-resurser som inte fungerar.

Mer information om hur du skapar logg frågor för att hitta data finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Skicka ett e-postmeddelande när kompatibilitetskontroll för tillstånds konfiguration Miss lyckas

En av våra främsta kund förfrågningar är att kunna skicka ett e-postmeddelande eller en text när något går fel med en DSC-konfiguration.

Om du vill skapa en varnings regel börjar du med att skapa en loggs ökning för rapport poster för tillstånds konfiguration som ska anropa aviseringen. Klicka på knappen **ny varnings regel** för att skapa och konfigurera varnings regeln.

1. Klicka på **loggar** på översikts sidan för Log Analytics-arbetsyta.
1. Skapa en loggs öknings fråga för aviseringen genom att skriva följande sökning i fältet fråga:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Om du har konfigurerat loggar från fler än ett Automation-konto eller en prenumeration på din arbets yta, kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Härled namnet på Automation-kontot från `Resource` fältet i Sök efter `DscNodeStatusData` poster.
1. Öppna skärmen **Skapa regel** genom att klicka på **ny varnings regel** överst på sidan. 

Mer information om alternativen för att konfigurera aviseringen finns i [skapa en varnings regel](../azure-monitor/platform/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Hitta felande DSC-resurser över alla noder

En fördel med att använda Azure Monitor loggar är att du kan söka efter misslyckade kontroller över flera noder. Så här hittar du alla instanser av DSC-resurser som har misslyckats:

1. Klicka på **loggar** på översikts sidan för Log Analytics-arbetsyta.
1. Skapa en loggs öknings fråga för aviseringen genom att skriva följande sökning i fältet fråga:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visa historisk DSC-nods status

Om du vill visualisera din status historik för DSC-noder över tid kan du använda den här frågan:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Den här frågan visar ett diagram över nodens status över tid.

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Azure Automation diagnostik skapar du två kategorier med poster i Azure Monitor loggar:

* Status data för noder ( `DscNodeStatusData` )
* Resurs status data ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då kompatibilitetskontroll kördes. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Värde som anger om noden är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| NodeComplianceStatus_s |Status värde som anger om noden är kompatibel. |
| DscReportStatus |Status värde som anger om kompatibilitetskontroll lyckades. |
| ConfigurationMode | Det läge som används för att tillämpa konfigurationen på noden. Möjliga värden: <ul><li>`ApplyOnly`: DSC tillämpar konfigurationen och gör ingenting ytterligare om inte en ny konfiguration skickas till målnoden eller när en ny konfiguration hämtas från en server. Efter första tillämpning av en ny konfiguration söker DSC inte efter avvikelse från ett tidigare konfigurerat tillstånd. DSC försöker tillämpa konfigurationen tills den har slutförts innan `ApplyOnly` värdet börjar gälla. </li><li>`ApplyAndMonitor`: Detta är standardvärdet. LCM använder alla nya konfigurationer. Efter den första körningen av en ny konfiguration, om mål-noden går från det önskade läget, rapporterar DSC den avvikelsen i loggarna. DSC försöker tillämpa konfigurationen tills den har slutförts innan `ApplyAndMonitor` värdet börjar gälla.</li><li>`ApplyAndAutoCorrect`: DSC tillämpar alla nya konfigurationer. Efter den första applikationen av en ny konfiguration, om mål-noden går från det önskade läget, rapporterar DSC den avvikelsen i loggarna och tillämpar sedan den aktuella konfigurationen igen.</li></ul> |
| HostName_s | Namnet på den hanterade noden. |
| IPAddress | Den hanterade nodens IPv4-adress. |
| Kategori | `DscNodeStatus`. |
| Resurs | Namnet på Azure Automation kontot. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g | GUID som identifierar den hanterade noden. |
| DscReportId_g | GUID som identifierar rapporten. |
| LastSeenTime_t | Datum och tid då rapporten senast visades. |
| ReportStartTime_t | Datum och tid då rapporten startades. |
| ReportEndTime_t | Datum och tid då rapporten slutfördes. |
| NumberOfResources_d | Antalet DSC-resurser som anropades i konfigurationen som tillämpas på noden. |
| SourceSystem | Käll systemet som identifierar hur Azure Monitor loggar har samlat in data. Alltid `Azure` för Azure Diagnostics. |
| ResourceId |Resurs-ID för det Azure Automation kontot. |
| ResultDescription | Resurs beskrivningen för den här åtgärden. |
| SubscriptionId | ID för Azure-prenumerationen (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Utforskaren. Automatiska. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Ett GUID som är korrelations-ID för Kompatibilitetsrapport. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då kompatibilitetskontroll kördes. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Om resursen är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| Kategori | DscNodeStatus. |
| Resurs | Namnet på Azure Automation kontot. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g |GUID som identifierar den hanterade noden. |
| DscReportId_g |GUID som identifierar rapporten. |
| DscResourceId_s |Namnet på DSC-Resurshanterarens instans. |
| DscResourceName_s |Namnet på DSC-resursen. |
| DscResourceStatus_s |Om DSC-resursen är kompatibel. |
| DscModuleName_s |Namnet på PowerShell-modulen som innehåller DSC-resursen. |
| DscModuleVersion_s |Den version av PowerShell-modulen som innehåller DSC-resursen. |
| DscConfigurationName_s |Namnet på konfigurationen som används på noden. |
| ErrorCode_s | Felkoden om resursen misslyckades. |
| ErrorMessage_s |Fel meddelandet om resursen misslyckades. |
| DscResourceDuration_d |Tiden, i sekunder, som DSC-resursen kördes. |
| SourceSystem | Hur Azure Monitor loggar in data. Alltid `Azure` för Azure Diagnostics. |
| ResourceId |ID för det Azure Automation kontot. |
| ResultDescription | Beskrivningen för den här åtgärden. |
| SubscriptionId | ID för Azure-prenumerationen (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Utforskaren. Automatiska. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID som är den efterföljande rapportens korrelations-ID. |

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).
- Information om hur du kommer igång finns i [Kom igång med Azure Automation tillstånds konfiguration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [Konfigurera kontinuerlig distribution med choklad](automation-dsc-cd-chocolatey.md).
- Mer information om hur du skapar olika Sök frågor och granskar konfigurations loggarna för Automation-tillstånd med Azure Monitor loggar finns [i loggs ökningar i Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md).
- Mer information om Azure Monitor loggar och data insamlings källor finns i [samla in Azure Storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).