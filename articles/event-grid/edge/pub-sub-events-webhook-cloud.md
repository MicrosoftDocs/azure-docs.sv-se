---
title: Publicera, prenumerera på händelser i Cloud-Azure Event Grid IoT Edge | Microsoft Docs
description: Publicera, prenumerera på händelser i molnet med hjälp av webhook med Event Grid på IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790807"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Självstudie: publicera, prenumerera på händelser i molnet

Den här artikeln vägleder dig igenom alla steg som behövs för att publicera och prenumerera på händelser med Event Grid på IoT Edge. I den här självstudien används och Azure Function som händelse hanterare. För ytterligare mål typer, se [händelse hanterare](event-handlers.md).

Se [Event Grid-koncept](concepts.md) för att förstå vad ett ämne i Event Grid och prenumerationen är innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar 
För att kunna slutföra den här självstudien behöver du:

* **Azure-prenumeration** – skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub-och IoT Edge-enhet** – Följ anvisningarna i snabb start för [Linux](../../iot-edge/quickstart-linux.md) -eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Skapa en Azure-funktion i Azure Portal

Följ stegen som beskrivs i [självstudien](../../azure-functions/functions-get-started.md) för att skapa en Azure-funktion. 

Ersätt kodfragmentet med följande kod:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

I den nya funktionen väljer du **Hämta funktions webb adress** längst upp till höger. Välj standard (**funktions nyckel**) och välj sedan **Kopiera**. Du kommer att använda URL-värdet för funktionen senare i självstudien.

> [!NOTE]
> Läs [Azure Functions](../../azure-functions/functions-overview.md) -dokumentationen för fler exempel och självstudier om hur du reagerar på händelser med hjälp av händelse utlösare i EventGrid.

## <a name="create-a-topic"></a>Skapa ett ämne

Som utgivare av en händelse måste du skapa ett event Grid-ämne. Avsnittet refererar till en slut punkt där utgivare kan skicka händelser till.

1. Skapa topic2.jsmed följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nytto lasten.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Kör följande kommando för att kontrol lera att avsnittet har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration

Prenumeranter kan registrera sig för händelser som publiceras i ett ämne. Prenumeranterna måste skapa en Event Grid-prenumeration i ett ämne av intresse för att få en händelse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa subscription2.jsmed följande innehåll. Läs vår [API-dokumentation](api.md) om du vill ha mer information om nytto lasten.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **EndpointType** anger att prenumeranten är en webhook.  **EndpointUrl** anger URL: en där prenumeranten lyssnar efter händelser. URL: en motsvarar Azure Function-exemplet som du tidigare har konfigurerat.
2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att kontrol lera att prenumerationen har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event2.jsmed följande innehåll. Läs vår [API-dokumentation](api.md) om du vill ha mer information om nytto lasten.

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Kör följande kommando för att publicera händelsen

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelse leverans

Du kan visa händelsen som levererats i Azure Portal under alternativet **övervakare** för din funktion.

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla prenumerationer

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Ta bort Azure-funktionen som skapats i Azure Portal.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett event Grid-ämne, prenumeration och publicerade händelser. Nu när du känner till de grundläggande stegen kan du läsa följande artiklar:

* Information om hur du felsöker problem med att använda Azure Event Grid på IoT Edge finns i [fel söknings guide](troubleshoot.md).
* Skapa/uppdatera prenumeration med [filter](advanced-filtering.md).
* Konfigurera persistence för Event Grid modul i [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
* Följ [dokumentationen](configure-client-auth.md) om du vill konfigurera klientautentisering
* Vidarebefordra händelser till Azure Event Grid i molnet genom att följa den här [självstudien](forward-events-event-grid-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)