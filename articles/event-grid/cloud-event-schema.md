---
title: CloudEvents v 1.0-schema med Azure Event Grid
description: Beskriver hur du använder CloudEvents v 1.0-schemat för händelser i Azure Event Grid. Tjänsten stöder händelser i JSON-implementeringen av moln händelser.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324186"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>CloudEvents v 1.0-schema med Azure Event Grid

Förutom dess [standard händelse schema](event-schema.md)har Azure Event Grid inbyggt stöd för händelser i [JSON-implementeringen av CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [http-protokoll bindning](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelse data.

CloudEvents fören klar samverkan genom att tillhandahålla ett gemensamt händelse schema för publicering, och konsumera molnbaserade händelser. Det här schemat möjliggör enhetlig verktygs hantering, vanliga sätt att dirigera & hanterings händelser och Universal sätt att avserialisera det yttre händelse schemat. Med ett gemensamt schema kan du enklare integrera arbete på olika plattformar.

CloudEvents skapas av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via [Cloud Native Computing Foundation](https://www.cncf.io/). Den är för närvarande tillgänglig som version 1,0.

I den här artikeln beskrivs CloudEvents-schemat med Event Grid.

## <a name="sample-event-using-cloudevents-schema"></a>Exempel händelse med CloudEvents-schema

Här är ett exempel på en Azure Blob Storage-händelse i CloudEvents-format:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner i CloudEvents v 1.0 finns [här](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Rubrik värden för händelser som levereras i CloudEvents-schemat och Event Grid schemat är samma förutom för `content-type` . Detta huvud värde är för CloudEvents-schemat `"content-type":"application/cloudevents+json; charset=utf-8"` . För Event Grid schema är detta huvud värde `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>Event Grid för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schemat. Du kan använda CloudEvents för system händelser, t. ex. Blob Storage händelser och IoT Hub händelser och anpassade händelser. Den kan också omvandla dessa händelser till den igen.


| Schema för indatamängd       | Schema för utdata
|--------------------|---------------------
| CloudEvents-format | CloudEvents-format
| Event Grid format  | CloudEvents-format
| Event Grid format  | Event Grid format

För alla händelse scheman måste Event Grid verifiera vid publicering till ett event Grid-ämne och när en händelse prenumeration skapas. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

## <a name="next-steps"></a>Nästa steg
Se [hur du använder CloudEvents v 1.0-schemat med event Grid](cloudevents-schema.md).  
