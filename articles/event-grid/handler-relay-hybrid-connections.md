---
title: Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Relay hybrid anslutningar som händelse hanterare för Azure Event Grid händelser.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270210"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Relay** är en av dem. 

Använd Azure **Relay-hybridanslutningar** för att skicka händelser till program som finns i ett företags nätverk och inte har en offentligt tillgänglig slut punkt.

## <a name="tutorials"></a>Självstudier
I följande självstudie finns ett exempel på hur du använder en Azure Relay hybrid anslutning som händelse hanterare. 

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: skicka händelser till hybrid anslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybrid anslutning för bearbetning av ett lyssnar program. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 