---
title: Azure Event Grid händelse hanterare
description: Beskriver händelse hanterare som stöds för Azure Event Grid. Azure Automation, funktioner, Event Hubs, Hybridanslutningar, Logic Apps, Service Bus, Queue Storage, Webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86117023"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelse hanterare i Azure Event Grid
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en webhook för att hantera händelser. Webhooken behöver inte vara värd för Azure för att hantera händelser. Event Grid stöder endast HTTPS webhook-slutpunkter.

## <a name="supported-event-handlers"></a>Händelse hanterare som stöds
Följande är de händelse hanterare som stöds: 

- [Webhooks](handler-webhooks.md). Azure Automation runbooks och Logic Apps stöds via Webhooks. 
- [Azure Functions](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Relay-hybridanslutningar](handler-relay-hybrid-connections.md)
- [Service Bus köer och ämnen](handler-service-bus.md)
- [Lagringsköer](handler-storage-queues.md)

## <a name="next-steps"></a>Nästa steg
- En introduktion till Event Grid finns i [Om Event Grid](overview.md).
