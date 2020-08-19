---
title: Reagerar på Azure App konfiguration nyckel-värde-händelser
description: Använd Azure Event Grid för att prenumerera på konfigurations händelser för appar, vilket gör att program kan reagera på ändringar i nyckel värden utan att behöva komplicerad kod.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: ae3417f991c0d810d8946cdaf358218ebbe4f6a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590038"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagerar på Azure App konfigurations händelser

Azure App konfigurations händelser gör det möjligt för program att reagera på ändringar i nyckel värden. Detta görs utan behov av komplicerad kod eller dyra och ineffektiva avsöknings tjänster. I stället flyttas händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med din egen anpassade http-lyssnare. Du betalar mycket bara för det du använder.

Azure App konfigurations händelser skickas till Azure Event Grid som tillhandahåller pålitliga leverans tjänster till dina program genom omfattande principer för återförsök och leverans av obeställbara meddelanden. Läs mer i [Event Grid meddelande leverans och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Vanliga konfigurations scenarier för appar omfattar uppdatering av program konfiguration, aktivering av distributioner eller konfigurations-orienterade arbets flöden. När ändringar är ovanliga, men scenariot kräver omedelbar svars tid, kan den händelsebaserade arkitekturen vara särskilt effektiv.

Ta en titt på [Route Azure App konfigurations händelser till en anpassad webb slut punkt – CLI](./howto-app-configuration-event.md) för ett enkelt exempel. 

![Event Grid modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Tillgängliga Azure App konfigurations händelser
Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Azure App konfigurations händelse prenumerationer kan innehålla två typer av händelser:  

> |Händelsenamn|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Utlöses när ett nyckel värde skapas eller ersätts|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Utlöses när ett nyckel värde tas bort|

## <a name="event-schema"></a>Händelseschema
Azure App konfigurations händelser innehåller all information som du behöver för att svara på ändringar i dina data. Du kan identifiera en konfigurations händelse för appen eftersom egenskapen eventType börjar med "Microsoft. AppConfiguration". Ytterligare information om användningen av Event Grid händelse egenskaper dokumenteras i [Event Grid händelse schema](../event-grid/event-schema.md).  

> |Egenskap|Typ|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |ämne|sträng|Fullständigt Azure Resource Manager-ID för den app-konfiguration som utsänder händelsen.|
> |Ämne|sträng|URI för det nyckel värde som är ämnet för händelsen.|
> |Händelsetid|sträng|Datum/tid då händelsen genererades, i ISO 8601-format.|
> |Händelsetyp|sträng|"Microsoft. AppConfiguration. KeyValueModified" eller "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|sträng|En unik identifierare för den här händelsen.|
> |Dataversion|sträng|Dataobjektets schemaversion.|
> |Metadataversion|sträng|Schema versionen för toppnivå egenskaper.|
> |data|objekt|Samling med Azure App konfiguration av vissa händelse data|
> |data. Key|sträng|Nyckeln till det nyckel värde som ändrades eller togs bort.|
> |data. label|sträng|Etiketten, om det finns, för det nyckel värde som ändrades eller togs bort.|
> |data. etag|sträng|För `KeyValueModified` etag för det nya nyckel värdet. För `KeyValueDeleted` etag för det nyckel värde som har tagits bort.|

Här är ett exempel på en KeyValueModified-händelse:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Mer information finns i [schemat för Azure App konfigurations händelser](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser
Program som hanterar konfigurations händelser för appar bör följa dessa rekommendationer:
> [!div class="checklist"]
> * Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, så anta inte att händelser kommer från en viss källa. Kontrol lera i stället avsnittet i meddelandet för att se till att appens konfigurations instans skickar händelsen.
> * Kontrol lera eventType och anta inte att alla händelser som du tar emot är de typer som du förväntar dig.
> * Använd etag-fälten för att ta reda på om din information om objekt fortfarande är uppdaterad.  
> * Använd sekvenserande fält för att förstå ordningen på händelser för ett visst objekt.
> * Använd fältet ämne för att få åtkomst till det nyckel värde som ändrades.


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure App konfigurations händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)
- [Dirigera Azure App konfigurations händelser till en anpassad webb slut punkt](./howto-app-configuration-event.md)