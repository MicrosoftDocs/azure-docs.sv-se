---
title: Azure Service Bus som Event Grid källa
description: Beskriver de egenskaper som har angetts för Service Bus händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34c6990c4e6e87304c457a5b2ca6459c404c8d9a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008120"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Service Bus händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Service Bus avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Service Bus. ActiveMessagesAvailableWithNoListeners | Utlöses när det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar. |
| Microsoft. Service Bus. DeadletterMessagesAvailableWithNoListener | Utlöses när det finns aktiva meddelanden i en kö för obeställbara meddelanden och inga aktiva lyssnare. |
| Microsoft. Service Bus. ActiveMessagesAvailablePeriodicNotifications | Aktive ras regelbundet om det finns aktiva meddelanden i en kö eller prenumeration, även om det finns aktiva lyssnare på den aktuella kön eller prenumerationen. |
| Microsoft. Service Bus. DeadletterMessagesAvailablePeriodicNotifications | Aktive ras regelbundet om det finns meddelanden i obeställbara meddelanden kön-entiteten i en kö eller prenumeration, även om det finns aktiva lyssnare på obeställbara meddelanden kön entiteten för den aktuella kön eller prenumerationen. | 

### <a name="example-event"></a>Exempel händelse

#### <a name="active-messages-available-with-no-listeners"></a>Aktiva meddelanden som är tillgängliga utan lyssnare

I följande exempel visas schemat för aktiva meddelanden utan Listener-händelse:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Obeställbara meddelanden kön-meddelanden som är tillgängliga utan lyssnare

Schemat för en händelse av obeställbara meddelanden liknar följande:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Tillgängliga periodiska meddelanden för aktiva meddelanden

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Obeställbara meddelanden kön meddelanden tillgängliga periodiska meddelanden

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Händelse data för Blob Storage. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| namespaceName | sträng | Service Bus namn området som resursen finns i. |
| requestUri | sträng | URI: n till den angivna kön eller prenumerationen som avger händelsen. |
| entityType | sträng | Typ av Service Bus enhet som avger händelser (kö eller prenumeration). |
| queueName | sträng | Kön med aktiva meddelanden om prenumererar på en kö. Värdet null om du använder ämnen/prenumerationer. |
| topicName | sträng | Avsnittet Service Bus prenumerationen med aktiva meddelanden tillhör. Värdet null om du använder en kö. |
| subscriptionName | sträng | Service Bus prenumerationen med aktiva meddelanden. Värdet null om du använder en kö. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till att fungera som app-och Logic-appen. |
| [Azure Service Bus att Event Grid-integrering](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över att integrera Service Bus med Event Grid. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* Mer information om hur du använder Azure Event Grid med Service Bus finns i [Service Bus till Event Grid integrations översikt](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Försök att [ta emot Service Bus händelser med Functions eller Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
