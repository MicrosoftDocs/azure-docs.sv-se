---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du använder CloudEvents-schemat för händelser i Azure Event Grid. Tjänsten stöder händelser i JSON-implementeringen av CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0ee816663a385601d4a31edbf87f8c787ea5aa91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389510"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Använda CloudEvents v1.0-schema med Event Grid
Förutom [standardhändelseschemat](event-schema.md)stöder Azure Event Grid händelser i JSON-implementeringen av [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [HTTP-protokollbindningen](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelsedata.

CloudEvents förenklar samverkan genom att tillhandahålla ett gemensamt händelseschema för publicering och användning av molnbaserade händelser. Det här schemat möjliggör enhetliga verktyg, standardsätt för routning och hantering av händelser och universella sätt att deserialisera det yttre händelseschemat. Med ett gemensamt schema kan du enklare integrera arbete mellan plattformar.

CloudEvents byggs av flera [medarbetare,](https://github.com/cloudevents/spec/blob/master/community/contributors.md)inklusive Microsoft, via [Cloud Native Computing Foundation.](https://www.cncf.io/) Den är för närvarande tillgänglig som version 1.0.

Den här artikeln beskriver hur du använder CloudEvents-schemat med Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent-schema

Här är ett exempel på en Azure Blob Storage händelse i CloudEvents-format:

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

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner finns i [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Huvudvärdena för händelser som levereras i CloudEvents-schemat och Event Grid schema är samma förutom `content-type` för . För CloudEvents-schemat är huvudvärdet `"content-type":"application/cloudevents+json; charset=utf-8"` . För Event Grid schema är huvudvärdet `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera Event Grid för CloudEvents

Du kan använda Event Grid både för indata och utdata från händelser i CloudEvents-schemat. I följande tabell beskrivs möjliga transformningar:

 Event Grid resurs | Indataschema       | Leveransschema
|---------------------|-------------------|---------------------
| Systemämnen       | Event Grid-schema | Event Grid schema eller CloudEvents-schema
| Anpassade ämnen/domäner | Event Grid-schema | Event Grid schema eller CloudEvents-schema
| Anpassade ämnen/domäner | CloudEvents-schema | CloudEvents-schema
| Anpassade ämnen/domäner | Anpassat schema     | Anpassat schema, Event Grid schema eller CloudEvents-schema
| Partnerämnen       | CloudEvents-schema | CloudEvents-schema

För alla händelsescheman kräver Event Grid validering när du publicerar till ett Event Grid-ämne och när du skapar en händelseprenumeration.

Mer information finns i [Event Grid säkerhet och autentisering.](security-authentication.md)

### <a name="input-schema"></a>Indataschema

Du anger indataschemat för ett anpassat ämne när du skapar det anpassade ämnet.

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

### <a name="output-schema"></a>Utdataschema

Du anger utdataschemat när du skapar händelseprenumerationen.

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

 För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions när händelsen levereras i CloudEvents-schemat. Använd en HTTP-utlösare. Exempel på hur du implementerar en HTTP-utlösare som tar emot händelser i CloudEvents-schemat finns i [Använda CloudEvents med Azure Functions](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Slutpunktsverifiering med CloudEvents v1.0

Om du redan är bekant med Event Grid kanske du känner till handskakningen för slutpunktsverifiering för att förhindra missbruk. CloudEvents v1.0 implementerar sin egen semantik [för missbruksskydd](webhook-event-delivery.md) med hjälp av HTTP OPTIONS-metoden. Mer information finns i [HTTP 1.1 Web Hooks för händelseleverans – version 1.0.](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) När du använder CloudEvents-schemat för utdata använder Event Grid CloudEvents v1.0-skydd mot missbruk i stället för Event Grid för valideringshändelsen.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Använd med Azure Functions

Den [Azure Functions Event Grid bindningen](../azure-functions/functions-bindings-event-grid.md) har inte inbyggt stöd för CloudEvents, så HTTP-utlösta funktioner används för att läsa CloudEvents-meddelanden. När du använder en HTTP-utlösare för att läsa CloudEvents måste du skriva kod för vad Event Grid utlöser automatiskt:

* Skickar ett valideringssvar till en begäran [om prenumerationsverifiering](../event-grid/webhook-event-delivery.md)
* Anropar funktionen en gång per element i händelsematrisen som finns i begärandetexten

Information om url:en som ska användas för att aktivera funktionen lokalt eller när den körs i Azure finns i referensdokumentationen [för HTTP-utlösarbindning.](../azure-functions/functions-bindings-http-webhook.md)

Följande C#-exempelkod för en HTTP-utlösare simulerar Event Grid utlösarbeteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

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

Följande JavaScript-exempelkod för en HTTP-utlösare simulerar Event Grid utlösarbeteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

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

* Information om hur du övervakar händelseleveranser finns i [Övervaka Event Grid leverans av meddelanden.](monitor-event-delivery.md)
* Vi rekommenderar att du testar, kommenterar och [bidrar till CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns [i Event Grid prenumerationsschema](subscription-creation-schema.md).
