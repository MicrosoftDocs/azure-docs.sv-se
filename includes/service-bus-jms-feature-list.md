---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652618"
---
I följande tabell visas de JMS-funktioner (Java Message Service) som Azure Service Bus för närvarande stöder. Den visar även funktioner som inte stöds.


| Funktion | API |Status |
|---|---|---|
| Köer   | <ul> <li> JMSContext. createQueue (String queueName) </li> </ul>| **Stöds** |
| Ämnen   | <ul> <li> JMSContext. createTopic (String topicName) </li> </ul>| **Stöds** |
| Tillfälliga köer |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Stöds** |
| Tillfälliga ämnen |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Stöds** |
| Meddelande tillverkare/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Stöds** |
| Köa webbläsare |<ul> <li> JMSContext. createBrowser (Queue queue) </li> <li> JMSContext. createBrowser (Queue Queue, String messageSelector) </li> </ul> | **Stöds** |
| Meddelande förbrukare/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (mål destination) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector, boolesk nolocal)</li> </ul>  <br/> Det finns för närvarande inte stöd för den lokala datorn | **Stöds** |
| Delade varaktiga prenumerationer | <ul> <li> JMSContext. createSharedDurableConsumer (ämne, sträng namn) </li> <li> JMSContext. createSharedDurableConsumer (ämne, sträng namn, String messageSelector) </li> </ul>| **Stöds**|
| Delade varaktiga prenumerationer | <ul> <li> JMSContext. createDurableConsumer (ämne, sträng namn) </li> <li> createDurableConsumer (ämne, sträng namn, sträng messageSelector, boolesk nolocal) </li> </ul> <br/> nolocal stöds inte för närvarande och ska vara inställt på falskt | **Stöds** |
| Delade icke-varaktiga prenumerationer |<ul> <li> JMSContext. createSharedConsumer (ämne, String sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (ämne, sträng sharedSubscriptionName, sträng messageSelector) </li> </ul> | **Stöds** |
| Ej delade icke-varaktiga prenumerationer |<ul> <li> JMSContext. createConsumer (mål destination) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector, boolesk nolocal) </li> </ul> <br/> nolocal stöds inte för närvarande och ska vara inställt på falskt | **Stöds** |
| Meddelande väljare | beror på att klienten har skapats | **Stöds** |
| Leverans fördröjning (schemalagda meddelanden) | <ul> <li> JMSProducer. setDeliveryDelay (lång deliveryDelay) </li> </ul>|**Stöds**|
| Ett meddelande har skapats |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (serialiserbar objekt) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (sträng text) </li> </ul>| **Stöds** |
| Transaktioner mellan enheter |<ul> <li> Connection. createSession (True, Session.SESSION_TRANSACTED) </li> </ul> | **Stöds** |
| Distribuerade transaktioner || Stöds inte |
