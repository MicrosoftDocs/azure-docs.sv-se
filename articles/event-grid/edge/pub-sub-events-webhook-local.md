---
title: Publicera, prenumerera på händelser lokalt – Azure Event Grid IoT Edge | Microsoft Docs
description: Publicera, prenumerera på händelser lokalt med webhook med Event Grid på IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171456"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Självstudie: publicera, prenumerera på händelser lokalt

Den här artikeln vägleder dig igenom alla steg som behövs för att publicera och prenumerera på händelser med hjälp av Event Grid på IoT Edge.

> [!NOTE]
> Mer information om Azure Event Grid ämnen och prenumerationer finns i [Event Grid begrepp](concepts.md).

## <a name="prerequisites"></a>Förutsättningar 
För att kunna slutföra den här självstudien behöver du:

* **Azure-prenumeration** – skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub-och IoT Edge-enhet** – Följ stegen i snabb starten för [Linux](../../iot-edge/quickstart-linux.md) -eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera Event Grid IoT Edge modul

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure Portal.

>[!NOTE]
> I den här självstudien ska du distribuera Event Grid-modulen utan persistence. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistence finns i följande artiklar: [sparat tillstånd i Linux](persist-state-linux.md) eller [sparat tillstånd i Windows](persist-state-windows.md). För produktions arbets belastningar rekommenderar vi att du installerar Event Grid-modulen med persistence.


### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhets hantering** . 
1. Klicka på mål enhetens ID i listan över enheter
1. Välj **Ange moduler**. Låt sidan vara öppen. Du kommer att fortsätta med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Azure Portal har en guide som vägleder dig genom att skapa ett distributions manifest i stället för att skapa JSON-dokumentet manuellt.  Det finns tre steg: **Lägg till moduler**, **Ange vägar** och **Granska distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till**
1. Från typer av moduler i list rutan väljer du **IoT Edge modul**
1. Ange namn, avbildning, behållarens skapande alternativ för behållaren:

   * **Namn**: eventgridmodule
   * **Bild-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Alternativ för att skapa behållare**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klicka på **Spara**
 1. Fortsätt till nästa avsnitt för att lägga till modulen Azure Event Grid-prenumerant innan du distribuerar dem tillsammans.

    >[!IMPORTANT]
    > I den här självstudien ska du distribuera Event Grid-modulen med klientautentisering inaktive rad. För produktions arbets belastningar rekommenderar vi att du aktiverar klientautentisering. Mer information om hur du konfigurerar Event Grid modul på ett säkert sätt finns i [säkerhet och autentisering](security-authentication.md).
    > 
    > Om du använder en virtuell Azure-dator som gräns enhet lägger du till en regel för inkommande port för att tillåta inkommande trafik på port 4438. Anvisningar om hur du lägger till regeln finns i [så här öppnar du portar till en virtuell dator](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuera Event Grid Subscriber IoT Edge-modulen

I det här avsnittet visas hur du distribuerar en annan IoT-modul som fungerar som en händelse hanterare som du kan leverera händelser till.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till** igen. 
1. Från typer av moduler i list rutan väljer du **IoT Edge modul**
1. Ange namn, avbildning och behållar skapande alternativ för behållaren:

   * **Namn**: prenumerant
   * **Bild-URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Alternativ för att skapa behållare**: ingen
1. Klicka på **Spara**
1. Klicka på **Nästa** för att fortsätta till avsnittet vägar

 ### <a name="setup-routes"></a>Installations vägar

Behåll standard vägarna och välj **Nästa** för att fortsätta till gransknings avsnittet

### <a name="submit-the-deployment-request"></a>Skicka distributions förfrågan

1. I avsnittet granska visas JSON-distributions manifestet som skapades utifrån dina val i föregående avsnitt. Bekräfta att du ser båda modulerna: **eventgridmodule** och **prenumeranter** som anges i JSON. 
1. Granska distributions informationen och välj sedan **Skicka**. När du har skickat distributionen kommer du tillbaka till sidan **enhet** .
1. I **avsnittet moduler** kontrollerar du att både **eventgrid** -och **Subscriber** -moduler visas. Och kontrol lera att den **angivna i distributionen** och **rapporteras av enhets** kolumner är inställd på **Ja**.

    Det kan ta en stund innan modulen har startats på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se en uppdaterad status.

## <a name="create-a-topic"></a>Skapa ett ämne

Som utgivare av en händelse måste du skapa ett event Grid-ämne. I Azure Event Grid refererar ett ämne till en slut punkt där utgivare kan skicka händelser till.

1. Skapa topic.jsmed följande innehåll. Mer information om nytto lasten finns i vår [API-dokumentation](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Kör följande kommando för att skapa ett event Grid-ämne. Bekräfta att du ser HTTP-statuskoden är `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Kör följande kommando för att kontrol lera att avsnittet har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration

Prenumeranter kan registrera sig för händelser som publiceras i ett ämne. Om du vill ta emot en händelse måste du skapa en Event Grid prenumeration för ett ämne av intresse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa subscription.jsmed följande innehåll. Mer information om nytto lasten finns i vår [API-dokumentation](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Egenskapen **endpointType** anger att prenumeranten är en **webhook**.  **EndpointUrl** anger URL: en där prenumeranten lyssnar efter händelser. URL: en motsvarar Azure-prenumerantens exempel som du distribuerade tidigare.
2. Kör följande kommando för att skapa en prenumeration för ämnet. Bekräfta att du ser HTTP-statuskoden är `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att kontrol lera att prenumerationen har skapats. HTTP-statuskod på 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event.jsmed följande innehåll. Mer information om nytto lasten finns i vår [API-dokumentation](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Kör följande kommando för att publicera en händelse.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelse leverans

1. SSH eller RDP till din IoT Edge VM.
1. Kontrol lera prenumeranternas loggar:

    Kör följande kommando i Windows:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Kör följande kommando i Linux:

    ```sh
    sudo docker logs subscriber
    ```

    Exempel på utdata:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla prenumerationer.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Ta bort modulen Subscriber från IoT Edge-enheten.


## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat ett event Grid-ämne, prenumeration och publicerade händelser. Nu när du känner till de grundläggande stegen kan du läsa följande artiklar: 

- Information om hur du felsöker problem med att använda Azure Event Grid på IoT Edge finns i [fel söknings guide](troubleshoot.md).
- Skapa/uppdatera prenumeration med [filter](advanced-filtering.md).
- Aktivera persistence för Event Grid modul i [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
- Följ [dokumentationen](configure-client-auth.md) om du vill konfigurera klientautentisering
- Vidarebefordra händelser till Azure Functions i molnet genom att följa den här [självstudien](pub-sub-events-webhook-cloud.md)
- [Reagera på Blob Storage händelser på IoT Edge](react-blob-storage-events-locally.md)
- [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)

