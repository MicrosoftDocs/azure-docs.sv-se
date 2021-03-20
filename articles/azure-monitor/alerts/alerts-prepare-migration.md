---
title: Uppdatera Logic Apps & Runbooks för aviserings migrering
description: Lär dig hur du ändrar dina Webhooks, Logi Kap par och Runbooks för att förbereda för frivillig migrering.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ce61c3539a4ea29cbeb48c379ed143363500701e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038024"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Förbered dina logikappar och runbooks för migrering av klassiska aviseringsregler

> [!NOTE]
> Som [tidigare](monitoring-classic-retirement.md)har meddelats kommer de klassiska aviseringarna i Azure monitor att dras tillbaka för offentliga moln användare, men fortfarande i begränsad användning till och med **31 maj 2021**. Klassiska aviseringar för Azure Government molnet och Azure Kina 21Vianet kommer att dra tillbaka den **29 februari 2024**.
>

Om du väljer att frivilligt migrera dina klassiska aviserings regler till nya varnings regler finns det några skillnader mellan de två systemen. I den här artikeln beskrivs skillnaderna och hur du kan förbereda dig för ändringen.

## <a name="api-changes"></a>API-ändringar

De API: er som skapar och hanterar klassiska varnings regler ( `microsoft.insights/alertrules` ) skiljer sig från de API: er som skapar och hanterar nya mått varningar ( `microsoft.insights/metricalerts` ). Om du program mässigt skapar och hanterar klassiska varnings regler idag, kan du uppdatera dina distributions skript så att de fungerar med de nya API: erna.

Följande tabell är en referens till programmerings gränssnitten för både de klassiska och nya aviseringarna:

| Typ av distributions skript | Klassiska aviseringar | Nya mått varningar |
| ---------------------- | -------------- | ----------------- |
|REST-API     | [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)         | [Microsoft. Insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [avisering om AZ-övervakaren](/cli/azure/monitor/alert)        | [avisering om AZ Monitor-mått](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [Referens](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referens](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-mall | [För klassiska aviseringar](./alerts-enable-template.md)|[För nya mått varningar](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Ändringar i meddelande nytto Last

Formatet för meddelande nytto Last skiljer sig något från de [klassiska aviserings reglerna](alerts-webhooks.md) och [nya mått varningar](alerts-metric-near-real-time.md#payload-schema). Om du har klassiska varnings regler med webhook, Logi Kap par eller Runbook-åtgärder måste du uppdatera målen för att godkänna det nya nytto Last formatet.

Använd följande tabell för att Mappa fälten för webhook-nyttolasten från det klassiska formatet till det nya formatet:

| Slut punkts typ för meddelande | Klassiska aviseringar | Nya mått varningar |
| -------------------------- | -------------- | ----------------- |
|Har aviseringen Aktiver ATS eller lösts?    | **statusfältet**       | **data. status** |
|Sammanhangsbaserad information om aviseringen     | **Edit**        | **data. context**        |
|Tidstämpeln som aviseringen aktiverades eller löstes i     | **Context. timestamp**       | **data. context. timestamp**        |
| Varnings regel-ID | **context.id** | **data.context.id** |
| Namn på aviseringsregel | **context.name** | **data.context.name** |
| Beskrivning av aviserings regeln | **Context. Description** | **data. context. Description** |
| Villkor för varnings regel | **Context. Condition** | **data. context. Condition** |
| Måttnamn | **Context. condition. metricName** | **data. context. condition. allOf [0]. metricName** |
| Tids mängd (hur måttet sammanställs över utvärderings perioden)| **Context. condition. timeAggregation** | **Context. condition. timeAggregation** |
| Utvärderings period | **Context. condition. windowSize** | **data. context. condition. windowSize** |
| Operator (hur det aggregerade Metric-värdet jämförs mot tröskelvärdet) | **Context. condition. operator** | **data. context. condition. operator** |
| Tröskelvärde | **Context. condition. Threshold** | **data. context. condition. allOf [0]. tröskel** |
| Mått värde | **Context. condition. metricValue** | **data. context. condition. allOf [0]. metricValue** |
| Prenumerations-ID:t | **Context. subscriptionId** | **data. context. subscriptionId** |
| Resurs grupp för den berörda resursen | **Context. resourceGroup** | **data. context. resourceGroup** |
| Namnet på den berörda resursen | **Context. resourceName** | **data. context. resourceName** |
| Typ av resurs som påverkas | **Context. resourceType** | **data. context. resourceType** |
| Resurs-ID för den berörda resursen | **Context. resourceId** | **data. context. resourceId** |
| Direkt länk till sammanfattnings sidan för Portal resursen | **Context. portalLink** | **data. context. portalLink** |
| Anpassade nytto Last fält som ska skickas till webhooken eller Logic app | **egenskaperna** | **data. Properties** |

Nytto lasterna är liknande, som du ser. Följande avsnitt innehåller:

- Information om hur du ändrar Logi Kap par för att arbeta med det nya formatet.
- Ett Runbook-exempel som analyserar meddelande nytto lasten för nya aviseringar.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Ändra en Logi Kap par-app för att få ett mått på varnings meddelande

Om du använder Logi Kap par med klassiska aviseringar måste du ändra logik-app-koden för att parsa de nya måtten för mått aviseringar. Följ de här stegen:

1. Skapa en ny Logic-app.

1. Använd mallen Azure Monitor-Metrics-aviserings hanterare. Den här mallen har en **http-begäran-** utlösare med lämpligt schema definierat.

    ![Skärm bild som visar två knappar, Tom Logic app och Azure Monitor – mått aviserings hanterare.](media/alerts-prepare-migration/logic-app-template.png "Mall för mått varningar")

1. Lägg till en åtgärd som är värd för bearbetnings logiken.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Använd en Automation-Runbook som tar emot ett varnings meddelande om mått

I följande exempel visas PowerShell-kod som används i din Runbook. Den här koden kan parsa nytto lasterna för både klassiska mått och nya mått för varnings regler.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Ett fullständigt exempel på en Runbook som stoppar en virtuell dator när en avisering utlöses finns i [Azure Automation-dokumentationen](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Partner integrering via Webhooks

De flesta av [våra partner som integrerar med klassiska aviseringar har](../partners.md) redan stöd för nya mått aviseringar via deras integreringar. Kända integreringar som redan fungerar med nya mått aviseringar är:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Om du använder en partner integrering som inte listas här, bekräftar du med den provider som de arbetar med nya mått aviseringar.

## <a name="next-steps"></a>Nästa steg

- [Använda migreringsverktyget](alerts-using-migration-tool.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)