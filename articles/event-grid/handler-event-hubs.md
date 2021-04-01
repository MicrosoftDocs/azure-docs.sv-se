---
title: Event Hub som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Event Hub som händelse hanterare för Azure Event Grid händelser.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005645"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event Hub som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar en åtgärd för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Event Hubs** är en av dem. 

Använd **Event Hubs** när din lösning hämtar händelser från Event Grid snabbare än den kan bearbeta händelserna. När händelserna är i en händelsehubben kan ditt program bearbeta händelser från händelsehubben enligt ett eget schema. Du kan skala händelse bearbetningen för att hantera inkommande händelser.

## <a name="tutorials"></a>Självstudier
Se följande exempel: 

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera anpassade händelser till Azure Event Hubs med Azure CLI](custom-event-to-eventhub.md) | Skickar en anpassad händelse till en händelsehubben för bearbetning av ett program. |
| [Resource Manager-mall: skapa ett Event Grid anpassat ämne och skicka händelser till en Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure-Event Hubs. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST-exempel (för placering)


### <a name="event-hub"></a>Händelsehubb

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event Hub – leverans med hanterad identitet

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
