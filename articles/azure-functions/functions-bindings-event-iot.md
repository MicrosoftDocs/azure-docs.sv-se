---
title: Azure IoT Hub-bindningar för Azure Functions
description: Lär dig att använda IoT Hub utlösare och bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77586145"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub-bindningar för Azure Functions

I den här uppsättningen artiklar förklaras hur du arbetar med Azure Functions-bindningar för IoT Hub. IoT Hub-stödet baseras på [Azure Event Hubs-bindningen](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> I följande kod exempel används Event Hub API, men den aktuella syntaxen gäller för IoT Hub functions.

| Action | Typ |
|--------|------|
| Svara på händelser som skickats till en händelse ström i IoT Hub. | [Utlösare](./functions-bindings-event-iot-trigger.md) |
| Skriv händelser till en IoT-händelselogg | [Utgående bindning](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Nästa steg

- [Svara på händelser som skickats till händelse strömmen i Event Hub (utlösare)](./functions-bindings-event-iot-trigger.md)
- [Skriv händelser till en händelse ström (utgående bindning)](./functions-bindings-event-iot-output.md)
