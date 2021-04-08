---
title: Reagera på händelser i Azure SignalR service
description: Använd Azure Event Grid för att prenumerera på händelser i Azure SignalR service. Andra underordnade tjänster kan utlösas av dessa händelser.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151111"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagera på händelser i Azure SignalR Service

Azure SignalR service Events gör att program kan reagera på klient anslutningar anslutna eller frånkopplade med moderna serverbaserade arkitekturer. Det gör det utan att det behövs komplicerade kod eller dyra och ineffektiva avsöknings tjänster.  I stället flyttas händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med din egen anpassade http-lyssnare. Med Azure SignalR betalar du bara för det du använder.

Azure SignalR service-händelser skickas på ett tillförlitligt sätt till tjänsten Event Grid som tillhandahåller pålitliga leverans tjänster till dina program genom omfattande principer för återförsök och leverans av obeställbara meddelanden. Läs mer i [Event Grid meddelande leverans och försök igen](../event-grid/delivery-and-retry.md).

![Event Grid modell](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Tillstånd utan Server
Azure SignalR service-händelser är bara aktiva när klient anslutningar är i ett Server löst tillstånd. Om en klient inte dirigerar till en NAV Server går den utan server tillstånd. Klassiskt läge fungerar bara när hubben som klient anslutningar ansluter till inte har en NAV-Server. Server lös läge rekommenderas som bästa praxis. Mer information om tjänst läge finns i [så här väljer du tjänst läge](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Tillgängliga Azure SignalR service-händelser
Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Händelse prenumerationer för Azure SignalR-tjänsten har stöd för två typer av händelser:  

|Händelsenamn|Beskrivning|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Utlöses när en klient anslutning är ansluten.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Utlöses när en klient anslutning kopplas från.|

## <a name="event-schema"></a>Händelseschema
Azure SignalR service-händelser innehåller all information som du behöver för att svara på ändringar i dina data. Du kan identifiera en Azure SignalR service-händelse med egenskapen eventType börjar med "Microsoft. SignalRService". Ytterligare information om användningen av Event Grid händelse egenskaper dokumenteras i [Event Grid händelse schema](../event-grid/event-schema.md).  

Här är ett exempel på en ansluten händelse för klient anslutning:
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

Mer information finns i [schema för signalerar tjänst händelser](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure SignalR service-händelser ett försök:

> [!div class="nextstepaction"]
> [Prova ett exempel på Event Grid-integrering med Azure SignalR-tjänsten](./signalr-howto-event-grid-integration.md) 
>  [Om Event Grid](../event-grid/overview.md)