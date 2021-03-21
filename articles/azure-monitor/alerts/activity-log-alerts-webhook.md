---
title: Förstå det webhook-schema som används i aktivitets logg aviseringar
description: Lär dig mer om schemat för JSON som publiceras i en webhook-URL när en aktivitets logg avisering aktive ras.
ms.topic: conceptual
ms.date: 03/31/2017
ms.openlocfilehash: 31b9f4b41d741475a031efd4392c7df2fd2260c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034344"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhookar för Azure aktivitets logg aviseringar
Som en del av definitionen av en åtgärds grupp kan du konfigurera webhook-slutpunkter för att ta emot aviseringar om aktivitets logg aviseringar. Med Webhooks kan du dirigera dessa meddelanden till andra system för efter bearbetning eller anpassade åtgärder. Den här artikeln visar vad nytto lasten för HTTP-inlägget till en webhook ser ut.

Mer information om aktivitets logg aviseringar finns i så här [skapar du Azure aktivitets logg aviseringar](./activity-log-alerts.md).

Information om åtgärds grupper finns i så här [skapar du åtgärds grupper](./action-groups.md).

> [!NOTE]
> Du kan också använda det [vanliga aviserings schemat](./alerts-common-schema.md), vilket ger fördelarna med att ha en enda utöknings bar och enhetlig aviserings nytto Last i alla aviserings tjänster i Azure Monitor, för dina webhook-integrationer. [Lär dig mer om vanliga aviserings schema definitioner.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan också använda token-baserad auktorisering för autentisering. Webhook-URI: n sparas med ett token-ID, till exempel `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue` .

## <a name="payload-schema"></a>Nytto Last schema
JSON-nyttolasten som ingår i POST-åtgärden skiljer sig från den nytto lastens data. context. activityLog. eventSource-fält.

> [!NOTE]
> För närvarande kopieras beskrivningen som är en del av aktivitets logg händelsen till den utlöst **varnings beskrivningen** .
>
> För att kunna justera nytto lasten för aktivitets loggen med andra aviserings typer, från och med den 1 april 2021, innehåller aviserings **regel beskrivningen i** stället.
>
> Vid förberedelse för den här ändringen skapade vi en ny egenskap **"aktivitets logg händelse Beskrivning"** i aviseringen om aktivitets loggen. Den här nya egenskapen fylls med egenskapen **"Description"** som redan är tillgänglig för användning. Det innebär att det nya fältet **"aktivitets logg händelse Beskrivning"** kommer att innehålla den beskrivning som ingår i aktivitets logg händelsen.
>
> Granska aviserings reglerna, åtgärds regler, webhookar, Logic app eller andra konfigurationer där du kan använda egenskapen **Beskrivning** från den aktiverade aviseringen och ersätt den med egenskapen **händelse Beskrivning för aktivitets logg** .
>
> Om ditt villkor (i åtgärds regler, Webhooks, Logic app eller andra konfigurationer) för närvarande baseras på egenskapen **"Beskrivning"** för aktivitets logg aviseringar, kan du behöva ändra den så att den baseras på egenskapen **"händelse Beskrivning för aktivitets loggen"** i stället.
>
> Du kan lägga till en beskrivning i varnings regel definitionen för att fylla i den nya egenskapen **Description** .
> ![Utlöst aktivitets logg aviseringar](media/activity-log-alerts-webhook/activity-log-alert-fired.png)

### <a name="common"></a>Common

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrativ

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Säkerhet

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Rekommendation

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Information om en detaljerad schema information om logg aviseringar för tjänst hälso aviseringar finns i [meddelanden om tjänst hälsa](../../service-health/service-notifications.md). Dessutom får du lära dig hur du [konfigurerar webhook-meddelanden för service Health med dina befintliga problem hanterings lösningar](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för mått varningar. Ställ alltid in på "aktive rad" för aktivitets logg aviseringar. |
| Edit |Händelsens kontext. |
| resourceProviderName |Resurs leverantören för den påverkade resursen. |
| conditionType |Always "event." |
| name |Aviserings regelns namn. |
| id |Resurs-ID för aviseringen. |
| beskrivning |Aviserings beskrivning som anges när aviseringen skapas. |
| subscriptionId |ID för Azure-prenumeration. |
| timestamp |Tiden då händelsen genererades av den Azure-tjänst som bearbetade begäran. |
| resourceId |Resurs-ID för den påverkade resursen. |
| resourceGroupName |Namnet på resurs gruppen för den påverkade resursen. |
| properties |Uppsättning `<Key, Value>` par (det vill säga `Dictionary<String, String>` ) som innehåller information om händelsen. |
| händelse |Element som innehåller metadata om händelsen. |
| auktorisering |Azures rollbaserade egenskaper för åtkomst kontroll för händelsen. Dessa egenskaper omfattar vanligt vis åtgärden, rollen och omfånget. |
| category |Händelsens kategori. De värden som stöds är administration, avisering, säkerhet, ServiceHealth och rekommendation. |
| anroparen |E-postadressen till den användare som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa system anrop. |
| correlationId |Vanligt vis ett GUID i sträng format. Händelser med correlationId tillhör samma större åtgärd och delar vanligt vis ett correlationId. |
| eventDescription |Statisk text Beskrivning av händelsen. |
| eventDataId |Unikt ID för händelsen. |
| eventSource |Namnet på den Azure-tjänst eller infrastruktur som genererade händelsen. |
| httpRequest |Begäran inkluderar vanligt vis metoden clientRequestId, clientIpAddress och HTTP (till exempel placering). |
| nivå |Ett av följande värden: kritisk, fel, varning och information. |
| operationId |Vanligt vis en GUID som delas mellan händelserna som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Händelsens egenskaper. |
| status |Sträng. Status för åtgärden. Vanliga värden är startad, pågår, lyckades, misslyckades, aktivt och löst. |
| subStatus |Innehåller vanligt vis HTTP-statuskod för motsvarande REST-anrop. Det kan också innehålla andra strängar som beskriver en under status. Vanliga under status värden är OK (HTTP-status kod: 200), skapad (HTTP-status kod: 201), godkänd (HTTP-status kod: 202), inget innehåll (HTTP-status kod: 204), felaktig begäran (HTTP-status kod: 400), hittades inte (HTTP-status kod: 404), konflikt (HTTP-status kod: 409), internt Server fel (HTTP-status kod: 500), tjänsten är inte tillgänglig (HTTP-status kod: 503) : 504). |

En detaljerad schema information om alla andra aktivitets logg aviseringar finns i [Översikt över Azure aktivitets loggen](../essentials/platform-logs-overview.md).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitets loggen](../essentials/platform-logs-overview.md).
* [Köra Azure Automation-skript (Runbooks) på Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Använd en Logic app för att skicka ett SMS via Twilio från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för mått varningar, men det kan ändras för att fungera med en aktivitets logg avisering.
* [Använd en Logic app för att skicka ett slack-meddelande från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för mått varningar, men det kan ändras för att fungera med en aktivitets logg avisering.
* [Använd en Logic app för att skicka ett meddelande till en Azure-kö från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för mått varningar, men det kan ändras för att fungera med en aktivitets logg avisering.