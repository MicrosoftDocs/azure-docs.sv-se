---
title: Forward Edge-händelser till Event Grid Cloud-Azure Event Grid IoT Edge | Microsoft Docs
description: Forward Edge-händelser till Event Grid Cloud
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171609"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Självstudie: vidarebefordra händelser till Event Grid Cloud

Den här artikeln vägleder dig igenom alla steg som krävs för att vidarebefordra Edge-händelser till Event Grid i Azure-molnet. Du kanske vill göra det av följande orsaker:

* Reagera på gräns händelser i molnet.
* Vidarebefordra händelser till Event Grid i molnet och använda Azure Event Hubs eller Azure Storage köer för att buffra händelser innan de bearbetas i molnet.

 För att slutföra den här självstudien måste du ha en förståelse för Event Grid koncept på [Edge](concepts.md) och [Azure](../concepts.md). För ytterligare mål typer, se [händelse hanterare](event-handlers.md). 

## <a name="prerequisites"></a>Förutsättningar 
För att kunna slutföra den här självstudien behöver du:

* **Azure-prenumeration** – skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub-och IoT Edge-enhet** – Följ anvisningarna i snabb start för [Linux](../../iot-edge/quickstart-linux.md) -eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Skapa event Grid-ämne och-prenumeration i molnet

Skapa ett event Grid-ämne och en prenumeration i molnet genom att följa [den här självstudien](../custom-event-quickstart-portal.md). Anteckna `topicURL` , `sasKey` och `topicName` av det nyligen skapade avsnittet som du kommer att använda senare i självstudien.

Om du till exempel har skapat ett ämne `testegcloudtopic` som heter västra USA, skulle värdena se ut ungefär så här:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **SasKey**: tillgänglig under **Accesskey** i ditt ämne. Använd **KEY1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Avsnittet Skapa händelse rutnät i kanten

1. Skapa topic3.jsmed följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nytto lasten.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Kör följande kommando för att kontrol lera att avsnittet har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Skapa Event Grid prenumeration vid gränsen

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa subscription3.jsmed följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nytto lasten.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **EndpointUrl** anger att URL: en för Event Grid avsnittet i molnet. **SasKey** refererar till Event Grid moln ämnets nyckel. Värdet i **topicName** kommer att användas för att stämpla alla utgående händelser till Event Grid. Detta kan vara användbart när du publicerar i ett Event Grid domän ämne. Mer information om Event Grid domän finns i [händelse domäner](../event-domains.md)

    Exempel:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskod på 200 OK ska returneras.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Kör följande kommando för att kontrol lera att prenumerationen har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publicera en händelse i kanten

1. Skapa event3.jsmed följande innehåll. Se [API-dokumentationen](api.md) för mer information om nytto lasten.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Kör följande kommando:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Kontrol lera Edge-händelsen i molnet

Information om hur du visar händelser som levereras av moln ämnet finns i [självstudien](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla prenumerationer

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Ta bort avsnittet och prenumerationerna som skapats i molnet (Azure Event Grid) också.

## <a name="next-steps"></a>Nästa steg

I den här självstudien publicerade du en händelse på gränsen och vidarebefordrad till Event Grid i Azure-molnet. Nu när du känner till de grundläggande stegen för att vidarebefordra till Event Grid i molnet:

* Information om hur du felsöker problem med att använda Azure Event Grid på IoT Edge finns i [fel söknings guide](troubleshoot.md).
* Vidarebefordra händelser till IoTHub genom att följa den här [självstudien](forward-events-iothub.md)
* Vidarebefordra händelser till webhook i molnet genom att följa den här [självstudien](pub-sub-events-webhook-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)
