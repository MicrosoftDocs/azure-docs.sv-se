---
title: Händelse schema för Azure aktivitets logg
description: Beskriver händelse schemat för varje kategori i Azure aktivitets loggen.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: a050e9832537dd9b6690c7f9409bfbb5b795af2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621402"
---
# <a name="azure-activity-log-event-schema"></a>Händelse schema för Azure aktivitets logg
[Azure aktivitets loggen](../platform/platform-logs-overview.md) ger inblick i alla händelser på prenumerations nivå som har inträffat i Azure. I den här artikeln beskrivs aktivitets logg kategorier och schemat för var och en. 

Schemat kan variera beroende på hur du kommer åt loggen:
 
- De scheman som beskrivs i den här artikeln är när du öppnar aktivitets loggen från [REST API](/rest/api/monitor/activitylogs). Detta är även det schema som används när du väljer **JSON** -alternativet när du visar en händelse i Azure Portal.
- Se det sista avsnittets [schema från lagrings konto och händelse nav](#schema-from-storage-account-and-event-hubs) för schemat när du använder en [diagnostisk inställning](../platform/diagnostic-settings.md) för att skicka aktivitets loggen till Azure Storage eller Azure Event Hubs.
- Se [Azure Monitor data referens](/azure/azure-monitor/reference/) för schemat när du använder en [diagnostisk inställning](../platform/diagnostic-settings.md) för att skicka aktivitets loggen till en Log Analytics arbets yta.

## <a name="severity-level"></a>Allvarlighetsnivå
Varje post i aktivitets loggen har en allvarlighets grad. Allvarlighets grad kan ha ett av följande värden:  

| Allvarlighetsgrad | Beskrivning |
|:---|:---|
| Kritisk | Händelser som kräver omedelbar uppmärksamhet på en system administratör. Kan tyda på att ett program eller system har misslyckats eller slutat svara.
| Fel | Händelser som indikerar ett problem, men som inte kräver omedelbara åtgärder.
| Varning | Händelser som ger forewarning av potentiella problem, även om det inte är ett faktiskt fel. Ange att en resurs inte är i ett idealiskt tillstånd och kan försämras senare till att visa fel eller kritiska händelser.  
| Information | Händelser som skickar icke-kritisk information till administratören. På ungefär samma sätt som med texten: "för din information". 

Ee för varje resurs leverantör väljer allvarlighets graderna för deras resurs poster. Det innebär att den faktiska allvarlighets graden kan variera beroende på hur programmet har skapats. Till exempel kanske objekt som är "kritiska" till en viss resurs som tas i isolering inte är lika viktiga som "fel" i en resurs typ som är central för Azure-programmet. Tänk på detta när du bestämmer vilka händelser som ska aviseras om.  

## <a name="categories"></a>Kategorier
Varje händelse i aktivitets loggen har en viss kategori som beskrivs i följande tabell. I avsnitten nedan finns mer information om varje kategori och dess schema när du öppnar aktivitets loggen från portalen, PowerShell, CLI och REST API. Schemat skiljer sig när du [strömmar aktivitets loggen till lagrings-eller Event Hubs](../platform/resource-logs.md#send-to-azure-event-hubs). En mappning av egenskaperna till schemat för [resurs loggar](../platform/resource-logs-schema.md) finns i det sista avsnittet i artikeln.

| Kategori | Beskrivning |
|:---|:---|
| [Administrativ](#administrative-category) | Innehåller posten över alla åtgärder för att skapa, uppdatera, ta bort och åtgärd som utförs via Resource Manager. Exempel på administrativa händelser är att _skapa en virtuell dator_ och _ta bort nätverks säkerhets grupp_.<br><br>Varje åtgärd som utförs av en användare eller ett program som använder Resource Manager är modellerad som en åtgärd på en viss resurs typ. Om åtgärds typen är _Skriv_, _ta bort_ eller _åtgärd_ registreras posterna för både start och lyckad eller misslyckad åtgärd i den administrativa kategorin. Administrativa händelser inkluderar även eventuella ändringar i Azure rollbaserad åtkomst kontroll i en prenumeration. |
| [Tjänsthälsa](#service-health-category) | Innehåller posten för eventuella service Health-incidenter som har inträffat i Azure. Ett exempel på en Service Health händelse _SQL Azure i östra USA drabbas av drift stopp_. <br><br>Service Health-händelser finns på sex sorter: _åtgärd krävs_, _stöd för återställning_, _incident_, _Underhåll_, _information_ eller _säkerhet_. Dessa händelser skapas endast om du har en resurs i prenumerationen som skulle påverkas av händelsen.
| [Resource Health](#resource-health-category) | Innehåller posten för eventuella resurs hälso händelser som har inträffat på dina Azure-resurser. Ett exempel på en Resource Health-händelse är _hälso statusen för den virtuella datorn har ändrats till otillgänglig_.<br><br>Resource Health händelser kan representera en av fyra hälso status: _tillgänglig_, otillgänglig _,_ _degraderad_ och _okänd_. Dessutom kan Resource Health händelser kategoriseras som plattform som _initieras_ eller _användaren initieras_. |
| [Varning](#alert-category) | Innehåller posten för aktiveringar för Azure-aviseringar. Ett exempel på en varnings händelse är _CPU% på myVM har varit över 80 under de senaste 5 minuterna_.|
| [Automatisk skalning](#autoscale-category) | Innehåller posten för alla händelser som rör driften av autoskalning-motorn baserat på de inställningar för autoskalning som du har definierat i din prenumeration. Ett exempel på en autoskalning-händelse är autoskalning- _åtgärden misslyckades_. |
| [Rekommendation](#recommendation-category) | Innehåller rekommendations händelser från Azure Advisor. |
| [Säkerhet](#security-category) | Innehåller posten för eventuella aviseringar som genererats av Azure Security Center. Ett exempel på en säkerhets händelse är _misstänkt dubbel tilläggs fil som körs_. |
| [Princip](#policy-category) | Innehåller poster med åtgärder som utförs av alla åtgärder som utförs av Azure Policy. Exempel på princip händelser är _granskning_ och _neka_. Varje åtgärd som utförs av principen är modellerad som en åtgärd på en resurs. |

## <a name="administrative-category"></a>Administrativ kategori
Den här kategorin innehåller posten över alla åtgärder för att skapa, uppdatera, ta bort och utföra åtgärder som utförs via Resource Manager. Exempel på typer av händelser som visas i den här kategorin är "Skapa virtuell dator" och "ta bort nätverks säkerhets grupp" varje åtgärd som vidtas av en användare eller ett program som använder Resource Manager är modellerad som en åtgärd på en viss resurs typ. Om åtgärds typen är Skriv, ta bort eller åtgärd registreras posterna för både start och lyckad eller misslyckad åtgärd i den administrativa kategorin. Den administrativa kategorin inkluderar även ändringar i Azure rollbaserad åtkomst kontroll i en prenumeration.

### <a name="sample-event"></a>Exempel händelse
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| auktorisering |BLOB för Azure RBAC-egenskaper för händelsen. Innehåller vanligt vis egenskaperna "Action", "roll" och "omfattning". |
| anroparen |E-postadressen till den användare som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. |
| kanal |Ett av följande värden: "admin", "åtgärd" |
| gällande |JWT-token som används av Active Directory för att autentisera användaren eller programmet för att utföra den här åtgärden i Resource Manager. |
| correlationId |Vanligt vis ett GUID i sträng formatet. Händelser som delar ett correlationId tillhör samma Uber-åtgärd. |
| beskrivning |Statisk text Beskrivning av en händelse. |
| eventDataId |Unikt ID för en händelse. |
| eventName | Eget namn på den administrativa händelsen. |
| category | Alltid "administrativ" |
| httpRequest |BLOB som beskriver http-begäran. Innehåller vanligt vis "clientRequestId", "clientIpAddress" och "Method" (HTTP-metod. Till exempel placering). |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning" och "information" |
| resourceGroupName |Namnet på resurs gruppen för den påverkade resursen. |
| resourceProviderName |Namnet på resurs leverantören för den påverkade resursen |
| resourceType | Den typ av resurs som påverkades av en administrativ händelse. |
| resourceId |Resurs-ID för den påverkade resursen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver information om händelsen. |
| status |Sträng som beskriver status för åtgärden. Några vanliga värden är: startad, pågår, lyckades, misslyckades, aktiv, löst. |
| subStatus |Vanligt vis är HTTP-statuskoden för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en under status. till exempel följande vanliga värden: OK (HTTP-status kod: 200), skapad (HTTP-status kod: 201), godkänd (HTTP-status kod: 202), inget innehåll (HTTP-status kod: 204), felaktig begäran (HTTP-status kod: 400), (HTTP-status kod: 404), konflikt (HTTP-status kod) : 409), internt Server fel (HTTP-status kod: 500), tjänsten är inte tillgänglig (HTTP-status kod: 503), Gateway-tidsgräns (HTTP-status kod: 504). |
| eventTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |

## <a name="service-health-category"></a>Tjänste hälso kategori
Den här kategorin innehåller posten för eventuella service Health-incidenter som har inträffat i Azure. Ett exempel på vilken typ av händelse som visas i den här kategorin är "SQL Azure i östra USA upplever drift stopp." Tjänste hälso händelser finns i fem sorter: åtgärd krävs, stöd för återställning, incident, underhåll, information eller säkerhet, och endast om du har en resurs i prenumerationen som skulle påverkas av händelsen.

### <a name="sample-event"></a>Exempel händelse
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Information om värdena i egenskaperna hittar du i artikeln [tjänst hälso aviseringar](../../service-health/service-notifications.md) .

## <a name="resource-health-category"></a>Resurs hälso kategori
Den här kategorin innehåller posten för eventuella resurs hälso händelser som har inträffat på dina Azure-resurser. Ett exempel på vilken typ av händelse som visas i den här kategorin är "statusen för den virtuella datorns hälso status har ändrats till ej tillgänglig". Resurs hälso händelser kan representera en av fyra hälso status: tillgänglig, otillgänglig, degraderad och okänd. Dessutom kan resurs hälso händelser kategoriseras som påbörjad som plattform eller initieras av användaren.

### <a name="sample-event"></a>Exempel händelse

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| kanal | Always "admin, operation" |
| correlationId | Ett GUID i sträng formatet. |
| beskrivning |Statisk text Beskrivning av aviserings händelsen. |
| eventDataId |Unikt ID för aviserings händelsen. |
| category | Always "ResourceHealth" |
| eventTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning", "information" och "utförlig" |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| resourceGroupName |Namnet på den resurs grupp som innehåller resursen. |
| resourceProviderName |Always Microsoft. Resourcehealth/healthevent/action. |
| resourceType | Den typ av resurs som påverkades av en Resource Health händelse. |
| resourceId | Namnet på resurs-ID för den påverkade resursen. |
| status |Sträng som beskriver hälso händelsens status. Värdena kan vara: aktiva, lösta, pågående och uppdaterade. |
| subStatus | Normalt null för aviseringar. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver information om händelsen.|
| egenskaper. title | En användarvänlig sträng som beskriver resursens hälso status. |
| egenskaper. information | En användarvänlig sträng som beskriver mer information om händelsen. |
| egenskaper. currentHealthStatus | Resursens aktuella hälso status. Ett av följande värden: "tillgänglig", "otillgänglig", "degraderad" och "okänd". |
| egenskaper. previousHealthStatus | Resursens tidigare hälso status. Ett av följande värden: "tillgänglig", "otillgänglig", "degraderad" och "okänd". |
| egenskaper. typ | En beskrivning av typen av resurs hälso händelse. |
| egenskaper. orsak | En beskrivning av orsaken till resursens hälso tillstånds händelse. Antingen "UserInitiated" och "PlatformInitiated". |


## <a name="alert-category"></a>Aviseringskategori
Den här kategorin innehåller posten över alla aktiveringar av klassiska Azure-aviseringar. Ett exempel på vilken typ av händelse som visas i den här kategorin är "CPU% på myVM har varit över 80 under de senaste 5 minuterna." En rad olika Azure-system har ett varnings begrepp – du kan definiera en regel för viss sortering och få ett meddelande när villkoren matchar regeln. Varje gång som en Azure-aviserings typ som stöds aktive ras, eller villkoret uppfylls för att generera ett meddelande, skickas även en post över aktiveringen till den här kategorin i aktivitets loggen.

### <a name="sample-event"></a>Exempel händelse

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| anroparen | Always Microsoft. Insights/alertRules |
| kanal | Always "admin, operation" |
| gällande | JSON-BLOB med SPN (tjänstens huvud namn) eller resurs typ för aviserings motorn. |
| correlationId | Ett GUID i sträng formatet. |
| beskrivning |Statisk text Beskrivning av aviserings händelsen. |
| eventDataId |Unikt ID för aviserings händelsen. |
| category | Alltid "varning" |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning" och "information" |
| resourceGroupName |Namnet på resurs gruppen för den påverkade resursen, om det är en mått varning. För andra aviserings typer är det namnet på den resurs grupp som innehåller själva aviseringen. |
| resourceProviderName |Namnet på resurs leverantören för den påverkade resursen, om det är en mått varning. För andra aviserings typer är det namnet på resurs leverantören för själva aviseringen. |
| resourceId | Namnet på resurs-ID för den påverkade resursen, om det är en mått varning. För andra aviserings typer är det resurs-ID för själva aviserings resursen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver information om händelsen. |
| status |Sträng som beskriver status för åtgärden. Några vanliga värden är: startad, pågår, lyckades, misslyckades, aktiv, löst. |
| subStatus | Normalt null för aviseringar. |
| eventTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |

### <a name="properties-field-per-alert-type"></a>Egenskaps fält per aviserings typ
Egenskaps fältet innehåller olika värden beroende på källan för aviserings händelsen. Två vanliga aviserings händelse leverantörer är aktivitets logg aviseringar och mått varningar.

#### <a name="properties-for-activity-log-alerts"></a>Egenskaper för aktivitets logg aviseringar
| Elementnamn | Description |
| --- | --- |
| egenskaper. subscriptionId | Prenumerations-ID: t från aktivitets logg händelsen som gjorde att den här varnings regeln för aktivitets loggen aktiverades. |
| egenskaper. eventDataId | Händelse data-ID: t från aktivitets logg händelsen som gjorde att den här aviserings regeln för aktivitets loggen aktiverades. |
| egenskaper. resourceGroup | Resurs gruppen från aktivitets logg händelsen som gjorde att den här varnings regeln för aktivitets loggen aktiverades. |
| egenskaper. resourceId | Resurs-ID: t från aktivitets logg händelsen som gjorde att den här varnings regeln för aktivitets loggen aktiverades. |
| egenskaper. eventTimestamp | Händelse-tidsstämpeln för aktivitets logg händelsen som gjorde att den här varnings regeln för aktivitets loggen aktiverades. |
| egenskaper. operationName | Åtgärds namnet från aktivitets logg händelsen som gjorde att den här varnings regeln för aktivitets loggen aktiverades. |
| egenskaper. status | Status från aktivitets logg händelsen som gjorde att den här aviserings regeln för aktivitets loggen aktiverades.|

#### <a name="properties-for-metric-alerts"></a>Egenskaper för mått varningar
| Elementnamn | Description |
| --- | --- |
| egenskaperna. RuleUri | Resurs-ID för mått varnings regeln. |
| egenskaperna. RuleName | Namnet på måttets aviserings regel. |
| egenskaperna. RuleDescription | Beskrivning av måttets aviserings regel (enligt definitionen i aviserings regeln). |
| egenskaperna. Fastställd | Tröskelvärdet som används i utvärderingen av måttets varnings regel. |
| egenskaperna. WindowSizeInMinutes | Fönster storleken som används i utvärderingen av måttets varnings regel. |
| egenskaperna. Aggregat | Den agg regerings typ som definierats i måttets aviserings regel. |
| egenskaperna. Operator | Den villkorliga operator som används i utvärderingen av måttets varnings regel. |
| egenskaperna. MetricName | Mått namnet för måttet som används i utvärderingen av måttets varnings regel. |
| egenskaperna. MetricUnit | Mått enheten för måttet som används i utvärderingen av måttets varnings regel. |

## <a name="autoscale-category"></a>Autoskalning-kategori
Den här kategorin innehåller posten för alla händelser som rör driften av autoskalning-motorn baserat på de inställningar för autoskalning som du har definierat i din prenumeration. Ett exempel på den typ av händelse som du skulle se i den här kategorin är "Det gick inte att utföra autoskalning åtgärder". Med autoskalning kan du automatiskt skala ut eller skala antalet instanser i en resurs typ som stöds baserat på tid på dag och/eller läsa in (mått) data med hjälp av en inställning för automatisk skalning. När villkoren uppfylls för att skala upp eller ned registreras start-och lyckade eller misslyckade händelser i den här kategorin.

### <a name="sample-event"></a>Exempel händelse
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| anroparen | Always Microsoft. Insights/autoscaleSettings |
| kanal | Always "admin, operation" |
| gällande | JSON-BLOB med SPN (tjänstens huvud namn) eller resurs typ för den automatiska skalnings motorn. |
| correlationId | Ett GUID i sträng formatet. |
| beskrivning |Statisk text Beskrivning av händelsen autoskalning. |
| eventDataId |Unikt ID för autoskalning-händelsen. |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning" och "information" |
| resourceGroupName |Namnet på resurs gruppen för den automatiska skalnings inställningen. |
| resourceProviderName |Namnet på resurs leverantören för den automatiska skalnings inställningen. |
| resourceId |Resurs-ID för den automatiska skalnings inställningen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver information om händelsen. |
| egenskaperna. Beteckning | Detaljerad beskrivning av vad den automatiska skalnings motorn gjorde. |
| egenskaperna. ResourceName | Resurs-ID för den påverkade resursen (resursen som skalnings åtgärden utfördes på) |
| egenskaperna. OldInstancesCount | Antalet instanser innan autoskalning-åtgärden genomfördes. |
| egenskaperna. NewInstancesCount | Antalet instanser efter den automatiska skalnings åtgärden som genomfördes. |
| egenskaperna. LastScaleActionTime | Tidsstämpeln för när åtgärden för autoskalning utfördes. |
| status |Sträng som beskriver status för åtgärden. Några vanliga värden är: startad, pågår, lyckades, misslyckades, aktiv, löst. |
| subStatus | Normalt null för autoskalning. |
| eventTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |

## <a name="security-category"></a>Säkerhets kategori
Den här kategorin innehåller de aviseringar som genererats av Azure Security Center. Ett exempel på vilken typ av händelse som visas i den här kategorin är "misstänkt fil med dubbla tillägg som körs".

### <a name="sample-event"></a>Exempel händelse
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| kanal | Always "operation" |
| correlationId | Ett GUID i sträng formatet. |
| beskrivning |Statisk text Beskrivning av säkerhets händelsen. |
| eventDataId |Unik identifierare för säkerhets händelsen. |
| eventName |Eget namn på säkerhets händelsen. |
| category | Alltid "säkerhet" |
| ID |Unikt resurs-ID för säkerhets händelsen. |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning" eller "information" |
| resourceGroupName |Namnet på resurs gruppen för resursen. |
| resourceProviderName |Namnet på resurs leverantören för Azure Security Center. Alltid "Microsoft. Security". |
| resourceType |Den typ av resurs som skapade säkerhets händelsen, till exempel "Microsoft. Security/locations/Alerts" |
| resourceId |Resurs-ID för säkerhets aviseringen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName |Åtgärdens namn. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver information om händelsen. Egenskaperna varierar beroende på vilken typ av säkerhets avisering som används. Se [den här sidan](../../security-center/security-center-alerts-overview.md) för en beskrivning av de typer av aviseringar som kommer från Security Center. |
| egenskaperna. Allvarlighets grad |Allvarlighets grad. Möjliga värden är "hög", "medium" eller "låg". |
| status |Sträng som beskriver status för åtgärden. Några vanliga värden är: startad, pågår, lyckades, misslyckades, aktiv, löst. |
| subStatus | Brukar vara null för säkerhets händelser. |
| eventTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |

## <a name="recommendation-category"></a>Rekommendations kategori
Den här kategorin innehåller posten för eventuella nya rekommendationer som genereras för dina tjänster. Ett exempel på en rekommendation är "Använd tillgänglighets uppsättningar för förbättrad fel tolerans". Det finns fyra typer av rekommendations händelser som kan genereras: hög tillgänglighet, prestanda, säkerhet och kostnads optimering. 

### <a name="sample-event"></a>Exempel händelse
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Egenskaps beskrivningar
| Elementnamn | Description |
| --- | --- |
| kanal | Always "operation" |
| correlationId | Ett GUID i sträng formatet. |
| beskrivning |Statisk text Beskrivning av rekommendations händelsen |
| eventDataId | Unikt ID för rekommendations händelsen. |
| category | Alltid "rekommendation" |
| ID |Unikt resurs-ID för rekommendations händelsen. |
| nivå |Händelsens nivå. Ett av följande värden: "kritisk", "fel", "varning" eller "information" |
| operationName |Åtgärdens namn.  Always Microsoft. Advisor/generateRecommendations/Action|
| resourceGroupName |Namnet på resurs gruppen för resursen. |
| resourceProviderName |Namnet på resurs leverantören för den resurs som den här rekommendationen gäller för, till exempel "MICROSOFT. Compute" |
| resourceType |Namnet på resurs typen för den resurs som den här rekommendationen gäller för, till exempel "MICROSOFT. Compute/virtualmachines" |
| resourceId |Resurs-ID för den resurs som rekommendationen gäller |
| status | Always "Active" |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |ID för Azure-prenumeration. |
| properties |En uppsättning `<Key, Value>` par (dvs. en ord lista) som beskriver rekommendationernas information.|
| egenskaper. recommendationSchemaVersion| Schema version för de rekommenderade egenskaperna som publicerats i aktivitets logg posten |
| egenskaper. recommendationCategory | Rekommendationens kategori. Möjliga värden är "hög tillgänglighet", "prestanda", "säkerhet" och "kostnad" |
| egenskaper. recommendationImpact| Rekommendationens effekt. Möjliga värden är "hög", "medium", "Low" |
| egenskaper. recommendationRisk| Risk för rekommendationen. Möjliga värden är "Error", "Warning", "none" |

## <a name="policy-category"></a>Princip kategori

Den här kategorin innehåller poster med åtgärder som utförs av alla åtgärder som utförs av [Azure policy](../../governance/policy/overview.md). Exempel på typer av händelser som du ser i den här kategorin är _granskning_ och _neka_. Varje åtgärd som utförs av principen är modellerad som en åtgärd på en resurs.

### <a name="sample-policy-event"></a>Exempel på princip händelse

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Beskrivning av princip händelse egenskaper

| Elementnamn | Description |
| --- | --- |
| auktorisering | Matris med Azure RBAC-egenskaper för händelsen. För nya resurser är detta åtgärd och omfattning för den begäran som utlöste utvärderingen. För befintliga resurser är åtgärden "Microsoft. Resources/checkPolicyCompliance/Read". |
| anroparen | För nya resurser är identiteten som initierade en distribution. För befintliga resurser är GUID för Microsoft Azure policyn för insikter RP. |
| kanal | Princip händelser använder bara kanalen "åtgärd". |
| gällande | JWT-token som används av Active Directory för att autentisera användaren eller programmet för att utföra den här åtgärden i Resource Manager. |
| correlationId | Vanligt vis ett GUID i sträng formatet. Händelser som delar ett correlationId tillhör samma Uber-åtgärd. |
| beskrivning | Det här fältet är tomt för princip händelser. |
| eventDataId | Unikt ID för en händelse. |
| eventName | Antingen "BeginRequest" eller "EndRequest". "BeginRequest" används för fördröjda auditIfNotExists-och deployIfNotExists-utvärderingar och när en deployIfNotExists-påverkan startar en mall-distribution. Alla andra åtgärder returnerar "EndRequest". |
| category | Deklarerar aktivitets logg händelsen som tillhöra "princip". |
| eventTimestamp | Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| ID | Unikt ID för händelsen för den specifika resursen. |
| nivå | Händelsens nivå. Granskningen använder "varning" och neka använder "Error". Ett auditIfNotExists-eller deployIfNotExists-fel kan generera "varning" eller "fel" beroende på allvarlighets grad. Alla andra princip händelser använder "information". |
| operationId | Ett GUID som delas mellan de händelser som motsvarar en enskild åtgärd. |
| operationName | Namnet på åtgärden och korrelerar direkt till princip påverkan. |
| resourceGroupName | Namnet på resurs gruppen för den utvärderade resursen. |
| resourceProviderName | Namnet på resurs leverantören för den utvärderade resursen. |
| resourceType | För nya resurser är det den typ som utvärderas. För befintliga resurser returnerar "Microsoft. Resources/checkPolicyCompliance". |
| resourceId | Resurs-ID för den utvärderade resursen. |
| status | Sträng som beskriver status för princip utvärderings resultatet. De flesta princip utvärderingar returnerar "lyckades", men en neka-inverkan returnerar "misslyckades". Fel i auditIfNotExists eller deployIfNotExists returnerar även "misslyckades". |
| subStatus | Fältet är tomt för princip händelser. |
| submissionTimestamp | Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId | ID för Azure-prenumeration. |
| egenskaper. isComplianceCheck | Returnerar "false" när en ny resurs distribueras eller om en befintlig resurss Resource Manager-egenskaper uppdateras. Alla andra [utvärderings utlösare](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resulterar i "true". |
| egenskaper. resourceLocation | Azure-regionen för den resurs som utvärderas. |
| egenskaper. överordnade | En kommaavgränsad lista över överordnade hanterings grupper som har beställts från direkt överordnad till längst till föräldrar. |
| egenskaper. principer | Innehåller information om princip definitionen, tilldelningen, effekterna och parametrarna som denna princip utvärdering är resultatet av. |
| relatedEvents | Det här fältet är tomt för princip händelser. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema från lagrings konto och händelse nav
När du strömmar Azure-aktivitets loggen till ett lagrings konto eller en händelsehubben, följer data [schemat för resurs loggen](../platform/resource-logs-schema.md). Tabellen nedan innehåller en mappning av egenskaper från ovanstående scheman till resursens loggar schema.

> [!IMPORTANT]
> Formatet på de aktivitets logg data som skrivs till ett lagrings konto har ändrats till JSON-linjer på nov. 1st, 2018. Se [förbereda för format ändring till Azure Monitor resurs loggar arkiverade på ett lagrings konto](../platform/resource-logs-blob-format.md) för information om det här formatet.


| Schema egenskap för resurs loggar | Aktivitets logg REST API schema egenskap | Kommentarer |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName är härledda från resourceId. |
| operationName | operationName. Value |  |
| category | Del av åtgärds namn | Grupp av åtgärds typen-"Skriv"/"ta bort"/"åtgärd" |
| resultType | status. Value | |
| resultSignature | under status. värde | |
| resultDescription | beskrivning |  |
| durationMs | Ej tillämpligt | Alltid 0 |
| callerIpAddress | httpRequest. clientIpAddress |  |
| correlationId | correlationId |  |
| identity | anspråk och egenskaper för auktorisering |  |
| Nivå | Nivå |  |
| location | Ej tillämpligt | Platsen där händelsen bearbetades. *Detta är inte platsen för resursen, utan i stället där händelsen bearbetades. Den här egenskapen tas bort i en framtida uppdatering.* |
| Egenskaper | egenskaper. eventProperties |  |
| egenskaper. eventCategory | category | Om Properties. eventCategory inte finns är kategorin "administrativ" |
| egenskaper. eventName | eventName |  |
| egenskaper. operationId | operationId |  |
| egenskaper. eventProperties | properties |  |

Följande är ett exempel på en händelse som använder det här schemat..

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitets loggen](../platform/platform-logs-overview.md)
* [Skapa en diagnostisk inställning för att skicka aktivitets loggen till Log Analytics arbets yta, Azure Storage eller Event Hub](../platform/diagnostic-settings.md)
