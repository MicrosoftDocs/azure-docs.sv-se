---
title: Reagera på Azure Maps händelser med Event Grid
description: Ta reda på hur du reagerar på Azure Maps händelser som rör avgränsningar. Se hur du lyssnar på mappa händelser och hur du använder Event Grid för att dirigera om händelser till händelse hanterare.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92889774"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagera på Azure Maps händelser med Event Grid

Azure Maps integreras med Azure Event Grid, så att användarna kan skicka händelse meddelanden till andra tjänster och utlösa efterföljande processer. Syftet med den här artikeln är att hjälpa dig att konfigurera dina affärs program så att de lyssnar på Azure Maps händelser. Detta gör att användarna kan reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt. Användare kan till exempel skapa ett program för att uppdatera en databas, skapa en biljett och leverera ett e-postmeddelande varje gång en enhet går in på en inhägnad.

Azure Event Grid är en helt hanterad tjänst för händelse dirigering som använder en publicerings prenumerations modell. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och [Azure Logic Apps](../azure-functions/functions-overview.md). Den kan leverera händelse aviseringar till icke-Azure-tjänster som använder Webhooks. En fullständig lista över de händelse hanterare som Event Grid stöder finns i [en introduktion till Azure Event Grid](../event-grid/overview.md).


![Azure Event Grid funktionell modell](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typer av Azure Maps händelser

Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Ett Azure Maps konto avger följande händelse typer: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Utlöses när mottagna koordinater har flyttats från utsidan av ett angivet geografiskt avgränsnings tecken till inom |
| Microsoft. Maps. GeofenceExited | Utlöses när mottagna koordinater har flyttats inifrån ett angivet geografiskt avgränsnings tecken utanför |
| Microsoft. Maps. GeofenceResult | Genereras varje gång en inhägnad fråga returnerar ett resultat, oavsett tillstånd |

## <a name="event-schema"></a>Händelseschema

I följande exempel visas schemat för GeofenceResult:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Tips om att använda händelser

Program som hanterar Azure Mapss gräns händelser bör följa några rekommenderade metoder:

* Konfigurera flera prenumerationer för att dirigera händelser till samma händelse hanterare. Det är viktigt att inte anta att händelser kommer från en viss källa. Kontrol lera alltid meddelande ämnet för att se till att meddelandet kommer från den källa som du förväntar dig.
* Använd `X-Correlation-id` fältet i svars huvudet för att förstå om informationen om objekt är aktuell. Meddelanden kan tas emot i annan ordning eller med fördröjning.
* När en GET-eller a POST-begäran i det geometriska API: t anropas med läges parametern inställt på `EnterAndExit` , genereras en retur-eller Exit-händelse för varje geometri i det avgränsnings värde som statusen har ändrats från det föregående API-anropet för polystängsel.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder polystaket för att styra åtgärder på en Bygg plats finns i:

> [!div class="nextstepaction"] 
> [Konfigurera en geofence med hjälp av Azure Maps](tutorial-geofence.md)