---
title: Azure App konfiguration som Event Grid källa
description: I den här artikeln beskrivs hur du använder Azure App konfiguration som en Event Grid händelse källa. Det innehåller schemat och länkar till självstudier och instruktions artiklar.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366780"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure App konfiguration som en Event Grid källa
Den här artikeln innehåller egenskaper och schema för Azure App konfigurations händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure App konfiguration som en händelse källa.

## <a name="available-event-types"></a>Tillgängliga händelse typer

Azure App konfiguration avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Utlöses när ett nyckel värde skapas eller ersätts. |
| Microsoft. AppConfiguration. KeyValueDeleted | Utlöses när ett nyckel värde tas bort. |

## <a name="example-event"></a>Exempel händelse

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)
I följande exempel visas schemat för en händelse med nyckel värdes ändringar: 

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

Schemat för en händelse som tar bort nyckel värde liknar: 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

I följande exempel visas schemat för en händelse med nyckel värdes ändringar: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

Schemat för en händelse som tar bort nyckel värde liknar: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Händelse egenskaper

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)
En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `eventType` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `eventTime` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Händelse data för app-konfiguration. |
| `dataVersion` | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| `metadataVersion` | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |


# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `source` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `type` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `time` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Händelse data för app-konfiguration. |
| `specversion` | sträng | CloudEvents schema Specifikations version. |

---

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `key` | sträng | Nyckeln till det nyckel värde som ändrades eller togs bort. |
| `label` | sträng | Etiketten, om det finns, för det nyckel värde som ändrades eller togs bort. |
| `etag` | sträng | För `KeyValueModified` etag för det nya nyckel värdet. För `KeyValueDeleted` etag för det nyckel värde som har tagits bort. |


## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner

|Rubrik | Beskrivning |
|---------|---------|
| [Reagera på Azure App konfigurations händelser med Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över att integrera Azure App-konfiguration med Event Grid. |
| [Använd Event Grid för meddelanden om data ändring](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Lär dig hur du använder Azure App konfigurations händelse prenumerationer för att skicka ändrings händelser för nyckel värden till en webb slut punkt. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* En introduktion till hur du arbetar med Azure App konfigurations händelser finns i [använda Event Grid för meddelanden om data ändring](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 