---
title: Förstå Azure IoT Hub anpassade slutpunkter | Microsoft Docs
description: Utvecklarguide – använda routningsfrågor för att dirigera enhet-till-moln-meddelanden till anpassade slutpunkter.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 4ad57473e0950f031fbeadee2302f85557ed526f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388269"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använda meddelandevägar och anpassade slutpunkter för meddelanden från enheten till molnet

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[IoT Hub-meddelanderoutning](iot-hub-devguide-routing-query-syntax.md) gör det möjligt för användare att dirigera enhet-till-moln-meddelanden till tjänstriktade slutpunkter. Routning ger också en frågefunktion för att filtrera data innan de dirigeras till slutpunkterna. Varje routningsfråga som du konfigurerar har följande egenskaper:

| Egenskap      | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Det unika namn som identifierar frågan. |
| **Källa**    | Ursprunget för dataströmmen som åtgärder ska åtgärdas för. Till exempel enhettelemetri. |
| **Condition** | Frågeuttrycket för routningsfrågan som körs mot meddelandeprogrammets egenskaper, systemegenskaper, meddelandetext, taggar för enhetstvillingar och egenskaper för enhetstvilling för att avgöra om det är en matchning för slutpunkten. Mer information om hur du skapar en fråga finns i syntaxen för [meddelanderoutningsfråga](iot-hub-devguide-routing-query-syntax.md) |
| **Slutpunkt**  | Namnet på den slutpunkt där IoT Hub skickar meddelanden som matchar frågan. Vi rekommenderar att du väljer en slutpunkt i samma region som din IoT-hubb. |

Ett enskilt meddelande kan matcha villkoret för flera routningsfrågor, vilket innebär IoT Hub levererar meddelandet till slutpunkten som är associerad med varje matchande fråga. IoT Hub också automatiskt deduplicerar meddelandeleveransen, så om ett meddelande matchar flera frågor som har samma mål skrivs det bara en gång till det målet.

## <a name="endpoints-and-routing"></a>Slutpunkter och routning

En IoT-hubb har en [inbyggd standardslutpunkt.](iot-hub-devguide-messages-read-builtin.md) Du kan skapa anpassade slutpunkter för att dirigera meddelanden till genom att länka andra tjänster i de prenumerationer som du äger till hubben. IoT Hub stöder för närvarande Azure Storage-containrar, Event Hubs, Service Bus-köer och Service Bus-ämnen som anpassade slutpunkter.

När du använder routning och anpassade slutpunkter levereras meddelanden endast till den inbyggda slutpunkten om de inte matchar någon fråga. Om du vill leverera meddelanden till den inbyggda slutpunkten och till en anpassad slutpunkt lägger du till en väg som skickar meddelanden till den inbyggda **slutpunkten för** händelser.

> [!NOTE]
> * IoT Hub stöder endast skrivning av data till Azure Storage containrar som blobar.
> * Service Bus köer och ämnen med **Sessioner eller** **Dubblettidentifiering aktiverat** stöds inte som anpassade slutpunkter.
> * I Azure Portal kan du bara skapa anpassade slutpunkter för routning till Azure-resurser som finns i samma prenumeration som din hubb. Du kan skapa anpassade slutpunkter för resurser i andra prenumerationer som du äger, men anpassade slutpunkter måste konfigureras med en annan metod än Azure Portal.

Mer information om hur du skapar anpassade slutpunkter i IoT Hub finns [i IoT Hub slutpunkter](iot-hub-devguide-endpoints.md).

Mer information om att läsa från anpassade slutpunkter finns i:

* Läsa från [Azure Storage containrar](../storage/blobs/storage-blobs-introduction.md).

* Läsa från [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Läsning från [Service Bus köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Läsa från [Service Bus avsnitt](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub finns i IoT Hub [slutpunkter](iot-hub-devguide-endpoints.md).

* Mer information om det frågespråk som du använder för att definiera routningsfrågor finns i [Frågesyntax för meddelanderoutning.](iot-hub-devguide-routing-query-syntax.md)

* [Självstudien IoT Hub meddelanden från](tutorial-routing.md) enhet till moln med vägar visar hur du använder routningsfrågor och anpassade slutpunkter.
