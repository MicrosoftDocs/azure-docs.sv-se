---
title: Kom igång med Azure IoT Hub Device Ungarns (python) | Microsoft Docs
description: 'Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT SDK: er för python för att implementera den simulerade Device-appen och en service-app som lägger till taggarna och kör IoT Hubs frågan.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87875480"
---
# <a name="get-started-with-device-twins-python"></a>Kom igång med enhets garn (python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien kommer du att ha två python-konsol program:

* **AddTagsAndQuery.py**, en python-backend-app, som lägger till taggar och frågor enhets dubbla.

* **ReportConnectivity.py**, en python-app, som simulerar en enhet som ansluter till din IoT Hub med enhets identiteten som skapades tidigare och rapporterar dess anslutnings tillstånd.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en python-konsol-app som lägger till platsens metadata till den enhet som är kopplad till **{Device ID}**. Den frågar sedan enheten efter varandra i IoT Hub och väljer enheterna som finns i Redmond och sedan de som rapporterar en mobil anslutning.

1. Öppna en kommando tolk i arbets katalogen och installera **Azure IoT Hub service SDK för python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Skapa en ny **AddTagsAndQuery.py** -fil med hjälp av en text redigerare.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Lägg till följande kod. Ersätt `[IoTHub Connection String]` med IoT Hub-anslutningssträngen som du kopierade i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen. Ersätt `[Device Id]` med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Lägg till följande kod i **AddTagsAndQuery.py** -filen:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **IoTHubRegistryManager** -objektet exponerar alla de metoder som krävs för att interagera med enheten tillsammans från tjänsten. Koden initierar först **IoTHubRegistryManager** -objektet, uppdaterar sedan enheten med **DEVICE_ID** och kör slutligen två frågor. Först väljer den bara enheten med enheter som finns i **Redmond43** -anläggningen och den andra refinar frågan så att endast de enheter som också är anslutna via ett mobil nät.

6. Lägg till följande kod i slutet av  **AddTagsAndQuery.py** för att implementera funktionen **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Kör programmet med:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för den fråga som begränsar resultatet till enheter som använder ett mobil nät verk.

    ![första frågan som visar alla enheter i Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

I nästa avsnitt skapar du en enhets app som rapporterar anslutnings informationen och ändrar resultatet för frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet ska du skapa en python-konsol-app som ansluter till din hubb som **{Device ID}** och sedan uppdaterar dess enhets dubbla rapporter som innehåller den information som den är ansluten till med ett mobilt nätverk.

1. Installera **Azure IoT Hub Device SDK för python** från en kommando tolk i arbets katalogen:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en ny **ReportConnectivity.py** -fil med hjälp av en text redigerare.

3. Lägg till följande kod för att importera de nödvändiga modulerna från enhets-SDK:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Lägg till följande kod. Ersätt `[IoTHub Device Connection String]` placeholder-värdet med enhets anslutnings strängen som du kopierade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Lägg till följande kod i **ReportConnectivity.py** -filen för att implementera enhetens dubbla funktioner:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    **IoTHubModuleClient** -objektet visar alla metoder som du behöver för att interagera med enheten mellan enheten. Föregående kod, efter att den initierat **IoTHubModuleClient** -objektet, hämtar enheten till enheten och uppdaterar den rapporterade egenskapen med anslutnings informationen.

6. Lägg till följande kod i slutet av  **ReportConnectivity.py** för att implementera funktionen **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Kör enhets appen:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Du bör se bekräftelse på enhetens dubbla rapporterade egenskaper har uppdaterats.

    ![uppdatera rapporterade egenskaper från Device app](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nu när enheten rapporterade anslutnings information, bör den visas i båda frågorna. Gå tillbaka och kör frågorna igen:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Den här gången ska **{Device ID}** visas i båda frågeresultaten.

    ![andra frågan på tjänst programmet](./media/iot-hub-python-twin-getstarted/service-2.png)

    I din enhets app ser du en bekräftelse på att de önskade egenskaperna för den dubbla korrigeringen som skickas av tjänst appen togs emot.

    ![ta emot önskade egenskaper för Device app](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en simulerad Device-app för att rapportera enhetens anslutnings information på enheten. Du har också lärt dig hur du frågar den här informationen med hjälp av registret.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med självstudierna [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) .

* Konfigurera enheter med enhetens dubbla egenskaper med hjälp av självstudierna [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) .

* Kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-python.md) .
