---
title: Meddelanden från moln till enhet med Azure IoT Hub (python) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för python. Du ändrar en simulerad enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876809"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden från moln till enhet med IoT Hub (python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en simulerad enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Det visar hur du:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

Du hittar mer information om moln-till-enhet-meddelanden i [guiden för IoT Hub utvecklare](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två python-konsol program:

* **SimulatedDevice.py**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage.py**, som skickar meddelanden från moln till enhet till den simulerade Device-appen via IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade Device-appen

I det här avsnittet skapar du en python-konsol-app för att simulera enheten och ta emot meddelanden från molnet till enheten från IoT Hub.

1. Installera **Azure IoT Hub Device SDK för python** från en kommando tolk i arbets katalogen:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Skapa en fil med namnet **SimulatedDevice.py** med hjälp av en text redigerare.

1. Lägg till följande- `import` instruktioner och variabler i början av **SimulatedDevice.py** -filen:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Lägg till följande kod i **SimulatedDevice.py** -filen. Ersätt `{deviceConnectionString}` placeholder-värdet med enhets anslutnings strängen för den enhet som du skapade i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Lägg till följande funktion för att skriva ut mottagna meddelanden till-konsolen:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Lägg till följande kod för att initiera klienten och vänta på att ta emot meddelanden från molnet till enheten:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Lägg till följande huvud funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Spara och stäng filen **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

I det här avsnittet skapar du en python-konsol-app som skickar meddelanden från molnet till enheten till den simulerade Device-appen. Du behöver enhets-ID för enheten som du har lagt till i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start. Du behöver också den IoT Hub-anslutningssträng som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.

1. Öppna en kommando tolk i arbets katalogen och installera **Azure IoT Hub service SDK för python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Skapa en fil med namnet **SendCloudToDeviceMessage.py** med hjälp av en text redigerare.

1. Lägg till följande- `import` instruktioner och variabler i början av **SendCloudToDeviceMessage.py** -filen:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Lägg till följande kod i **SendCloudToDeviceMessage.py** -filen. Ersätt `{iot hub connection string}` värdena och `{device id}` plats hållarna med IoT Hub-anslutningssträngen och enhets-ID: t som du antecknade tidigare:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Lägg till följande kod för att skicka meddelanden till enheten:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Lägg till följande huvud funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Spara och Stäng **SendCloudToDeviceMessage.py** -filen.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando i kommando tolken i arbets katalogen för att lyssna efter meddelanden från molnet till enheten:

    ```shell
    python SimulatedDevice.py
    ```

    ![Kör den simulerade Device-appen](./media/iot-hub-python-python-c2d/device-1.png)

1. Öppna en ny kommando tolk i arbets katalogen och kör följande kommando för att skicka meddelanden från molnet till enheten:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Kör appen för att skicka kommandot från molnet till enheten](./media/iot-hub-python-python-c2d/service.png)

1. Anteckna vilka meddelanden som tas emot av enheten.

    ![Meddelande mottaget](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [Azure IoT Remote Monitoring Solution Accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
