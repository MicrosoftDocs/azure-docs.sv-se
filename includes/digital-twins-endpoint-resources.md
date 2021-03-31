---
author: baanders
description: inkludera fil för de resurser som behövs för att skapa Azure Digitals-slutpunkter
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054521"
---
### <a name="prerequisite-create-endpoint-resources"></a>Förutsättning: skapa slut punkts resurser

Om du vill länka en slut punkt till Azure Digitals-band måste du redan ha installerat Event Grid-ämnet, händelsehubben eller Service Bus ämnet som du använder för slut punkten.

Använd följande diagram för att se vilka resurser som ska ställas in innan du skapar slut punkten.

| Slut punkts typ | Resurser som krävs (länkade till skapande instruktioner) |
| --- | --- |
| Event Grid slut punkt | [Event Grid-ämne](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs slut punkt | [&nbsp; &nbsp; Namn område för Event Hub](../articles/event-hubs/event-hubs-create.md)<br/><br/>[händelsehubben](../articles/event-hubs/event-hubs-create.md)<br/><br/>Valfritt [auktoriseringsregel](../articles/event-hubs/authorize-access-shared-access-signature.md) för nyckelbaserad autentisering | 
| Service Bus slut punkt | [Service Bus namnrymd](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus ämne](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Valfritt [auktoriseringsregel](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) för nyckelbaserad autentisering|
