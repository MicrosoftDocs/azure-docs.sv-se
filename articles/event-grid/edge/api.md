---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: REST API på Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171762"
---
# <a name="rest-api"></a>REST-API
I den här artikeln beskrivs REST-API: er för Azure Event Grid på IoT Edge

## <a name="common-api-behavior"></a>Gemensamt API-beteende

### <a name="base-url"></a>Grundläggande URL
Event Grid på IoT Edge har följande API: er exponerade över HTTP (port 5888) och HTTPS (port 4438).

* Bas-URL för HTTP: http://eventgridmodule:5888
* Bas-URL för HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Frågesträng för begäran
Alla API-begäranden kräver följande frågesträngparametern:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Begär innehålls typ
Alla API-begäranden måste ha en **innehålls typ**.

I händelse av **EventGridSchema** eller **CustomSchema** kan värdet för Content-Type vara något av följande värden:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Om **CloudEventSchemaV1_0** i strukturerat läge kan värdet för Content-Type vara något av följande värden:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

I händelse av **CloudEventSchemaV1_0** i binärt läge, se [dokumentationen](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) för mer information.

### <a name="error-response"></a>Fel svar
Alla API: er returnerar ett fel med följande nytto last:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Hantera ämnen

### <a name="put-topic-create--update"></a>Lägg till ämne (Skapa/uppdatera)

**Begäran**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Nytto Last**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Svar**: http 200

**Nytto Last**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Hämta ämne

**Begäran**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Hämta alla ämnen

**Begäran**: ``` GET /topics?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Ta bort ämne

**Begäran**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar**: http 200, Tom nytto Last

## <a name="manage-event-subscriptions"></a>Hantera händelse prenumerationer
Exemplen i det här avsnittet använder `EndpointType=Webhook;` . JSON-exemplen för `EndpointType=EdgeHub / EndpointType=EventGrid` finns i nästa avsnitt. 

### <a name="put-event-subscription-create--update"></a>Lägg till händelse prenumeration (Skapa/uppdatera)

**Begäran**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Nytto Last**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Svar**: http 200

**Nytto Last**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Hämta händelse prenumeration

**Begäran**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Hämta händelse prenumerationer

**Begäran**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Ta bort händelse prenumeration

**Begäran**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar**: http 200, ingen nytto Last


## <a name="publish-events-api"></a>API för publicerings händelser

### <a name="send-batch-of-events-in-event-grid-schema"></a>Skicka batch med händelser (i Event Grid schema)

**Begäran**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Svar**: http 200, Tom nytto Last


**Fält beskrivningar för nytto Last**
- ```Id``` är obligatoriskt. Det kan vara valfritt sträng värde som anroparen har fyllt i. Event Grid utför ingen dubblettidentifiering eller använder inga semantik i det här fältet.
- ```Topic``` är valfritt, men om det anges måste den matcha topic_name från begärd URL
- ```Subject``` är obligatoriskt, kan vara valfritt sträng värde
- ```EventType``` är obligatoriskt, kan vara valfritt sträng värde
- ```EventTime``` är obligatoriskt, den är inte verifierad men bör vara en korrekt DateTime.
- ```DataVersion``` är obligatorisk
- ```MetadataVersion``` är valfritt, om det anges måste det vara en sträng med värdet ```"1"```
- ```Data``` är valfritt och kan vara valfri JSON-token (Number, String, Boolean, array, Object)

### <a name="send-batch-of-events-in-custom-schema"></a>Skicka batch med händelser (i anpassat schema)

**Begäran**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Svar**: http 200, Tom nytto Last


**Nytto Last begränsningar**
- MÅSTE vara en matris med händelser.
- Varje mat ris post måste vara ett JSON-objekt.
- Inga andra begränsningar (andra än nytto Last storleken).

## <a name="examples"></a>Exempel

### <a name="set-up-topic-with-eventgrid-schema"></a>Konfigurera ämne med EventGrid-schema
Ställer in ett ämne för att kräva att händelser publiceras i **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Konfigurera ämne med anpassat schema
Konfigurerar ett ämne för att kräva att händelser publiceras i `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Konfigurera ämne med moln händelse schema
Konfigurerar ett ämne för att kräva att händelser publiceras i `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Konfigurera webhook som mål, händelser som ska levereras i eventgridschema
Använd den här måltypen för att skicka händelser till andra moduler (som är värdar för en HTTP-slutpunkt) eller till valfri HTTP-adresserad slut punkt i nätverket/Internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Begränsningar för `endpointUrl` attributet:
- Det får inte vara null.
- Det måste vara en absolut URL.
- Om outbound__webhook__httpsOnly har angetts till sant i EventGridModule-inställningarna måste det endast vara HTTPS.
- Om outbound__webhook__httpsOnly har angetts till false, kan det vara HTTP eller HTTPS.

Begränsningar för `eventDeliverySchema` egenskapen:
- Den måste överensstämma med det underbeskrivande ämnets schema.
- Det kan vara null. Det används som standard för det här avsnittets schema.

### <a name="set-up-iot-edge-as-destination"></a>Konfigurera IoT Edge som mål

Använd det här målet för att skicka händelser till IoT Edge hubb och omfattas av del systemet routning/filtrering/vidarebefordran för Edge Hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Konfigurera Event Grid molnet som mål

Använd det här målet för att skicka händelser till Event Grid i molnet (Azure). Du måste först konfigurera ett användar ämne i molnet som händelser ska skickas till innan du skapar en händelse prenumeration på gränsen.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Det får inte vara null.
- Det måste vara en absolut URL.
- Sökvägen `/api/events` måste definieras i URL-sökvägen för begäran.
- Den måste ha `api-version=2018-01-01` i frågesträngen.
- Om outbound__eventgrid__httpsOnly är inställt på sant i EventGridModule-inställningarna (sant som standard), måste det endast vara HTTPS.
- Om outbound__eventgrid__httpsOnly är inställt på falskt, kan det vara HTTP eller HTTPS.
- Om outbound__eventgrid__allowInvalidHostnames är inställt på falskt (falskt som standard) måste den ha en av följande slut punkter:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Får inte vara null.

TopicName:
- Om Subscription. EventDeliverySchema är inställt på EventGridSchema placeras värdet från det här fältet i varje händelses ämnes fält innan det vidarebefordras till Event Grid i molnet.
- Om Subscription. EventDeliverySchema har angetts till CustomEventSchema ignoreras den här egenskapen och den anpassade händelse nytto lasten vidarebefordras exakt som den togs emot.

## <a name="set-up-event-hubs-as-a-destination"></a>Konfigurera Event Hubs som ett mål

Om du vill publicera till en Event Hub ställer `endpointType` du in på `eventHub` och anger:

* connectionString: anslutnings sträng för den specifika Händelsehubben som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Du kan skapa en entitets-Specific-anslutningssträng genom att navigera till den speciella händelsehubben som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets connecection sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Konfigurera Service Bus köer som ett mål

Om du vill publicera till en Service Bus kö anger `endpointType` du till `serviceBusQueue` och anger:

* connectionString: anslutnings sträng för den specifika Service Bus kön som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Generera en entitets-Specific-anslutningssträng genom att gå till den angivna Service Buss kön som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets Specific connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Konfigurera Service Bus ämnen som mål

Om du vill publicera till ett Service Bus ämne anger `endpointType` du till `serviceBusTopic` och anger:

* connectionString: anslutnings sträng för det specifika Service Bus ämne som du har genererat via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutnings strängen måste vara en speciell entitet. Det går inte att använda en anslutnings sträng för namn områden. Generera en entitets-speciell anslutnings sträng genom att gå till den aktuella Service Bus avsnittet som du vill publicera till i Azure-portalen och klicka på **principer för delad åtkomst** för att generera en ny entitets Specific connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Konfigurera lagrings köer som mål

För att publicera till en lagrings kö, ange  `endpointType` till `storageQueue` och ange:

* queueName: namnet på lagrings kön som du publicerar till.
* connectionString: anslutnings sträng för lagrings kontot som lagrings kön finns i.

    >[!NOTE]
    > Unline Event Hubs, Service Bus köer och Service Bus ämnen, den anslutnings sträng som används för lagrings köer är inte entitets-specifika. I stället måste den, men anslutnings strängen för lagrings kontot.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```