---
title: Schemalägga jobb med Azure IoT Hub (python) | Microsoft Docs
description: 'Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT SDK: er för python för att implementera de simulerade enhetens appar och en tjänst-app för att köra jobbet.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87876690"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Schema-och sändnings jobb (python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som gör det möjligt för en backend-app att skapa och spåra jobb som schemalägger och uppdaterar miljon tals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

Ett jobb är konceptuellt, och spårar förloppet för körningen mot en uppsättning enheter som definieras av en enhets dubbla frågor.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart på 10 000 enheter, som anges av en enhets dubbla frågor och schemaläggs vid ett senare tillfälle.  Programmet kan sedan spåra förloppet när var och en av enheterna får och kör metoden starta om.

Lär dig mer om var och en av dessa funktioner i dessa artiklar:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-python-twin-getstarted.md) och [Självstudier: hur du använder enhetens dubbla egenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub Developer Guide – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: direkta metoder](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en python-simulerad Device-app som har en direkt metod som aktiverar **lockDoor**, som kan anropas av lösningens Server del.

* Skapa en python-konsol-app som anropar **lockDoor** Direct-metoden i den simulerade Device-appen med ett jobb och uppdaterar önskade egenskaper med ett enhets jobb.

I slutet av den här självstudien har du två python-appar:

**simDevice.py**, som ansluter till din IoT Hub med enhets identiteten och får en **lockDoor** Direct-metod.

**scheduleJobService.py**, som anropar en direkt metod i den simulerade Device-appen och uppdaterar enhetens dubbla egenskaper med ett jobb.

> [!NOTE]
> **Azure IoT SDK för python** stöder inte **jobb** funktioner direkt. I stället erbjuder den här självstudien en alternativ lösning som använder asynkrona trådar och timers. Mer uppdateringar finns i funktions listan för **service client SDK** på sidan [Azure IoT SDK för python](https://github.com/Azure/azure-iot-sdk-python) .
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en python-konsol-app som svarar på en direkt metod som anropas av molnet, vilket utlöser en simulerad **lockDoor** -metod.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en ny **simDevice.py** -fil i din arbets katalog med hjälp av en text redigerare.

3. Lägg till följande- `import` instruktioner och variabler i början av **simDevice.py** -filen. Ersätt `deviceConnectionString` med anslutnings strängen för den enhet som du skapade ovan:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktions anrop för att hantera metoden **lockDoor** :

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Lägg till en annan funktion motringning för att hantera enhetens dubbla uppdateringar:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Lägg till följande kod för att registrera hanteraren för **lockDoor** -metoden. Ta även med `main` rutinen:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Spara och Stäng filen **simDevice.py** .

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst som anropar en direkt metod på en enhet och uppdaterar enheten med dubbla. Tjänsten behöver **service Connect-** behörighet för att anropa en direkt metod på en enhet. Tjänsten behöver också **Läs** -och Skriv behörighet för registret för att läsa **och skriva identitets** registret. Det finns ingen standard princip för delad åtkomst som bara innehåller de här behörigheterna, så du måste skapa en.

Följ dessa steg om du vill skapa en princip för delad åtkomst som beviljar **tjänsten Connect**, **Registry Read** och **Registry Skriv** behörigheter och för att få en anslutnings sträng för den här principen:

1. Öppna din IoT Hub i [Azure Portal](https://portal.azure.com). Det enklaste sättet att komma till din IoT-hubb är att välja **resurs grupper**, välja resurs gruppen där IoT Hub finns och sedan välja din IoT Hub i listan över resurser.

2. I den vänstra rutan i IoT Hub väljer du **principer för delad åtkomst**.

3. Välj **Lägg till** på den översta menyn ovanför listan över principer.

4. I fönstret **Lägg till en princip för delad åtkomst** anger du ett beskrivande namn för principen. till exempel: *serviceAndRegistryReadWrite*. Under **behörigheter** väljer du **tjänst anslutning** och **Skriv register** (**register läsning** väljs automatiskt när du väljer **register skrivning**). Välj sedan **Skapa**.

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Gå tillbaka till fönstret **principer för delad åtkomst** och välj den nya principen i listan över principer.

6. Under **delade åtkomst nycklar** väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägg jobb för att anropa en direkt metod och uppdatera en enhets dubbla egenskaper

I det här avsnittet skapar du en python-konsolsession som initierar en fjärran sluten **lockDoor** på en enhet med hjälp av en direkt metod och uppdaterar även enhetens dubbla egenskaper.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Skapa en ny **scheduleJobService.py** -fil i din arbets katalog med hjälp av en text redigerare.

3. Lägg till följande- `import` instruktioner och variabler i början av **scheduleJobService.py** -filen. Ersätt `{IoTHubConnectionString}` plats hållaren med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Ersätt `{deviceId}` plats hållaren med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub):

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Lägg till följande funktion som används för att fråga efter enheter:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Lägg till följande metoder för att köra jobb som anropar Direct-metoden och enheten dubbla:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Lägg till följande kod för att schemalägga jobb och uppdatera jobb status. Ta även med `main` rutinen:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Spara och Stäng filen **scheduleJobService.py** .

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando i kommando tolken i din arbets katalog för att börja lyssna efter metoden starta om Direct:

    ```cmd/sh
    python simDevice.py
    ```

2. Kör följande kommando i en annan kommando tolk i arbets katalogen för att utlösa jobben för att låsa dörren och uppdatera den dubbla:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Du ser enhets Svaren till direkt metoden och enheten har dubbla uppdateringar i-konsolen.

    ![IoT Hub jobb exempel 1--enhets utdata](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub jobb exempel 2--enhets utdata](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

Om du vill fortsätta att komma igång med IoT Hub-och enhets hanterings mönster, till exempel fjärran sluten av den inbyggda program varan, kan du läsa mer i [Uppdatera](tutorial-firmware-update.md)
