---
title: Azure Event Grid händelse schema
description: Beskriver de egenskaper och schema som finns för alla händelser. Händelser består av en uppsättning av fem obligatoriska sträng egenskaper och ett obligatoriskt data objekt.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 5ff7b41a45cf467074e18a50be398b2a3c9866a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732167"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid händelse schema

I den här artikeln beskrivs de egenskaper och schema som finns för alla händelser. Händelser består av en uppsättning av fem obligatoriska sträng egenskaper och ett obligatoriskt data objekt. Egenskaperna är gemensamma för alla händelser från vilken utgivare som helst. Dataobjektet har egenskaper som är speciella för varje utgivare. För system ämnen är dessa egenskaper specifika för resurs leverantören, till exempel Azure Storage eller Azure Event Hubs.

Händelse källor skickar händelser till Azure Event Grid i en matris som kan ha flera händelse objekt. När du publicerar händelser i ett event Grid-ämne kan matrisen ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsad till 1 MB. Om en händelse eller matrisen är större än storleks gränserna får du svar 413- **nytto lasten för stor**. Åtgärder debiteras i steg om 64 KB. Händelser över 64 KB kommer därför att debiteras som om de var flera händelser. En händelse som är 130 KB skulle till exempel kunna medföra åtgärder som om det var tre separata händelser.

Event Grid skickar händelserna till prenumeranter i en matris som har en enda händelse. Det här beteendet kan ändras i framtiden.

Du kan hitta JSON-schemat för Event Grid-händelsen och varje Azure-utgivares data nytto Last i [händelse schema arkivet](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Händelseschema

I följande exempel visas de egenskaper som används av alla händelse utgivare:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Till exempel är schemat som publicerats för en Azure Blob Storage-händelse:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Händelse egenskaper

Alla händelser har samma följande toppnivå data:

| Egenskap | Typ | Obligatorisk | Beskrivning |
| -------- | ---- | -------- | ----------- |
| ämne | sträng | Nej, men om det ingår måste det matcha Event Grid avsnittet Azure Resource Manager ID exakt. Om den inte ingår stämplas Event Grid in på händelsen. | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Ja | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | Ja | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Ja | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Ja | Unikt ID för händelsen. |
| data | objekt | Inga | Händelse data som är speciella för resurs leverantören. |
| Dataversion | sträng | Nej, men kommer att stämplas med ett tomt värde. | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Krävs inte, men om det ingår måste det matcha Event Grid schema `metadataVersion` exakt (endast för närvarande `1` ). Om den inte ingår stämplas Event Grid in på händelsen. | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Information om egenskaperna i data-objektet finns i händelse källan:

* [Azure Policy](./event-schema-policy.md)
* [Azure-prenumerationer (hanterings åtgärder)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Resurs grupper (hanterings åtgärder)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

För anpassade ämnen bestämmer händelse utgivaren dataobjektet. Data på den översta nivån ska ha samma fält som standard resurs definierade händelser.

När du publicerar händelser till anpassade ämnen skapar du ämnen för dina händelser som gör det enkelt för prenumeranter att veta om de är intresserade av evenemanget. Prenumeranter använder ämnet för att filtrera och dirigera händelser. Överväg att ange sökvägen till den plats där händelsen inträffade, så att prenumeranter kan filtrera efter segment i den sökvägen. Med hjälp av sökvägen kan prenumeranter begränsa eller filtrera händelser på ett stort sätt. Om du till exempel anger en tre segment Sök väg som `/A/B/C` i ämnet kan prenumeranter filtrera efter det första segmentet `/A` för att få en bred uppsättning händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E` . Andra prenumeranter kan filtrera efter `/A/B` för att få en smalare uppsättning händelser.

Ibland behöver ditt ämne mer information om vad som hände. **Lagrings kontoren** tillhandahåller till exempel ämnet `/blobServices/default/containers/<container-name>/blobs/<file>` när en fil läggs till i en behållare. En prenumerant kan filtrera efter sökvägen `/blobServices/default/containers/testcontainer` för att hämta alla händelser för behållaren, men inte andra behållare i lagrings kontot. En prenumerant kan också filtrera eller dirigera efter suffixet `.txt` så att det bara fungerar med textfiler.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
