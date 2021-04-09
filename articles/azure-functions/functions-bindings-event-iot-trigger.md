---
title: Azure IoT Hub-utlösare för Azure Functions
description: Lär dig att svara på händelser som skickas till en händelse ström i IoT Hub i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612294"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub-utlösare för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Functions-bindningar för IoT Hub. IoT Hub-stödet baseras på [Azure Event Hubs-bindningen](functions-bindings-event-hubs.md).

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-event-iot.md).

> [!IMPORTANT]
> I följande kod exempel används Event Hub API, men den aktuella syntaxen gäller för IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.jspå egenskaper

host.jsfilen innehåller inställningar som styr beteendet [ för](functions-host-json.md#eventhub) Event Hub-utlösaren. Mer information om tillgängliga inställningar finns i avsnittet [host.jsi inställningar](functions-bindings-event-iot.md#hostjson-settings) .

## <a name="next-steps"></a>Nästa steg

- [Skriv händelser till en händelse ström (utgående bindning)](./functions-bindings-event-iot-output.md)
