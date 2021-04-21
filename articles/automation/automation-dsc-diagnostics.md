---
title: Integrera med Azure Monitor loggar
description: Den här artikeln beskriver hur du skickar Desired State Configuration rapporteringsdata från Azure Automation State Configuration till Azure Monitor loggar.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834812"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrera med Azure Monitor loggar

Azure Automation State Configuration behåller nodstatusdata i 30 dagar. Du kan skicka nodstatusdata till Log Analytics-arbetsytan om du föredrar att behålla dessa data under en längre period. Kompatibilitetsstatusen visas i Azure Portal eller med PowerShell, för noder och för enskilda DSC-resurser i nodkonfigurationer. 

Azure Monitor ger bättre driftssynlighet för dina Automation-State Configuration-data och kan hjälpa dig att åtgärda incidenter snabbare. Med Azure Monitor-loggar kan du:

- Hämta efterlevnadsinformation för hanterade noder och enskilda resurser.
- Utlösa ett e-postmeddelande eller en avisering baserat på efterlevnadsstatus.
- Skriv avancerade frågor över dina hanterade noder.
- Korrelera efterlevnadsstatus mellan Automation-konton.
- Använd anpassade vyer och sökfrågor för att visualisera runbook-resultat, runbook-jobbstatus och andra relaterade kpi:er eller mått.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill börja skicka dina Automation State Configuration-rapporter till Azure Monitor loggar behöver du:

- November 2016 eller senare version av [Azure PowerShell](/powershell/azure/) (v2.3.0).
- Ett Azure Automation-konto. Mer information finns i [En introduktion till Azure Automation](automation-intro.md).
- En Log Analytics-arbetsyta med ett Automation & Control-tjänsterbjudande. Mer information finns i [Kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).
- Minst en Azure Automation State Configuration nod. Mer information finns i [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).
- Modulen [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) version 2.7.0.0 eller senare. Installationssteg finns i [Felsöka Azure Automation Desired State Configuration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Konfigurera integrering med Azure Monitor loggar

Börja importera data från Azure Automation State Configuration till Azure Monitor loggar genom att utföra följande steg:

1. Logga in på ditt Azure-konto i PowerShell. Se [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Hämta resurs-ID:t för ditt Automation-konto genom att köra följande PowerShell-cmdlet. Om du har fler än ett Automation-konto väljer du resurs-ID för det konto som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Hämta resurs-ID:t för Log Analytics-arbetsytan genom att köra följande PowerShell-cmdlet. Om du har fler än en arbetsyta väljer du resurs-ID:t för den arbetsyta som du vill konfigurera.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Kör följande PowerShell-cmdlet och ersätt `<AutomationResourceId>` och med värdena från vart och ett av de föregående `<WorkspaceResourceId>` `ResourceId` stegen.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Om du vill sluta importera data från Azure Automation State Configuration till Azure Monitor loggar kör du följande PowerShell-cmdlet.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visa State Configuration loggarna

När du har ställt **in** integrering med Azure Monitor-loggar för dina Automation State Configuration-data  kan du visa dem genom att välja Loggar i avsnittet Övervakning i det vänstra fönstret på sidan State Configuration (DSC).

![Loggar](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Fönstret Loggsökning öppnas med en frågeregion som är begränsad till din Automation-kontoresurs. Du kan söka i State Configuration efter DSC-åtgärder genom att söka i Azure Monitor loggar. Posterna för DSC-åtgärder lagras i `AzureDiagnostics` tabellen. Om du till exempel vill hitta noder som inte är kompatibla skriver du följande fråga.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filtreringsinformation:

* Filtrera på `DscNodeStatusData` för att returnera åtgärder för varje State Configuration nod.
* Filtrera på `DscResourceStatusData` för att returnera åtgärder för varje DSC-resurs som anropas i nodkonfigurationen som tillämpas på resursen. 
* Filtrera på `DscResourceStatusData` för att returnera felinformation för DSC-resurser som misslyckas.

Mer information om hur du skapar loggfrågor för att hitta data finns [i Översikt över loggfrågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Skicka ett e-postmeddelande när en State Configuration efterlevnadskontroll misslyckas

En av våra främsta kundförfrågningar är möjligheten att skicka ett e-postmeddelande eller en text när något går fel med en DSC-konfiguration.

Om du vill skapa en aviseringsregel börjar du med att skapa en loggsökning State Configuration de rapportposter som ska anropa aviseringen. Klicka på **knappen Ny aviseringsregel** för att skapa och konfigurera aviseringsregeln.

1. På översiktssidan för Log Analytics-arbetsytan klickar du på **Loggar.**
1. Skapa en loggsökfråga för aviseringen genom att skriva följande sökning i frågefältet:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Om du har ställt in loggar från mer än ett Automation-konto eller en Automation-prenumeration till din arbetsyta kan du gruppera aviseringarna efter prenumeration och Automation-konto. Härled Automation-kontonamnet `Resource` från fältet i sökningen efter `DscNodeStatusData` posterna.
1. Öppna skärmen **Skapa regel** genom att klicka på Ny **aviseringsregel** överst på sidan. 

Mer information om alternativen för att konfigurera aviseringen finns i [Skapa en aviseringsregel.](../azure-monitor/alerts/alerts-metric.md)

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Hitta misslyckade DSC-resurser på alla noder

En fördel med att använda Azure Monitor-loggar är att du kan söka efter misslyckade kontroller mellan noder. Så här hittar du alla instanser av DSC-resurser som har misslyckats:

1. På log analytics-arbetsytans översiktssida klickar du på **Loggar.**
1. Skapa en loggsökfråga för aviseringen genom att skriva följande sökning i frågefältet:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Visa historisk DSC-nodstatus

Om du vill visualisera DSC-nodens statushistorik över tid kan du använda den här frågan:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Den här frågan visar ett diagram över nodstatus över tid.

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Azure Automation diagnostik skapar du två kategorier av poster i Azure Monitor loggar:

* Nodstatusdata ( `DscNodeStatusData` )
* Resursstatusdata ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då kompatibilitetskontrollen kördes. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Värde som anger om noden är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| NodeComplianceStatus_s |Statusvärde som anger om noden är kompatibel. |
| DscReportStatus |Statusvärde som anger om kompatibilitetskontrollen har körts. |
| ConfigurationMode | Det läge som används för att tillämpa konfigurationen på noden. Möjliga värden: <ul><li>`ApplyOnly`: DSC tillämpar konfigurationen och gör ingenting ytterligare om inte en ny konfiguration skickas till målnoden eller när en ny konfiguration hämtas från en server. Efter den första tillämpningen av en ny konfiguration kontrollerar DSC inte om det finns någon avdrift från ett tidigare konfigurerat tillstånd. DSC försöker tillämpa konfigurationen tills den lyckas innan `ApplyOnly` värdet börjar gälla. </li><li>`ApplyAndMonitor`: Det här är standardvärdet. LCM tillämpar alla nya konfigurationer. Efter den första tillämpningen av en ny konfiguration rapporterar DSC avvikelsen i loggarna om målnoden fördr bort från det önskade tillståndet. DSC försöker tillämpa konfigurationen tills den lyckas innan `ApplyAndMonitor` värdet börjar gälla.</li><li>`ApplyAndAutoCorrect`: DSC tillämpar alla nya konfigurationer. Om målnoden efter den första tillämpningen av en ny konfiguration förs från det önskade tillståndet, rapporterar DSC avvikelsen i loggarna och använder sedan den aktuella konfigurationen på nytt.</li></ul> |
| HostName_s | Namnet på den hanterade noden. |
| IPAddress | IPv4-adressen för den hanterade noden. |
| Kategori | `DscNodeStatus`. |
| Resurs | Namnet på Azure Automation konto. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g | GUID som identifierar den hanterade noden. |
| DscReportId_g | GUID som identifierar rapporten. |
| LastSeenTime_t | Datum och tid då rapporten senast visades. |
| ReportStartTime_t | Datum och tid då rapporten startades. |
| ReportEndTime_t | Datum och tid när rapporten slutfördes. |
| NumberOfResources_d | Antalet DSC-resurser som anropas i konfigurationen som tillämpas på noden. |
| SourceSystem | Källsystemet som identifierar hur Azure Monitor loggar har samlat in data. Alltid `Azure` för Azure Diagnostics. |
| ResourceId |Resursidentifieraren för Azure Automation konto. |
| ResultDescription | Resursbeskrivningen för den här åtgärden. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Ett GUID som är korrelationsidentifieraren för efterlevnadsrapporten. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då kompatibilitetskontrollen kördes. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Om resursen är kompatibel. |
| NodeName_s |Namnet på den hanterade noden. |
| Kategori | DscNodeStatus. |
| Resurs | Namnet på Azure Automation konto. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| NodeId_g |GUID som identifierar den hanterade noden. |
| DscReportId_g |GUID som identifierar rapporten. |
| DscResourceId_s |Namnet på DSC-resursinstansen. |
| DscResourceName_s |Namnet på DSC-resursen. |
| DscResourceStatus_s |Om DSC-resursen är i överensstämmelse. |
| DscModuleName_s |Namnet på Den PowerShell-modul som innehåller DSC-resursen. |
| DscModuleVersion_s |Den version av PowerShell-modulen som innehåller DSC-resursen. |
| DscConfigurationName_s |Namnet på konfigurationen som tillämpas på noden. |
| ErrorCode_s | Felkoden om resursen misslyckades. |
| ErrorMessage_s |Felmeddelandet om resursen misslyckades. |
| DscResourceDuration_d |Tiden i sekunder som DSC-resursen kördes. |
| SourceSystem | Hur Azure Monitor loggarna samlade in data. Alltid `Azure` för Azure Diagnostics. |
| ResourceId |Identifieraren för Azure Automation konto. |
| ResultDescription | Beskrivningen för den här åtgärden. |
| SubscriptionId | Azure-prenumerations-ID (GUID) för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID som är korrelations-ID:t för efterlevnadsrapporten. |

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Azure Automation State Configuration översikt.](automation-dsc-overview.md)
- Kom igång genom att gå [till Kom igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns i [Kompilera DSC-konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
- Prisinformation finns i [Azure Automation State Configuration priser.](https://azure.microsoft.com/pricing/details/automation/)
- Ett exempel på hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med Chocolatey](automation-dsc-cd-chocolatey.md).
- Mer information om hur du skapar olika sökfrågor och granskar Automation State Configuration-loggar med Azure Monitor-loggar finns i [Loggsökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md).
- Mer information om hur Azure Monitor- och datainsamlingskällor finns i [Samla in Azure-lagringsdata Azure Monitor översikt över loggar.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
