---
title: Azure Event Grid diagnostikloggar för ämnen eller domäner
description: Den här artikeln innehåller konceptuell information om diagnostiska loggar för ett Azure Event Grid-ämne eller en domän.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574994"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Diagnostikloggar för Azure Event Grid ämnen/domäner
Med diagnostikinställningar kan Event Grid användare fånga och visa **publicerings-och leverans fel** loggar i antingen ett lagrings konto, en händelsehubben eller en Log Analytics arbets yta. Den här artikeln innehåller schema för loggarna och en exempel logg post.


## <a name="schema-for-publishdelivery-failure-logs"></a>Schema för publicerings-/leverans problem loggar

| Egenskapsnamn | Datatyp | Beskrivning |
| ------------- | --------- | ----------- | 
| Tid | DateTime | Tiden då logg posten skapades <p>**Exempel värde:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | Sträng | Namnet på händelse prenumerationen <p>**Exempel värde:** "EVENTSUB1"</p> <p>Den här egenskapen finns bara för loggning av leverans felen.</p>  |
| Kategori | Sträng | Namnet på logg kategorin. <p>**Exempel värden:** "DeliveryFailures" eller "PublishFailures" | 
| OperationName | Sträng | Namnet på åtgärden orsakade ett haveri.<p>**Exempel värden:** "Leverera" för leverans problem. |
| Meddelande | Sträng | Logg meddelandet för användaren som förklarar orsaken till fel och ytterligare information. |
| ResourceId | Sträng | Resurs-ID för ämnet/domän resursen<p>**Exempel värden:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Exempel

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Nästa steg
Information om hur du aktiverar diagnostikloggar för ämnen eller domäner finns i [Aktivera diagnostikloggar](enable-diagnostic-logs-topic.md).
