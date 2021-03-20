---
title: Kom igång med Azure IoT Hub Device Management (python) | Microsoft Docs
description: Så här använder du IoT Hub enhets hantering för att starta en fjärran sluten enhet. Du använder Azure IoT SDK för python för att implementera en simulerad enhets app som innehåller en direkt metod och en tjänst-app som anropar den direkta metoden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87872726"
---
# <a name="get-started-with-device-management-python"></a>Kom igång med enhets hantering (python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal för att skapa en IoT Hub och skapa en enhets identitet i din IoT-hubb.

* Skapa en simulerad enhets app som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en python-konsol-app som anropar metoden starta om direkt i den simulerade Device-appen via din IoT Hub.

I slutet av den här självstudien har du två python-konsol program:

* **dmpatterns_getstarted_device. py**, som ansluter till din IoT Hub med enhets identiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service. py**, som anropar en direkt metod i den simulerade Device-appen, visar svaret och visar de uppdaterade egenskaper som rapporteras.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en python-konsol-app som svarar på en direkt metod som anropas av molnet

* Simulera en omstart av enheten

* Använd de rapporterade egenskaperna för att aktivera enhets dubbla frågor för att identifiera enheter och när de startades om senast

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_device. py** i din arbets katalog med hjälp av en text redigerare.

3. Lägg till följande- `import` instruktioner i början av filen **dmpatterns_getstarted_device. py** .

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Lägg till variabeln **CONNECTION_STRING** . Ersätt `{deviceConnectionString}` placeholder-värdet med enhets anslutnings strängen. Du har kopierat den här anslutnings strängen tidigare i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Lägg till följande funktions återanrop för att implementera den direkta metoden på enheten.

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

6. Starta den direkta metoden lyssnare och vänta.

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

7. Spara och Stäng filen **dmpatterns_getstarted_device. py** .

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärran sluten omstart på enheten med en direkt metod

I det här avsnittet skapar du en python-konsol-app som initierar en fjärromstart på en enhet med hjälp av en direkt metod. Appen använder enhets dubbla frågor för att identifiera den senaste omstarts tiden för enheten.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_service. py** i din arbets katalog med hjälp av en text redigerare.

3. Lägg till följande- `import` instruktioner i början av filen **dmpatterns_getstarted_service. py** .

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Lägg till följande variabel deklarationer. Ersätt `{IoTHubConnectionString}` placeholder-värdet med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Ersätt `{deviceId}` placeholder-värdet med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Lägg till följande funktion för att anropa enhets metoden för att starta om mål enheten, och fråga sedan efter enheten och hämta den senaste omstarts tiden.

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

6. Spara och Stäng filen **dmpatterns_getstarted_service. py** .

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Kör följande kommando i kommando tolken för att börja lyssna efter metoden starta om Direct.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Kör följande kommando i en annan kommando tolk för att utlösa fjärromstart och fråga efter enheten för att hitta den senaste omstarts tiden.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Du ser enhets svaret till den direkta metoden i-konsolen.

   Följande visar enhets svaret på den direkta metoden för omstart:

   ![Simulerad enhets programs utdata](./media/iot-hub-python-python-device-management-get-started/device.png)

   Följande visar tjänsten som anropar metoden starta om direkt och avsöker enheten så att den har statusen:

   ![Utlös utdata för att starta om tjänsten](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
