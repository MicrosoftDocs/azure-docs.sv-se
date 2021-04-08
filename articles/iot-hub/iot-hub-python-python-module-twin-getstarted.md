---
title: Azure IoT Hub-modulens identitet och modul, dubbla (python)
description: 'Lär dig att skapa modul identitet och uppdatera modul dubbla med IoT SDK: er för python.'
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139394"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Kom igång med IoT Hub-modulens identitet och modul, dubbla (python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modul identiteter och modulernas dubblare](iot-hub-devguide-module-twins.md) liknar Azure IoT Hub enhets identiteter och enheter, men ger bättre granularitet. Även om Azure IoT Hub enhets identiteter och enhet är dubbla, gör det möjligt att konfigurera en enhet och tillhandahålla synlighet på enhetens villkor, modulens identiteter och modulerna ger dessa funktioner för enskilda komponenter i en enhet. På enheter med flera komponenter, till exempel operativ systembaserade enheter eller inbyggda enheter, kan de isolera konfiguration och villkor för varje komponent.
>

I slutet av den här självstudien har du tre python-appar:

* **CreateModule**, som skapar en enhets identitet, en modul identitet och tillhör ande säkerhets nycklar för att ansluta dina enhets-och modul klienter.

* **UpdateModuleTwinDesiredProperties**, som skickar uppdaterad modul dubbla önskade egenskaper till din IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, som tar emot modulen med den dubbla önskade egenskaps korrigeringen på enheten.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en server dels tjänst som lägger till en enhet i identitets registret och lägger sedan till en modul till den enheten. Den här tjänsten kräver **Skriv** behörighet för registret (som även innehåller **register läsning**). Du skapar också en tjänst som lägger till önskade egenskaper i modulen för den nyligen skapade modulen. Tjänsten måste ha behörighet för **tjänst anslutning** . Även om det finns standard principer för delad åtkomst som beviljar dessa behörigheter individuellt, i det här avsnittet skapar du en anpassad princip för delad åtkomst som innehåller båda dessa behörigheter.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhets identitet och en modul identitet i IoT Hub

I det här avsnittet skapar du en python-tjänsteapp som skapar en enhets identitet och en modul identitet i identitets registret i din IoT-hubb. En enhet eller modul kan inte ansluta till IoT Hub om den inte har en post i identitets registret. Mer information finns i [förstå identitets registret i din IoT-hubb](iot-hub-devguide-identity-registry.md). När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Kör följande kommando i kommando tolken för att installera **msrest** -paketet. Du behöver det här paketet för att kunna fånga **HTTPOperationError** -undantag.

    ```cmd/sh
    pip install msrest
    ```

1. Skapa en fil med namnet **CreateModule.py** i din arbets katalog med hjälp av en text redigerare.

1. Lägg till följande kod i python-filen. Ersätt *YourIoTHubConnectionString* med anslutnings strängen som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Kör följande kommando i kommando tolken:

    ```cmd/sh
    python CreateModule.py
    ```

Den här appen skapar en enhets identitet med ID **t myfirstdevice** och en modul identitet med ID **MyFirstModule** under enhet **t myfirstdevice**. (Om enhets-ID: t redan finns i identitets registret hämtar koden bara den befintliga enhets-eller modulens information.) Appen visar ID och primär nyckel för varje identitet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [förstå identitets registret i din IoT-hubb](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Uppdatera modul dubbla med python-tjänst-SDK

I det här avsnittet skapar du en python-tjänsteapp som uppdaterar modulen med de dubbla önskade egenskaperna.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Hub** . Du kan hoppa över det här steget om du har installerat **Azure-IoT-Hub-** paketet i föregående avsnitt.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Skapa en fil med namnet **UpdateModuleTwinDesiredProperties.py** i din arbets katalog med hjälp av en text redigerare.

1. Lägg till följande kod i python-filen. Ersätt *YourIoTHubConnectionString* med anslutnings strängen som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Hämta uppdateringar på enhets Sidan

I det här avsnittet skapar du en python-app för att hämta modulen med de dubbla önskade egenskaperna uppdatera på enheten.

1. Hämta anslutnings strängen för din modul. I [Azure Portal](https://portal.azure.com/)navigerar du till din IoT Hub och väljer **IoT-enheter** i den vänstra rutan. Välj **t myfirstdevice** i listan över enheter och öppna den. Under **modul identiteter** väljer du **myFirstModule**. Kopiera modulens anslutningssträng. Du behöver den i ett av följande steg.

   ![Information om Azure-portalmodulen](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Skapa en fil med namnet **ReceiveModuleTwinDesiredPropertiesPatch.py** i din arbets katalog med hjälp av en text redigerare.

1. Lägg till följande kod i python-filen. Ersätt *YourModuleConnectionString* med den modulens anslutnings sträng som du kopierade i steg 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Kör apparna

I det här avsnittet kör du **ReceiveModuleTwinDesiredPropertiesPatch** Device-appen och kör sedan **UpdateModuleTwinDesiredProperties** -tjänstens app för att uppdatera önskade egenskaper för modulen.

1. Öppna en kommando tolk och kör enhets appen:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Första utdata för enhets app](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Öppna en separat kommando tolk och kör tjänst appen:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Observera att den önskade egenskapen **TelemetryInterval** visas i den uppdaterade modulen i din tjänsts app-utdata:

   ![Utdata för service-app](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Samma egenskap visas i den önskade egenskaper-korrigeringen som tas emot i enhetens app-utdata:

   ![Enhetens app-utdata visar önskade egenskaper-korrigering](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/quickstart-linux.md)