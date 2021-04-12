---
title: Utlös komplexa åtgärder med Azure Monitor aviseringar
description: Lär dig hur du skapar en logisk app-åtgärd för att bearbeta Azure Monitor aviseringar.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029853"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Så här utlöser du komplexa åtgärder med Azure Monitor aviseringar

Den här artikeln visar hur du konfigurerar och utlöser en Logic app för att skapa en konversation i Microsoft Teams när en avisering utlöses.

## <a name="overview"></a>Översikt

När en Azure Monitor-avisering utlöses anropar den en [Åtgärds grupp](./action-groups.md). Med åtgärds grupper kan du utlösa en eller flera åtgärder för att meddela andra om en avisering och även åtgärda det.

Den allmänna processen är:

-   Skapa Logic-appen för respektive aviserings typ.

-   Importera en exempel nytto last för respektive aviserings typ till Logic-appen.

-   Definiera beteendet för Logic app.

-   Kopiera HTTP-slutpunkten för Logic-appen till en Azure-åtgärds grupp.

Processen påminner om du vill att Logic app ska utföra en annan åtgärd.

## <a name="create-an-activity-log-alert-administrative"></a>Skapa en aktivitets logg avisering: administrativ

1. [Skapa en Logic-app](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md).

1.  Välj utlösaren: **när en HTTP-begäran tas emot**.

1. I dialog rutan **när en HTTP-begäran tas emot** väljer **du Använd exempel nytto last för att generera schemat**.

    ![Skärm bild som visar dialog rutan när en H T T P P-begäran och alternativet Använd exempel nytto last för att skapa schema har valts. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  Kopiera och klistra in följande exempel nytto Last i dialog rutan:

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. **Logic Apps designer** visar ett popup-fönster för att påminna dig om att begäran som skickas till Logic-appen måste ange **Content-Type-** huvudet till **Application/JSON**. Stäng popup-fönstret. Azure Monitors aviseringen anger sidhuvudet.

    ![Ange rubrik för innehålls typ](media/action-groups-logic-app/content-type-header.png &quot;Ange rubrik för innehålls typ")

1. Välj **+** **nytt steg** och välj sedan **Lägg till en åtgärd**.

    ![Lägga till en åtgärd](media/action-groups-logic-app/add-action.png "Lägga till en åtgärd")

1. Sök efter och välj Microsoft Teams-anslutningsprogrammet. Välj åtgärden **Microsoft Teams-publicera meddelande** .

    ![Microsoft Teams-åtgärder](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-åtgärder")

1. Konfigurera Microsoft Teams-åtgärden. **Logic Apps designer** ber dig att autentisera till ditt arbets-eller skol konto. Välj det **Team-ID** och **kanal-ID** som meddelandet ska skickas till.

13. Konfigurera meddelandet med hjälp av en kombination av statisk text och referenser till \<fields\> i det dynamiska innehållet. Kopiera och klistra in följande text i **meddelande** fältet:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Sök sedan efter och Ersätt \<fields\> med dynamiska innehålls etiketter med samma namn.

    > [!NOTE]
    > Det finns två dynamiska fält med namnet **status**. Lägg till båda fälten i meddelandet. Använd fältet i **activityLog** egenskaps påse och ta bort det andra fältet. Håll markören över fältet **status** för att se den fullständigt kvalificerade fält referensen, som visas på följande skärm bild:

    ![Microsoft Teams-åtgärd: publicera ett meddelande](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-åtgärd: publicera ett meddelande")

1. Överst i **Logic Apps designer** väljer du **Spara** för att spara din Logic app.

1. Öppna den befintliga åtgärds gruppen och Lägg till en åtgärd som refererar till Logic app. Om du inte har en befintlig åtgärds grupp kan du läsa [skapa och hantera åtgärds grupper i Azure Portal](./action-groups.md) för att skapa en. Glöm inte att spara ändringarna.

    ![Uppdatera åtgärds gruppen](media/action-groups-logic-app/update-action-group.png "Uppdatera åtgärds gruppen")

Nästa gången en avisering anropar din åtgärds grupp, anropas din Logic Apps.

## <a name="create-a-service-health-alert"></a>Skapa en avisering om tjänstens hälsa

Azure Service Health poster ingår i aktivitets loggen. Processen för att skapa aviseringen liknar att [skapa en aktivitets logg avisering](#create-an-activity-log-alert-administrative), men med några få ändringar:

- Steg 1 till 3 är desamma.
- I steg 4 använder du följande exempel nytto last för utlösare för HTTP-begäran:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Steg 5 och 6 är desamma.
-  I steg 7 till 11 använder du följande process:

   1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor så att Logic app bara körs när indatan matchar värdena nedan.  När du anger version svärdet i text rutan sätter du citat tecken runt dem ("0.1.1") för att se till att den utvärderas som en sträng och inte en numerisk typ.  Systemet visar inte citat tecknen om du återgår till sidan, men den underliggande koden behåller fortfarande sträng typen.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Villkor för Service Health nytto Last"](media/action-groups-logic-app/service-health-payload-condition.png "Villkor för Service Health nytto Last")

   1. I villkoret **om sant** följer du anvisningarna i steg 11 till 13 i [skapa en aktivitets logg avisering](#create-an-activity-log-alert-administrative) för att lägga till åtgärden Microsoft Teams.

   1. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll. Kopiera och klistra in följande innehåll i **meddelande** fältet. Ersätt `[incidentType]` fälten, `[trackingID]` , `[title]` och `[communication]` med dynamiska innehålls etiketter med samma namn:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health True condition post Action"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Health True condition post-åtgärd")

   1. För villkoret **om falskt** anger du ett användbart meddelande:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health falsk villkor post åtgärd"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Service Health falsk villkors post åtgärd")

- Steg 15 är detsamma. Följ anvisningarna för att spara din Logic app och uppdatera åtgärds gruppen.

## <a name="create-a-metric-alert"></a>Skapa en måtta avisering

Processen för att skapa en måtta aviseringar liknar att [skapa en aktivitets logg avisering](#create-an-activity-log-alert-administrative), men med några få ändringar:

- Steg 1 till 3 är desamma.
- I steg 4 använder du följande exempel nytto last för utlösare för HTTP-begäran:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Steg 5 och 6 är desamma.
- I steg 7 till 11 använder du följande process:

  1. Välj **+** **nytt steg** och välj sedan **Lägg till ett villkor**. Ange följande villkor så att Logic app bara körs när indatan matchar dessa värden nedan. När du anger version svärdet i text rutan sätter du citat tecken runt dem ("2,0") för att se till att den utvärderas som en sträng och inte en numerisk typ.  Systemet visar inte citat tecknen om du återgår till sidan, men den underliggande koden behåller fortfarande sträng typen. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Villkor för nytto lastens nytto Last"](media/action-groups-logic-app/metric-alert-payload-condition.png "Villkor för nytto last för mått")

  1. I villkoret **om sant** lägger du till ett **för varje** slinga och åtgärden Microsoft Teams. Definiera meddelandet med hjälp av en kombination av HTML och dynamiskt innehåll.

      !["Mått avisering True condition post åtgärd"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Mått avisering, True condition post-åtgärd")

  1. I villkoret **om falskt** definierar du en Microsoft Teams-åtgärd för att kommunicera att mått aviseringen inte matchar förväntningarna i Logic app. Ta med JSON-nyttolasten. Observera hur du refererar till det `triggerBody` dynamiska innehållet i `json()` uttrycket.

      !["Mått varning falsk villkor post åtgärd"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Mått avisering, felaktig villkors post åtgärd")

- Steg 15 är detsamma. Följ anvisningarna för att spara din Logic app och uppdatera åtgärds gruppen.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Anropa andra program förutom Microsoft Teams
Logic Apps har flera olika anslutningar som gör att du kan utlösa åtgärder i en mängd olika program och databaser. Slack, SQL Server, Oracle, Salesforce, är bara några exempel. Mer information om anslutningar finns i [Logic app-kopplingar](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Nästa steg
* Få en [Översikt över Azure aktivitets logg aviseringar](./alerts-overview.md) och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett Azure Service Health-meddelande publiceras](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Läs mer om [Åtgärds grupper](./action-groups.md).
