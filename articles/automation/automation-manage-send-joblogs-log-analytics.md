---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln beskriver hur du skickar jobbstatus och runbook-jobbströmmar till Azure Monitor loggar.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c24c38368ef20dadd0dc19b1804f9d27ad68bd27
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833696"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar

Azure Automation kan skicka status för runbook-jobb och jobbströmmar till Log Analytics-arbetsytan. Den här processen omfattar inte länkning av arbetsytor och är helt oberoende. Jobbloggar och jobbströmmar visas i Azure Portal, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu med Azure Monitor kan du:

* Få insikter om statusen för dina Automation-jobb.
* Utlösa ett e-postmeddelande eller en avisering baserat på statusen för runbook-jobbet (till exempel misslyckad eller pausad).
* Skriv avancerade frågor i dina jobbströmmar.
* Korrelera jobb mellan Automation-konton.
* Använd anpassade vyer och sökfrågor för att visualisera runbook-resultat, runbook-jobbstatus och andra relaterade kpi:er eller mått.

## <a name="prerequisites"></a>Förutsättningar

Om du vill börja skicka Automation-loggar Azure Monitor loggar behöver du:

* Den senaste versionen av [Azure PowerShell](/powershell/azure/).

* En Log Analytics-arbetsyta och dess resurs-ID. Mer information finns i [Kom igång med Azure Monitor loggar.](../azure-monitor/overview.md)

* Resurs-ID för ditt Azure Automation konto.

## <a name="how-to-find-resource-ids"></a>Så här hittar du resurs-ID:er

1. Använd följande kommando för att hitta resurs-ID:t för ditt Azure Automation konto:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Kopiera värdet för **ResourceID**.

3. Använd följande kommando för att hitta resurs-ID:t för Log Analytics-arbetsytan:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Kopiera värdet för **ResourceID**.

Om du vill returnera resultat från en specifik resursgrupp inkluderar du `-ResourceGroupName` parametern . Mer information finns i [Get-AzResource](/powershell/module/az.resources/get-azresource).

Om du har fler än ett Automation-konto eller en arbetsyta i utdata från föregående kommandon kan du hitta namnet och andra relaterade egenskaper som ingår i det fullständiga resurs-ID:t för ditt Automation-konto genom att utföra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Azure Portal väljer du ditt Automation-konto på sidan **Automation-konton.**
1. På sidan för det valda Automation-kontot går du till **Kontoinställningar** och väljer **Egenskaper.**
1. På sidan **Egenskaper** noterar du informationen som visas nedan.

    ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Diagnostikinställningar för automatisering stöder vidarebefordran av följande plattformsloggar och måttdata:

* JobLogs
* JobStreams
* DSCNodeStatus
* Mått – totalt antal jobb, totalt antal datorkörningar för uppdateringsdistribution, totalt antal uppdateringsdistributionskörningar

Om du vill börja skicka Automation-loggar [](../azure-monitor/essentials/diagnostic-settings.md) till Azure Monitor loggar kan du läsa skapa diagnostikinställningar för att förstå funktionen och metoderna som är tillgängliga för att konfigurera diagnostikinställningar för att skicka plattformsloggar.

## <a name="azure-monitor-log-records"></a>Azure Monitor loggposter

Azure Automation diagnostiken skapar du två typer av poster i Azure Monitor loggar, taggade som `AzureDiagnostics` . Tabellerna i nästa avsnitt är exempel på poster som Azure Automation genererar och de datatyper som visas i loggsökningsresultaten.

### <a name="job-logs"></a>Jobbloggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>– Ny<br>– Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>– Slutfört |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | System som Azure Monitor loggar använder för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Resultattillståndet för runbook-jobbet. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |Korrelations-GUID för runbook-jobbet. |
| ResourceId |Det Azure Automation kontoresurs-ID:t för runbooken. |
| SubscriptionId | Azure-prenumerationens GUID för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Resursprovidern. Värdet är MICROSOFT. Automation. |
| ResourceType | Resurstypen. Värdet är AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Arbetsflöden
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>– Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet Jobb. |
| Resurs | Namnet på Automation-kontot. |
| SourceSystem | System som Azure Monitor loggar använder för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Beskrivning som innehåller utdataströmmen från runbooken. |
| CorrelationId |Korrelations-GUID för runbook-jobbet. |
| ResourceId |Det Azure Automation kontoresurs-ID:t för runbooken. |
| SubscriptionId | AZURE-prenumerationens GUID för Automation-kontot. |
| ResourceGroup | Namnet på resursgruppen för Automation-kontot. |
| ResourceProvider | Resursprovidern. Värdet är MICROSOFT. Automation. |
| ResourceType | Resurstypen. Värdet är AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Visa Automation-loggar i Azure Monitor loggar

Nu när du har börjat skicka dina Automation-jobbströmmar och loggar till Azure Monitor loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor loggar.

Om du vill se loggarna kör du följande fråga: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett runbook-jobb misslyckas eller pausas

Följande steg visar hur du ställer in aviseringar i Azure Monitor meddelar dig när något går fel med ett Runbook-jobb.

Om du vill skapa en aviseringsregel börjar du med att skapa en loggsökning efter de Runbook-jobbposter som ska anropa aviseringen. Klicka på **aviseringsknappen** för att skapa och konfigurera aviseringsregeln.

1. På översiktssidan för Log Analytics-arbetsytan klickar du på **Visa loggar.**

2. Skapa en loggsökfråga för aviseringen genom att skriva följande sökning i frågefältet: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Du kan också gruppera efter runbookens namn med hjälp av: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du ställer in loggar från fler än ett Automation-konto eller en Automation-prenumeration till din arbetsyta kan du gruppera aviseringarna efter prenumeration och Automation-konto. Automation-kontonamnet finns i `Resource` fältet i sökningen i `JobLogs` .

3. Öppna skärmen **Skapa regel** genom att klicka på Ny **aviseringsregel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns i [Logga aviseringar i Azure](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel

Förutom aviseringar om fel kan du se när ett Runbook-jobb har ett icke-avslutande fel. I dessa fall genererar PowerShell en felström, men de icke-avslutande felen gör inte att jobbet pausas eller misslyckas.

1. Klicka på Loggar på Log **Analytics-arbetsytan.**

2. I frågefältet skriver du `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Klicka på **knappen** Sök.

### <a name="view-job-streams-for-a-job"></a>Visa jobbströmmar för ett jobb

När du felsöker ett jobb kanske du även vill titta på jobbströmmarna. Följande fråga visar alla strömmar för ett enda jobb med `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` GUID:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Visa status för historiska jobb

Slutligen kanske du vill visualisera jobbhistoriken över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Diagram över historisk jobbstatus i Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrera jobbstatusutdata som konverterats till ett JSON-objekt

Nyligen ändrade vi beteendet för hur Automation-loggdata skrivs till tabellen i Log Analytics-tjänsten, där de inte längre delar upp `AzureDiagnostics` JSON-egenskaperna i separata fält. Om du har konfigurerat din runbook för att formatera objekt i utdataströmmen i JSON-format som separata kolumner, är det nödvändigt att konfigurera om dina frågor för att parsa fältet till ett JSON-objekt för att komma åt dessa egenskaper. Detta åstadkoms med [hjälp av parsejson för](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) att komma åt ett specifikt JSON-element i en känd sökväg.

En runbook formaterar till exempel *egenskapen ResultDescription* i utdataströmmen i JSON-format med flera fält. Om du vill söka efter status för jobb som är i ett misslyckat tillstånd som anges i ett fält som heter **Status** använder du den här exempelfrågan för att söka i *ResultDescription* med **statusen Misslyckades:**

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![JSON-format för Log Analytics Historical Job Stream](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar sökfrågor och granskar Automation-jobbloggar med Azure Monitor loggar finns i [Loggsökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md).
* Information om hur du skapar och hämtar utdata och felmeddelanden från runbooks finns i [Övervaka runbook-utdata.](automation-runbook-output-and-messages.md)
* Mer information om runbook-körning, hur du övervakar Runbook-jobb och annan teknisk information finns i [Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Mer information om hur Azure Monitor- och datainsamlingskällor finns i [Samla in Azure-lagringsdata Azure Monitor översikt över loggar.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
* Hjälp med att felsöka Log Analytics finns [i Felsöka varför Log Analytics inte längre samlar in data.](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)
