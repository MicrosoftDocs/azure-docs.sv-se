---
title: Vidarebefordra Event Grid händelser till IoTHub-Azure Event Grid IoT Edge | Microsoft Docs
description: Vidarebefordra Event Grid händelser till IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171558"
---
# <a name="tutorial-forward-events-to-iothub"></a>Självstudie: vidarebefordra händelser till IoTHub

Den här artikeln vägleder dig igenom alla steg som behövs för att vidarebefordra Event Grid händelser till andra IoT Edge-moduler, IoTHub med hjälp av vägar. Du kanske vill göra det av följande orsaker:

* Fortsätt att använda befintliga investeringar som redan finns på plats med edgeHub-routning
* Föredra att bara dirigera alla händelser från en enhet via IoT Hub

För att slutföra den här självstudien måste du förstå följande begrepp:

- [Event Grid begrepp](concepts.md)
- [IoT Edge hubb](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Förutsättningar 
För att kunna slutföra den här självstudien behöver du:

* **Azure-prenumeration** – skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub-och IoT Edge-enhet** – Följ anvisningarna i snabb start för [Linux](../../iot-edge/quickstart-linux.md) -eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Skapa ämne

Som utgivare av en händelse måste du skapa ett event Grid-ämne. Avsnittet refererar till en slut punkt där utgivare sedan kan skicka händelser till.

1. Skapa topic4.jsmed följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nytto lasten.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Kör följande kommando för att kontrol lera att avsnittet har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Skapa händelse prenumeration

Prenumeranter kan registrera sig för händelser som publiceras i ett ämne. För att ta emot händelser måste de skapa en Event Grid-prenumeration i ett ämne av intresse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa subscription4.jsmed följande innehåll. Läs vår [API-dokumentation](api.md) om du vill ha mer information om nytto lasten.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType`Anger att prenumeranten är `edgeHub` . `outputName`Anger utdata som Event Grid-modulen ska dirigera händelser som matchar den här prenumerationen till edgeHub. Till exempel skrivs händelser som matchar ovanstående prenumeration till `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att kontrol lera att prenumerationen har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Konfigurera en Edge Hub-väg

Uppdatera Edge Hub-vägen för att vidarebefordra händelse prenumerationens händelser så att de vidarebefordras till IoTHub enligt följande:

1. Logga in på [Azure-portalen](https://ms.portal.azure.com)
1. Navigera till **IoT Hub**.
1. Välj **IoT Edge** på menyn
1. Välj ID för mål enheten i listan med enheter.
1. Välj **Ange moduler**.
1. Välj **Nästa** och i avsnittet vägar.
1. I vägarna lägger du till en ny väg

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Exempel:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Ovanstående väg vidarebefordrar alla händelser som matchar den här prenumerationen så att de vidarebefordras till IoT-hubben. Du kan använda funktionerna för [Edge Hub-routning](../../iot-edge/module-composition.md) för att ytterligare filtrera och dirigera Event Grid händelser till andra IoT Edge moduler.

## <a name="setup-iot-hub-route"></a>Konfigurera IoT Hub väg

I [självstudien om IoT Hub routning](../../iot-hub/tutorial-routing.md) kan du konfigurera en väg från IoT-hubben så att du kan visa händelser som vidarebefordras från modulen Event Grid. Använd `true` för frågan för att hålla självstudien enkel.  



## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event4.jsmed följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nytto lasten.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Kör följande kommando för att publicera händelsen:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelse leverans

I [självstudien](../../iot-hub/tutorial-routing.md) om IoT Hub routning finns anvisningar för hur du visar händelserna.

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla prenumerationer på gränsen:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Ta bort alla resurser som har skapats när du konfigurerar IoTHub-routning även i molnet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett event Grid-ämne, en Edge Hub-prenumeration och publicerade händelser. Nu när du vet vilka grundläggande steg som ska vidarebefordras till en Edge Hub kan du läsa följande artiklar:

* Information om hur du felsöker problem med att använda Azure Event Grid på IoT Edge finns i [fel söknings guide](troubleshoot.md).
* Använd [kant Hubbs](../../iot-edge/module-composition.md) väg filter för att partitionera händelser
* Konfigurera persistence för Event Grid modul i [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
* Följ [dokumentationen](configure-client-auth.md) om du vill konfigurera klientautentisering
* Vidarebefordra händelser till Azure Event Grid i molnet genom att följa den här [självstudien](forward-events-event-grid-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)