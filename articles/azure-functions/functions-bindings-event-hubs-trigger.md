---
title: Azure Event Hubs-utlösare för Azure Functions
description: Lär dig att använda Azure Event Hubs-utlösare i Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608926"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Event Hubs-utlösare för Azure Functions

Den här artikeln förklarar hur du arbetar med [Azure Event Hubs](../event-hubs/event-hubs-about.md) -utlösare för Azure Functions. Azure Functions stöder utlösare och [utgående bindningar](functions-bindings-event-hubs-output.md) för Event Hubs.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.jspå Inställningar

host.jsfilen innehåller inställningar som styr beteendet [ för](functions-host-json.md#eventhub) Event Hub-utlösaren. Mer information om tillgängliga inställningar finns i avsnittet [host.jsi inställningar](functions-bindings-event-hubs.md#hostjson-settings) .

## <a name="next-steps"></a>Nästa steg

- [Skriv händelser till en händelse ström (utgående bindning)](./functions-bindings-event-hubs-output.md)
