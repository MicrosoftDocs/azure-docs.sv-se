---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du använder CloudEvents-schemat för händelser i Azure Event Grid. Tjänsten har stöd för händelser i JSON-implementeringen av CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737910"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Använd CloudEvents v 1.0-schemat med Event Grid
Förutom dess [standard händelse schema](event-schema.md)har Azure Event Grid inbyggt stöd för händelser i [JSON-implementeringen av CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [http-protokoll bindning](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelse data.

CloudEvents fören klar samverkan genom att tillhandahålla ett gemensamt händelse schema för att publicera och konsumera molnbaserade händelser. Det här schemat möjliggör enhetlig verktygs hantering, vanliga sätt att dirigera och hantera händelser och Universal sätt att avserialisera det yttre händelse schemat. Med ett gemensamt schema kan du enklare integrera arbete på olika plattformar.

CloudEvents skapas av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via [Cloud Native Computing Foundation](https://www.cncf.io/). Den är för närvarande tillgänglig som version 1,0.

Den här artikeln beskriver hur du använder CloudEvents-schemat med Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent-schema

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

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner finns i [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Rubrik värden för händelser som levereras i CloudEvents-schemat och Event Grid schemat är samma förutom för `content-type` . För CloudEvents-schemat är detta huvud värde `"content-type":"application/cloudevents+json; charset=utf-8"` . För det Event Grid schemat är huvud värdet `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera Event Grid för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schemat. I följande tabell beskrivs möjliga omvandlingar:

 Event Grid resurs | Schema för indatamängd       | Leverans schema
|---------------------|-------------------|---------------------
| Avsnitt om systemet       | Event Grid-schema | Event Grid schema eller CloudEvents-schema
| Användar ämnen/domäner | Event Grid-schema | Event Grid schema eller CloudEvents-schema
| Användar ämnen/domäner | CloudEvents-schema | CloudEvents-schema
| Användar ämnen/domäner | Anpassat schema     | Anpassat schema, Event Grid schema eller CloudEvents-schema
| PartnerTopics       | CloudEvents-schema | CloudEvents-schema

För alla händelse scheman måste Event Grid verifieras när du publicerar till ett Event Grid ämne och när du skapar en händelse prenumeration.

Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

### <a name="input-schema"></a>Schema för indatamängd

Du ställer in schemat för ett anpassat ämne när du skapar det anpassade ämnet.

För Azure CLI använder du:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Schema för utdata

Du ställer in output-schemat när du skapar händelse prenumerationen.

För Azure CLI använder du:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schemat. Använd en HTTP-utlösare. Exempel på hur du implementerar en HTTP-utlösare som tar emot händelser i CloudEvents-schemat finns i [använda CloudEvents med Azure Functions](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Slut punkts validering med CloudEvents v 1.0

Om du redan är bekant med Event Grid kan du vara medveten om slut punkts valideringens hand skakning för att förhindra missbruk. CloudEvents v 1.0 implementerar egna [skydds](webhook-event-delivery.md) objekt för missbruk med hjälp av http-alternativ-metoden. Läs mer om det i [HTTP 1,1-webbhookar för Event Delivery-Version 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid CloudEvents v 1.0-skyddet mot missbruk i stället för mekanismen för Event Grid validerings händelser.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Använd med Azure Functions

[Bindningen för Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) stöder inte internt CloudEvents, så http-utlöst funktioner används för att läsa CloudEvents-meddelanden. När du använder en HTTP-utlösare för att läsa CloudEvents, måste du skriva kod för vad den Event Grid utlösaren gör automatiskt:

* Skickar ett verifierings svar till en [begäran om prenumerations verifiering](../event-grid/webhook-event-delivery.md)
* Anropar funktionen en gång per element i händelse mat ris som finns i begär ande texten

Information om vilken URL som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i [referens dokumentationen för http-utlösaren](../azure-functions/functions-bindings-http-webhook.md).

I följande exempel C#-kod för en HTTP-utlösare simuleras Event Grid utlösarens beteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Följande exempel på JavaScript-kod för en HTTP-utlösare simulerar Event Grid utlösnings beteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Vi rekommenderar att du testar, kommenterar och [bidrar till CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
