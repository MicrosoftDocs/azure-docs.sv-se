---
title: Azure-signaler som Event Grid källa
description: Beskriver de egenskaper som har angetts för Azure SignalR-händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 321dc4d21485af23a9cca0d42d74da0a3e121b7c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363702"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid händelse schema för signal tjänsten

Den här artikeln innehåller egenskaper och schema för signalerar tjänst händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure SignalR som en händelse källa.


## <a name="available-event-types"></a>Tillgängliga händelse typer

SignalR tjänsten avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Utlöses när en klient anslutning anslöts. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Utlöses när en klient anslutning kopplades från. |

## <a name="example-event"></a>Exempel händelse

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)
I följande exempel visas schemat för en ansluten händelse för klient anslutning: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat för en frånkopplad klient anslutnings händelse är ungefär så här: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

I följande exempel visas schemat för en ansluten händelse för klient anslutning: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionConnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "specversion": "1.0"
}]
```

Schemat för en frånkopplad klient anslutnings händelse är ungefär så här: 

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "type": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "time": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Händelse egenskaper


# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)
En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `eventType` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `eventTime` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Signalerar tjänstens händelse data. |
| `dataVersion` | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| `metadataVersion` | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `source` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `type` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `time` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Signalerar tjänstens händelse data. |
| `specversion` | sträng | CloudEvents schema Specifikations version. |

---

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `timestamp` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `hubName` | sträng | Hubben som klient anslutningen tillhör. |
| `connectionId` | sträng | Klient anslutningens unika identifierare. |
| `userId` | sträng | Användar identifieraren som definierats i anspråk. |
| `errorMessage` | sträng | Det fel som gör att anslutningen kopplades från. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik | Beskrivning |
|---------|---------|
| [Reagera på händelser i Azure SignalR service genom att använda Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Översikt över integrering av Azure SignalR service med Event Grid. |
| [Så här skickar du Azure SignalR service-händelser till Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Visar hur du skickar Azure SignalR service-händelser till ett program via Event Grid. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
