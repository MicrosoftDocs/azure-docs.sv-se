---
title: Kom igång med Azure IoT Hub enhetshantering (Python) | Microsoft Docs
description: Hur du använder IoT Hub för att initiera en omstart av en fjärransluten enhet. Du använder Azure IoT SDK för Python för att implementera en simulerad enhetsapp som innehåller en direktmetod och en tjänstapp som anropar direktmetoden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: c07d26715aff2c8dd5e00a7d7adbb548adf00a28
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482508"
---
# <a name="get-started-with-device-management-python"></a>Kom igång med enhetshantering (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa IoT Hub och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som innehåller en direktmetod som startar om enheten. Direktmetoder anropas från molnet.

* Skapa en Python-konsolapp som anropar direktmetoden för omstart i den simulerade enhetsappen via din IoT-hubb.

I slutet av den här självstudien har du två Python-konsolappar:

* **dmpatterns_getstarted_device.py**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, tar emot en direktmetod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.py**, som anropar en direktmetod i den simulerade enhetsappen, visar svaret och visar de uppdaterade rapporterade egenskaperna.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här artikeln använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en Python-konsolapp som svarar på en direktmetod som anropas av molnet

* Simulera en omstart av enheten

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor för att identifiera enheter och när de senast startades om

1. Installera paketet **azure-iot-device** genom att köra följande kommando i kommandotolken:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_device.py i arbetskatalogen med hjälp** av en textredigerare.

3. Lägg till följande `import` -instruktioner i början av **filen dmpatterns_getstarted_device.py.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Lägg till **CONNECTION_STRING** variabeln . Ersätt `{deviceConnectionString}` platshållarvärdet med enhetens anslutningssträng. Du kopierade den här anslutningssträngen tidigare [i Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Lägg till följande funktionsanrop för att implementera direktmetoden på enheten.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Starta lyssnaren för direktmetoden och vänta.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spara och stäng **filen dmpatterns_getstarted_device.py.**

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Hantering av tillfälliga fel.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Hämta IoT Hub-anslutningssträngen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärrstart på enheten med en direktmetod

I det här avsnittet skapar du en Python-konsolapp som initierar en fjärrstart på en enhet med hjälp av en direktmetod. Appen använder enhetstvillingfrågor för att identifiera den senaste omstartstiden för enheten.

1. Installera paketet **azure-iot-hub** genom att köra följande kommando i kommandotolken:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_service.py i arbetskatalogen med hjälp** av en textredigerare.

3. Lägg till följande `import` -instruktioner i början av **filen dmpatterns_getstarted_service.py.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Lägg till följande variabeldeklarationer. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du kopierade tidigare i Hämta `{IoTHubConnectionString}` [IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Ersätt `{deviceId}` platshållarvärdet med det enhets-ID som du registrerade [i Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Lägg till följande funktion för att anropa enhetsmetoden för att starta om målenheten, sedan fråga efter enhetstvillingarna och hämta den senaste omstartstiden.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Spara och stäng **filen dmpatterns_getstarted_service.py.**

## <a name="run-the-apps"></a>Kör apparna

Nu är du redo att köra apparna.

1. Kör följande kommando i kommandotolken för att börja lyssna efter direktmetoden för omstart.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Kör följande kommando i en annan kommandotolk för att utlösa fjärrstarten och fråga efter enhetstvillingen för att hitta den senaste omstartstiden.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Du ser enhetssvaret på direktmetoden i -konsolen.

   Nedan visas enhetssvaret på direktmetoden för omstart:

   ![Utdata från simulerad enhetsapp](./media/iot-hub-python-python-device-management-get-started/device.png)

   Följande visar tjänsten som anropar direktmetoden för omstart och avsöker enhetstvillingen efter status:

   ![Utdata från omstartstjänsten](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
