---
title: Övervaka Logic Apps med hjälp av Azure Monitor loggar
description: Felsök Logic Apps genom att konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d1c095be1d0525cc843c04a3b83e14bb4211b175
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721267"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps

För att få bättre fel söknings information om dina Logi Kap par under körningen kan du konfigurera och använda [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md) för att registrera och lagra information om körnings data och händelser, till exempel utlösa händelser, köra händelser och åtgärds händelser i en [Log Analytics arbets yta](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure Monitor](../azure-monitor/overview.md) hjälper dig att övervaka molnet och lokala miljöer så att du enklare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor loggar kan du skapa [logg frågor](../azure-monitor/logs/log-query-overview.md) som hjälper dig att samla in och granska den här informationen. Du kan också [använda dessa diagnostikdata med andra Azure-tjänster](#extend-data), till exempel Azure Storage och Azure Event Hubs.

Om du vill konfigurera loggning för din Logi Kap par kan du [aktivera Log Analytics när du skapar din Logic app](#logging-for-new-logic-apps), eller så kan du [Installera Logic Apps hanterings lösningen](#install-management-solution) i din Log Analytics arbets yta för befintliga Logi Kap par. Den här lösningen ger aggregerad information för din Logi Kap par och innehåller information som status, körnings tid, status för återsändning och korrelations-ID. Om du vill aktivera loggning och skapa frågor för den här informationen [konfigurerar du Azure Monitor loggar](#set-up-resource-logs).

Den här artikeln visar hur du aktiverar Log Analytics på nya Logi Kap par och befintliga Logi Kap par, hur du installerar och konfigurerar Logic Apps hanterings lösningen och hur du konfigurerar och skapar frågor för Azure Monitor loggar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha en [Log Analytics-arbetsyta](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Om du inte har en arbets yta, lär du dig [hur du skapar en arbets yta för Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Aktivera Log Analytics för nya Logic Apps

Du kan aktivera Log Analytics när du skapar din Logic app.

1. I [Azure Portal](https://portal.azure.com), i fönstret där du anger informationen för att skapa din Logi Kap par, följer du dessa steg:

   1. Under **Log Analytics** väljer du **på**.

   1. I listan **Log Analytics arbets yta** väljer du den arbets yta där du vill skicka data från din Logic app-körningar.

      ![Ange information om din logikapp](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      När du har slutfört det här steget skapar Azure din Logic app, som nu är kopplad till din Log Analytics-arbetsyta. Det här steget installerar också Logic Apps hanterings lösningen i din arbets yta.

1. När du är färdig väljer du **Skapa**.

1. När du har kört din Logi Kap par kan du [gå vidare med de här stegen](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Installera Logic Apps hanterings lösning

Om du har aktiverat Log Analytics när du skapade din Logic app, hoppar du över det här steget. Du har redan Logic Apps hanterings lösningen installerad i Log Analytics-arbetsytan.

1. I sökrutan [Azure Portal](https://portal.azure.com)anger du `log analytics workspaces` och väljer **Log Analytics arbets ytor**.

   ![Välj Log Analytics arbets ytor](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Under **Log Analytics arbets ytor** väljer du din arbets yta.

   ![Välj din Log Analytics arbets yta](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. I fönstret **Översikt** , under **Kom igång med Log Analytics**  >  **Konfigurera övervaknings lösningar**, väljer du **Visa lösningar**.

   ![I översikts fönstret väljer du "Visa lösningar"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Under **Översikt** väljer du **Lägg till**.

   ![Lägg till ny lösning i översikts fönstret](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. När **Marketplace** öppnas går du till rutan Sök `logic apps management` och anger och väljer **Logic Apps hantering**.

   ![Från Marketplace väljer du "Logic Apps hantering"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. I fönstret lösnings Beskrivning väljer du **skapa**.

   ![Välj "skapa" för att lägga till lösningen Logic Apps hantering](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Granska och bekräfta Log Analytics arbets ytan där du vill installera lösningen och välj sedan **skapa** igen.

   ![Välj "skapa" för "Logic Apps hantering"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   När Azure har distribuerat lösningen till den Azure-resurs grupp som innehåller din Log Analytics arbets yta, visas lösningen i arbets ytans sammanfattnings fönster.

   ![Fönstret Sammanfattning av arbets yta](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor loggar

När du lagrar information om körnings händelser och data i [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md)kan du skapa [logg frågor](../azure-monitor/logs/log-query-overview.md) som hjälper dig att hitta och granska den här informationen.

> [!NOTE]
> När du har aktiverat diagnostikinställningar kanske inte diagnostikdata flödar upp till 30 minuter till loggarna på det angivna målet, till exempel Log Analytics, händelsehubben eller lagrings konto. Den här fördröjningen innebär att diagnostikdata från den här tids perioden kanske inte finns för dig att granska. Slutförda händelser och [spårade egenskaper](#extend-data) kanske inte visas i Log Analytics arbets ytan i 10-15 minuter.

1. Leta upp och välj din Logic app i [Azure Portal](https://portal.azure.com).

1. På din Logic app-meny, under **övervakning**, väljer du **diagnostikinställningar**  >  **Lägg till diagnostisk inställning**.

   ![Under "övervakning" väljer du "diagnostikinställningar" > "Lägg till diagnostisk inställning"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Följ de här stegen för att skapa inställningen:

   1. Ange ett namn för inställningen.

   1. Välj **Skicka till Log Analytics**.

   1. För **prenumeration** väljer du den Azure-prenumeration som är kopplad till din Log Analytics-arbetsyta.

   1. För **Log Analytics arbets yta** väljer du den arbets yta som du vill använda.

   1. Under **logg** väljer du kategorin **WorkflowRuntime** , som anger den händelse kategori som du vill spela in.

   1. Om du vill välja alla mått under **mått** väljer du **AllMetrics**.

   1. När du är klar väljer du **Spara**.

   Exempel:

   ![Välj Log Analytics arbets yta och data för loggning](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Visa körnings status för Logic app

När din Logic app har körts kan du visa data om de som körs i din Log Analytics-arbetsyta.

1. Leta upp och öppna din Log Analytics arbets yta i [Azure Portal](https://portal.azure.com).

1. På arbets ytans meny väljer du **Sammanfattning av arbets yta**  >  **Logic Apps hantering**.

   ![Körnings status och antal för Logic app](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Om Logic Apps hanterings panelen inte visar resultat direkt efter en körning, försök att välja **Uppdatera** eller vänta en stund innan du försöker igen.

   Här kommer din Logic app att köras grupperat efter namn eller körnings status. Den här sidan visar också information om felen i åtgärder eller utlösare för Logic app-körningar.

   ![Status sammanfattning för din Logic App körs](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Om du vill visa alla körningar för en speciell Logic app eller status väljer du raden för den Logic app eller status.

   Här är ett exempel som visar alla körningar för en speciell Logic-app:

   ![Visa körnings-och status för Logic app](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   För åtgärder där du [ställer in spårade egenskaper](#extend-data)kan du också visa dessa egenskaper genom att välja **Visa** i kolumnen **spårade egenskaper** . Om du vill söka efter de spårade egenskaperna använder du kolumn filtret.

   ![Visa spårade egenskaper för en Logic app](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. För att filtrera resultaten kan du utföra både klient-och Server sidans filtrering.

   * **Filter på klient sidan**: för varje kolumn väljer du de filter som du vill använda, till exempel:

     ![Exempel kolumn filter](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filter på Server sidan**: om du vill välja en tids period eller begränsa antalet körningar som visas använder du kontrollen omfång överst på sidan. Som standard visas endast 1 000 poster i taget.

     ![Ändra tids perioden](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Om du vill visa alla åtgärder och deras information för en speciell körning väljer du raden för en Logic app-körning.

   Här är ett exempel som visar alla åtgärder och utlösare för en speciell Logic app-körning:

   ![Visa åtgärder för en Logic app-körning](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Skicka diagnostikdata till Azure Storage och Azure Event Hubs

Tillsammans med Azure Monitor loggar kan du utöka hur du använder dina Logic Apps diagnostikdata med andra Azure-tjänster, till exempel:

* [Arkivera Azures resurs loggar till lagrings kontot](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Strömma Azure-plattforms loggar till Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Du kan sedan få real tids övervakning genom att använda telemetri och analys från andra tjänster, t. ex. [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../azure-monitor/visualize/powerbi.md). Exempel:

* [Strömma data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrument panel för real tids analys i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Se till att du först [skapar ett Azure Storage-konto](../storage/common/storage-account-create.md) eller [skapa en Azure Event Hub](../event-hubs/event-hubs-create.md)baserat på de platser där du vill skicka diagnostikdata. Du kan sedan välja de mål där du vill skicka dessa data. Kvarhållning gäller endast när du använder ett lagrings konto.

![Skicka data till Azure Storage-konto eller händelsehubben](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor Diagnostics-händelser

Varje diagnostisk händelse innehåller information om din Logic app och händelsen, till exempel status, start tid, slut tid och så vidare. Om du vill konfigurera övervakning, spårning och loggning program mässigt kan du använda den här informationen med [REST API för Azure Logic Apps](/rest/api/logic) och [REST API för Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows). Du kan också använda `clientTrackingId` -och `trackedProperties` -egenskaperna, som visas i 

* `clientTrackingId`: Om inget annat anges genererar Azure automatiskt detta ID och korrelerar händelser i en Logic app-körning, inklusive eventuella kapslade arbets flöden som anropas från Logic app. Du kan manuellt ange detta ID i en utlösare genom att skicka ett `x-ms-client-tracking-id` huvud med ditt eget ID-värde i trigger-begäran. Du kan använda en begär ande utlösare, HTTP-utlösare eller webhook-utlösare.

* `trackedProperties`: Om du vill spåra indata eller utdata i diagnostikdata kan du lägga till ett `trackedProperties` avsnitt i en åtgärd antingen med hjälp av Logic Apps designer eller direkt i din Logic Apps JSON-definition. Spårade egenskaper kan bara spåra en enda åtgärds indata och utdata, men du kan använda `correlation` egenskaperna för händelser för att korrelera mellan åtgärder i en körning. Om du vill spåra fler än en egenskap, lägger `trackedProperties` du till avsnittet och de egenskaper som du vill använda i åtgärds definitionen.

  Här är ett exempel som visar hur den **initierande variabeln** åtgärds definition innehåller spårade egenskaper från åtgärdens indata där indata är en matris, inte en post.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  I det här exemplet visas flera spårade egenskaper:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Det här exemplet visar hur `ActionCompleted` händelsen inkluderar `clientTrackingId` `trackedProperties` attributen och:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa frågor om övervakning och spårning](../logic-apps/create-monitoring-tracking-queries.md)
* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)
