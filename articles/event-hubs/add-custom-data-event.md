---
title: Lägg till anpassade data i händelser i Azure Event Hubs
description: Den här artikeln visar hur du lägger till anpassade data i händelser i Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893482"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Lägg till anpassade data i händelser i Azure Event Hubs
Eftersom en händelse består huvudsakligen av en ogenomskinlig uppsättning byte, kan det vara svårt för användare av dessa händelser att fatta välgrundade beslut om hur de ska bearbetas. För att händelse utgivare ska kunna erbjuda bättre kontext för konsumenter kan händelser också innehålla anpassade metadata i form av en uppsättning nyckel/värde-par. Ett vanligt scenario för att inkludera metadata är att ge ett tips om vilken typ av data som finns i en händelse, så att kunderna förstår formatet och kan deserialisera det på lämpligt sätt.

> [!NOTE]
> Dessa metadata används inte av eller på något sätt meningsfullt för Event Hubs tjänsten. Det finns bara för samordning mellan händelse utgivare och konsumenter. 

I följande avsnitt visas hur du lägger till anpassade data i händelser i olika programmeringsspråk. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Ett fullständigt kod exempel finns i [publicera händelser med anpassade metadata](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Ett fullständigt kod exempel finns i [publicera händelser med anpassade metadata](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Ett fullständigt kod exempel finns i avsnittet [skicka händelse data med egenskaper](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Nästa steg
Se följande snabb starter och exempel. 

- Snabb starter: [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [python](event-hubs-python-get-started-send.md), [Java Script](event-hubs-node-get-started-send.md)
- Exempel på GitHub: [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [Java Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [typescript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
