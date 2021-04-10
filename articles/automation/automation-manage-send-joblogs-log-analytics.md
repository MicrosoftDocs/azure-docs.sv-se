---
title: Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar
description: Den här artikeln beskriver hur du skickar jobb status och jobb strömmar i Runbook till Azure Monitor loggar.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581211"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar

Azure Automation kan skicka Runbook-jobbets status och jobb strömmar till arbets ytan Log Analytics. Den här processen omfattar inte arbets ytans länkning och är helt oberoende. Jobb loggar och jobb strömmar visas i Azure Portal, eller med PowerShell, för enskilda jobb och det gör att du kan utföra enkla undersökningar. Nu kan du med Azure Monitor loggar:

* Få inblick i status för dina Automation-jobb.
* Utlös ett e-postmeddelande eller en avisering baserat på status för Runbook-jobb (till exempel misslyckad eller pausad).
* Skriv avancerade frågor i dina jobb strömmar.
* Korrelera jobb mellan Automation-konton.
* Använd anpassade vyer och Sök frågor för att visualisera dina Runbook-resultat, status för Runbook-jobb och andra relaterade nyckel indikatorer eller mått.

## <a name="prerequisites"></a>Förutsättningar

För att börja skicka dina Automation-loggar till Azure Monitor loggar behöver du:

* Den senaste versionen av [Azure PowerShell](/powershell/azure/).

* En Log Analytics arbets yta och dess resurs-ID. Mer information finns i [Kom igång med Azure Monitor loggar](../azure-monitor/overview.md).

* Resurs-ID för ditt Azure Automation-konto.

## <a name="how-to-find-resource-ids"></a>Hitta resurs-ID: n

1. Använd följande kommando för att hitta resurs-ID: t för ditt Azure Automation-konto:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Kopiera värdet för **ResourceID**.

3. Använd följande kommando för att hitta resurs-ID: t för din Log Analytics arbets yta:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Kopiera värdet för **ResourceID**.

Inkludera parametern om du vill returnera resultat från en speciell resurs grupp `-ResourceGroupName` . Mer information finns i [Get-AzResource](/powershell/module/az.resources/get-azresource).

Om du har mer än ett Automation-konto eller en arbets yta i resultatet av föregående kommandon, kan du hitta namn och andra relaterade egenskaper som ingår i det fullständiga resurs-ID: t för ditt Automation-konto genom att utföra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Azure Portal väljer du ditt Automation-konto på sidan **Automation-konton** .
1. Välj **Egenskaper** under **konto inställningar** på sidan för det valda Automation-kontot.
1. På sidan **Egenskaper** noterar du informationen som visas nedan.

    ![Egenskaper för Automation-konto](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Inställningarna för Automation-diagnostik stöder vidarebefordran av följande plattforms loggar och mått data:

* JobLogs
* JobStreams
* DSCNodeStatus
* Mått-totalt antal jobb, den totala uppdaterings distributions datorn körs, den totala uppdaterings distributionen körs

Om du vill börja skicka dina Automation-loggar till Azure Monitor loggar kan du läsa [skapa diagnostikinställningar](../azure-monitor/essentials/diagnostic-settings.md) för att förstå funktionen och metoderna som är tillgängliga för att konfigurera diagnostikinställningar för att skicka plattforms loggar.

## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster

Azure Automation diagnostik skapar du två typer av poster i Azure Monitor loggar, taggade som `AzureDiagnostics` . Tabellerna i nästa avsnitt är exempel på poster som Azure Automation genererar och de data typer som visas i loggs öknings resultat.

### <a name="job-logs"></a>Jobbloggar

| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar Runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>– Ny<br>– Skapad<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>-Slutförd |
| Kategori | Klassificering av typ av data. För Automation är värdet JobLogs. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot |
| SourceSystem | System som Azure Monitor loggar används för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Jobbets resultat tillstånd för Runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes |
| CorrelationId |Runbook-jobbets korrelations-GUID. |
| ResourceId |Resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | GUID för Azure-prenumerationen för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Resurs leverantören. Värdet är MICROSOFT. Automatiska. |
| ResourceType | Resurs typen. Värdet är AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Arbetsflöden
| Egenskap | Beskrivning |
| --- | --- |
| TimeGenerated |Datum och tid då runbook-jobbet körs. |
| RunbookName_s |Anger namnet på runbooken. |
| Caller_s |Anroparen som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb. |
| StreamType_s |Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose |
| Tenant_g | GUID som identifierar klienten för anroparen. |
| JobId_g |GUID som identifierar Runbook-jobbet. |
| ResultType |Status för runbookjobbet. Möjliga värden:<br>Pågår |
| Kategori | Klassificering av typ av data. För Automation är värdet JobStreams. |
| OperationName | Typ av åtgärd som utförs i Azure. För Automation är värdet jobb. |
| Resurs | Namnet på Automation-kontot. |
| SourceSystem | System som Azure Monitor loggar används för att samla in data. Värdet är alltid Azure för Azure Diagnostics. |
| ResultDescription |Beskrivning som innehåller utdataströmmen från runbooken. |
| CorrelationId |Runbook-jobbets korrelations-GUID. |
| ResourceId |Resurs-ID för Azure Automation-kontot för runbooken. |
| SubscriptionId | GUID för Azure-prenumerationen för Automation-kontot. |
| ResourceGroup | Namnet på resurs gruppen för Automation-kontot. |
| ResourceProvider | Resurs leverantören. Värdet är MICROSOFT. Automatiska. |
| ResourceType | Resurs typen. Värdet är AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Visa Automation-loggar i Azure Monitor loggar

Nu när du har börjat skicka dina strömmar och loggar till Azure Monitor loggar ska vi se vad du kan göra med dessa loggar i Azure Monitor loggar.

Kör följande fråga för att se loggarna: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Skicka ett e-postmeddelande när ett Runbook-jobb Miss lyckas eller pausas

Följande steg visar hur du ställer in aviseringar i Azure Monitor för att meddela dig när något går fel med ett Runbook-jobb.

Om du vill skapa en varnings regel börjar du med att skapa en loggs ökning för de Runbook-jobb poster som ska anropa aviseringen. Klicka på knappen **avisering** om du vill skapa och konfigurera varnings regeln.

1. Klicka på **Visa loggar** på översikts sidan för Log Analytics-arbetsyta.

2. Skapa en loggs öknings fråga för aviseringen genom att skriva följande sökning i fältet fråga: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Du kan också gruppera efter Runbook-namnet genom att använda: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Om du ställer in loggar från fler än ett Automation-konto eller en prenumeration på din arbets yta, kan du gruppera dina aviseringar efter prenumeration och Automation-konto. Du hittar namnet på Automation-kontot i `Resource` fältet i sökningen `JobLogs` .

3. Öppna skärmen **Skapa regel** genom att klicka på **ny varnings regel** överst på sidan. Mer information om alternativen för att konfigurera aviseringen finns i [logg aviseringar i Azure](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Hitta alla jobb som har slutförts med fel

Förutom aviseringar vid fel kan du hitta när ett Runbook-jobb har ett icke-avslutande fel. I dessa fall genererar PowerShell en fel ström, men de icke-avslutande felen orsakar inte att jobbet pausas eller kraschar.

1. Klicka på **loggar** i arbets ytan Log Analytics.

2. I fältet fråga skriver du `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Klicka på knappen **Sök** .

### <a name="view-job-streams-for-a-job"></a>Visa jobb strömmar för ett jobb

När du felsöker ett jobb kanske du också vill titta på jobb strömmarna. Följande fråga visar alla data strömmar för ett enda jobb med GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Visa historisk jobb status

Slutligen kanske du vill visualisera jobb historiken över tid. Du kan använda den här frågan för att söka efter status för dina jobb över tid.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics historik jobb status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrera jobb status utdata som konverterats till ett JSON-objekt

Nyligen ändrade vi beteendet för hur Automation-loggdata skrivs till `AzureDiagnostics` tabellen i Log Analytics-tjänsten, där den inte längre delar upp JSON-egenskaperna i separata fält. Om du har konfigurerat din Runbook för att formatera objekt i utdataströmmen i JSON-format som separata kolumner, är det nödvändigt att konfigurera om dina frågor för att parsa fältet till ett JSON-objekt för att få åtkomst till dessa egenskaper. Detta görs med hjälp av [parseJSON](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) för att komma åt ett visst JSON-element i en känd sökväg.

En Runbook formaterar till exempel egenskapen *ResultDescription* i UTDATASTRÖMMEN i JSON-format med flera fält. Om du vill söka efter status för jobb som är i ett felaktigt tillstånd enligt vad som anges i ett fält med namnet **status**, använder du den här exempel frågan för att söka i *ResultDescription* med statusen **misslyckades**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics historisk JSON-format för jobb ström](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar Sök frågor och granskar automatiserings jobb loggar med Azure Monitor loggar finns i [loggs ökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md).
* Information om hur du skapar och hämtar utdata och fel meddelanden från Runbooks finns i [övervaka Runbook-utdata](automation-runbook-output-and-messages.md).
* Mer information om Runbook-körning, hur du övervakar Runbook-jobb och annan teknisk information finns [i Runbook-körning i Azure Automation](automation-runbook-execution.md).
* Mer information om Azure Monitor loggar och data insamlings källor finns i [samla in Azure Storage-data i Azure Monitor loggar översikt](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
* Information om hur du felsöker Log Analytics finns i [fel sökning varför Log Analytics inte längre samlar in data](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).