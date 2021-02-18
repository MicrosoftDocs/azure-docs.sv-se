---
title: Skicka Azure Service Health-aviseringar med ServiceNow
description: Få personligt anpassade meddelanden om service Health-händelser till din ServiceNow-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588194"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Skicka Azure Service Health-aviseringar med ServiceNow med webhookar

Den här artikeln visar hur du integrerar Azure Service Health-aviseringar med ServiceNow med hjälp av en webhook. När du har konfigurerat webhook-integrering med ServiceNow-instansen får du aviseringar via din befintliga aviserings infrastruktur när problem med Azure-tjänsten påverkar dig. Varje gång en Azure Service Health-avisering utlöses anropar den en webhook via ServiceNow skript REST API.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Skapa ett skript REST API i ServiceNow

1.  Kontrol lera att du har registrerat dig för och är inloggad på ditt [ServiceNow](https://www.servicenow.com/) -konto.

1.  Gå till avsnittet **system Web Services** i ServiceNow och välj **skriptade REST-API: er**.

    ![Avsnittet "skriptad webb tjänst" i ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Välj **nytt** om du vill skapa en ny skriptad REST-tjänst.
 
    ![Knappen "nytt skript REST API" i ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Lägg till ett **namn** i REST API och ange **API-id: t** `azureservicehealth` .

1.  Välj **Skicka**.

    ![REST API inställningar i ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Välj den REST API som du skapade och välj **ny** under fliken **resurser** .

    ![Fliken "resurs" i ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Ge** den nya resursen ett namn `event` och ändra **http-metoden** till `POST` .

1.  Lägg till följande JavaScript-kod i avsnittet **skript** :

    >[!NOTE]
    >Du måste uppdatera `<secret>` `<group>` värdet, och `<email>` i skriptet nedan.
    >* `<secret>` ska vara en slumpmässig sträng, t. ex. ett GUID
    >* `<group>` ska vara ServiceNow-gruppen som du vill tilldela incidenten till
    >* `<email>` ska vara den enskilde person som du vill tilldela incidenten till (valfritt)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Avmarkera **kräver autentisering** på fliken säkerhet och välj **Skicka**. `<secret>`Du anger skyddar detta API i stället.

    ![Kryss rutan Kräv autentisering i ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  I avsnittet Skripted REST-API: er ska du hitta **bas-API-sökvägen** för din nya REST API:

     !["Bas-API-sökväg" i ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Din fullständiga integrations-URL ser ut så här:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Skapa en avisering med ServiceNow i Azure Portal
### <a name="for-a-new-action-group"></a>För en ny åtgärds grupp:
1. Följ steg 1 till 8 i [den här artikeln](./alerts-activity-log-service-notifications-portal.md) för att skapa en avisering med en ny åtgärds grupp.

1. Definiera i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** ServiceNow- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärds grupp:
1. I [Azure Portal](https://portal.azure.com/)väljer du **övervaka**.

1. I avsnittet **Inställningar** väljer du **Åtgärds grupper**.

1. Sök efter och välj den åtgärds grupp som du vill redigera.

1. Lägg till i listan med **åtgärder**:

    a. **Åtgärds typ:** *webhook*

    b. **Information:** ServiceNow- **integreringens URL** som du sparade tidigare.

    c. **Namn:** Webhookens namn, alias eller identifierare.

1. Välj **Spara** när du är färdig om du vill uppdatera åtgärds gruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa den tjänst hälso nytto last som du vill skicka. Du hittar ett exempel på en service Health webhook-nyttolast vid [Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få ett `200 OK` svar med meddelandet "incident skapad".

1. Gå till [ServiceNow](https://www.servicenow.com/) för att bekräfta att din integrering har kon figurer ATS korrekt.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Läs mer om [meddelanden om tjänst hälsa](./service-notifications.md).
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
