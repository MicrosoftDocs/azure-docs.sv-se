---
title: Förstå anpassade slut punkter för Azure IoT Hub | Microsoft Docs
description: Guide för utvecklare – använda vägvals frågor för att dirigera enhets-till-moln-meddelanden till anpassade slut punkter.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147648"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använda meddelande vägar och anpassade slut punkter för meddelanden från enhet till moln

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [meddelanderoutning](iot-hub-devguide-routing-query-syntax.md) gör det möjligt för användare att dirigera meddelanden från enheten till molnet till service-riktade slut punkter. Routning tillhandahåller också en fråge funktion för att filtrera data innan de dirigeras till slut punkterna. Varje cirkulations fråga som du konfigurerar har följande egenskaper:

| Egenskap      | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Det unika namn som identifierar frågan. |
| **Källa**    | Ursprung för den data ström som ska användas. Till exempel telemetri av enhet. |
| **Condition** | Frågeuttrycket för den cirkulations fråga som körs mot egenskaperna för meddelande programmet, system egenskaper, meddelande text, enhetens dubbla Taggar och enhetens dubbla egenskaper för att avgöra om det är en matchning för slut punkten. Mer information om hur du skapar en fråga finns i se [syntax för meddelanderoutning](iot-hub-devguide-routing-query-syntax.md) |
| **Slutpunkt**  | Namnet på slut punkten där IoT Hub skickar meddelanden som matchar frågan. Vi rekommenderar att du väljer en slut punkt i samma region som IoT-hubben. |

Ett enskilt meddelande kan matcha villkoret på flera Dirigerings frågor, i vilket fall IoT Hub levererar meddelandet till slut punkten som är kopplad till varje matchad fråga. IoT Hub deduplicerar också meddelande leveransen automatiskt, så om ett meddelande matchar flera frågor som har samma mål, skrivs det bara en gång till det målet.

## <a name="endpoints-and-routing"></a>Slut punkter och routning

En IoT Hub har en [inbyggd standard slut punkt](iot-hub-devguide-messages-read-builtin.md). Du kan skapa anpassade slut punkter för att dirigera meddelanden till genom att länka andra tjänster i din prenumeration till hubben. IoT Hub stöder för närvarande Azure Storage behållare, Event Hubs, Service Bus köer och Service Bus ämnen som anpassade slut punkter.

När du använder Routning och anpassade slut punkter skickas meddelanden endast till den inbyggda slut punkten om de inte matchar någon fråga. Om du vill leverera meddelanden till den inbyggda slut punkten och till en anpassad slut punkt lägger du till en väg som skickar meddelanden till den inbyggda slut punkten för **händelser** .

> [!NOTE]
> * IoT Hub stöder bara skrivning av data till Azure Storage behållare som blobbar.
> * Service Bus köer och ämnen med **sessioner** eller **dubblettidentifiering** aktiverade stöds inte som anpassade slut punkter.

Mer information om hur du skapar anpassade slut punkter i IoT Hub finns [IoT Hub slut punkter](iot-hub-devguide-endpoints.md).

Mer information om att läsa från anpassade slut punkter finns i:

* Läser från [Azure Storage behållare](../storage/blobs/storage-blobs-introduction.md).

* Läser från [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Läser från [Service Bus köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Läser från [Service Bus ämnen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub slut punkter finns i [IoT Hub slut punkter](iot-hub-devguide-endpoints.md).

* Mer information om frågespråket som du använder för att definiera routnings frågor finns i [query-syntax för meddelande cirkulation](iot-hub-devguide-routing-query-syntax.md).

* [Processen IoT Hub enhet-till-moln-meddelanden med hjälp av rutter](tutorial-routing.md) visar hur du använder routnings frågor och anpassade slut punkter.