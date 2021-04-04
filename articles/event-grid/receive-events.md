---
title: Ta emot händelser från Azure Event Grid till en HTTP-slutpunkt
description: Beskriver hur du verifierar en HTTP-slutpunkt och sedan tar emot och deserialiserar händelser från Azure Event Grid
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023741"
---
# <a name="receive-events-to-an-http-endpoint"></a>Ta emot händelser till en HTTP-slutpunkt

Den här artikeln beskriver hur du [verifierar en HTTP-slutpunkt](webhook-event-delivery.md) för att ta emot händelser från en händelse prenumeration och sedan ta emot och deserialisera händelser. I den här artikeln används en Azure-funktion i demonstrations syfte, men samma koncept gäller oavsett var programmet finns.

> [!NOTE]
> Vi rekommenderar **starkt** att du använder en [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md) när du utlöser en Azure-funktion med event Grid. Användning av en allmän webhook-utlösare här är demonstrerad.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Function-app med en HTTP-utlöst funktion.

## <a name="add-dependencies"></a>Lägg till beroenden

Om du utvecklar i .NET lägger du [till ett beroende](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) till din funktion för NuGet- `Microsoft.Azure.EventGrid` [paketet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). I exemplen i den här artikeln krävs version 1.4.0 eller senare.

SDK: er för andra språk är tillgängliga via referens för att [publicera SDK](./sdk-overview.md#data-plane-sdks) : er. De här paketen har modeller för interna händelse typer som `EventGridEvent` , `StorageBlobCreatedEventData` och `EventHubCaptureFileCreatedEventData` .

Klicka på länken "Visa filer" i din Azure-funktion (den högra rutan i Azure Functions-portalen) och skapa en fil med namnet project.jspå. Lägg till följande innehåll i `project.json` filen och spara den:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![NuGet-paketet har lagts till](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Slut punkts validering

Det första du vill göra är att hantera `Microsoft.EventGrid.SubscriptionValidationEvent` händelser. Varje gång någon prenumererar på en händelse skickar Event Grid en validerings händelse till slut punkten med en `validationCode` i data nytto lasten. Slut punkten krävs för att ekona tillbaka i svars texten för att [bevisa att slut punkten är giltig och ägs av dig](webhook-event-delivery.md). Om du använder en [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md) i stället för en webhook-utlöst funktion, hanteras slut punkts verifieringen åt dig. Om du använder en API-tjänst från tredje part (t. ex. [Zapier](https://zapier.com/home) eller [ifttt](https://ifttt.com/)) kanske du inte kan program mässigt ekoa verifierings koden. För dessa tjänster kan du verifiera prenumerationen manuellt genom att använda en verifierings-URL som skickas i prenumerations validerings händelsen. Kopiera URL: en i `validationUrl` egenskapen och skicka en get-begäran antingen via en rest-klient eller webbläsaren.

I C# `DeserializeEventGridEvents()` deserialiserar funktionen Event Grid händelser. Den deserialiserar händelse data till lämplig typ, till exempel StorageBlobCreatedEventData. Använd- `Microsoft.Azure.EventGrid.EventTypes` klassen för att hämta händelse typer och namn som stöds.

Använd följande kod för att program mässigt ekoa verifierings koden. Du kan hitta relaterade exempel i [Event Grid Consumer-exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Test verifierings svar

Testa funktionen verifierings svar genom att klistra in exempel händelsen i fältet test för funktionen:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

När du klickar på Kör ska utdata vara 200 OK och `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` i texten:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Verifieringsbegäran":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Verifiering av utdata":::

## <a name="handle-blob-storage-events"></a>Hantera Blob Storage-händelser

Nu ska vi utöka funktionen att hantera `Microsoft.Storage.BlobCreated` :

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Testa blob som skapade händelse hantering

Testa de nya funktionerna i funktionen genom att placera en [Blob Storage-händelse](./event-schema-blob-storage.md#example-event) i fältet test och köra:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Du bör se BLOB-URL-utdata i funktions loggen:

![Utgående logg](./media/receive-events/blob-event-response.png)

Du kan också testa genom att skapa ett Blob Storage-konto eller ett Generell användning v2 (GPv2) lagrings konto, [lägga till och händelse prenumeration](../storage/blobs/storage-blob-event-quickstart.md)och ange slut punkten till funktions webb adressen:

![Funktionswebbadress](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Hantera anpassade händelser

Slutligen kan du utöka funktionen en gång till så att den även kan hantera anpassade händelser. 

I C# stöder SDK mappning av ett händelse typs namn till händelse data typen. Använd `AddOrUpdateCustomEventMapping()` funktionen för att mappa den anpassade händelsen.

Lägg till en kontroll för din händelse `Contoso.Items.ItemReceived` . Den slutliga koden bör se ut så här:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testa anpassad händelse hantering

Testa slutligen att din funktion nu kan hantera din anpassade händelse typ:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Du kan också testa den här funktionen Live genom att [skicka en anpassad händelse med en sväng från portalen](./custom-event-quickstart-portal.md) eller genom [att publicera till ett anpassat ämne](./post-to-custom-topic.md)  med valfri tjänst eller program som kan publicera till en slut punkt, till exempel [Postman](https://www.getpostman.com/). Skapa ett anpassat ämne och en händelse prenumeration med slut punkten som angetts som funktions webb adress.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Nästa steg

* Utforska [Azure Event Grid hantering och publicera SDK](./sdk-overview.md) : er
* Lär dig hur du [publicerar till ett anpassat ämne](./post-to-custom-topic.md)
* Prova en av djupgående Event Grid-och Function-självstudier som att [ändra storlek på bilder som har överförts till Blob Storage](resize-images-on-storage-blob-upload-event.md)
