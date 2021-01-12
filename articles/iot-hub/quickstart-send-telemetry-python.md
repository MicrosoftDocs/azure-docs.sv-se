---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du ett Python-exempelprogram som skickar simulerad telemetri till en IoT-hubb och använder ett verktyg för att läsa telemetrin från IoT-hubben.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 71d91ceba48158d2abc688af572dabfc258a2e94
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121449"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Snabb start: skicka telemetri från en enhet till en IoT-hubb och Läs den med Server dels program (python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

I den här snabb starten skickar du telemetri från ett simulerat enhets program via Azure IoT Hub till ett Server dels program för bearbetning. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabb starten används två i förväg skrivna python-program: en för att skicka Telemetrin och en för att läsa Telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). Andra versioner av python som stöds finns i [funktioner i Azure IoT-enheter](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Ett exempel på python-projekt](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Port 8883 öppna i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    **MyPythonDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyPythonDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för den enhet som du har registrerat:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

1. Du behöver också _Event Hubs-kompatibel slut punkt_, _Event Hubs-kompatibel sökväg_ och _tjänstens primära nyckel_ från din IoT Hub för att aktivera backend-programmet för att ansluta till din IoT-hubb och hämta meddelandena. Följande kommandon hämtar dessa värden för din IoT-hubb:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anteckna de här tre värdena, som du kommer att använda senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till Python-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING` variabeln med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. I det lokala terminalfönstret kör du följande kommandon för att köra programmet för simulerad enhet:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

> [!NOTE]
> I följande steg används det synkrona exemplet **read_device_to_cloud_messages_sync. py**. Du kan utföra samma steg med det asynkrona exemplet **read_device_to_cloud_messages_async. py**.

1. Navigera till Python-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

2. Öppna filen **read_device_to_cloud_messages_sync. py** i valfri text redigerare. Uppdatera följande variabler och spara ändringarna i filen.

    | Variabel | Värde |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Ersätt värdet för variabeln med den Event Hubs-kompatibla slut punkten som du antecknade tidigare. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Ersätt värdet för variabeln med Event Hubs-kompatibel sökväg som du antecknade tidigare. |
    | `IOTHUB_SAS_KEY`                | Ersätt värdet för variabeln med tjänstens primära nyckel som du gjorde en anteckning om tidigare. |

3. Installera de bibliotek som krävs för serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Skapa och kör serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Kör serverdelsprogrammet](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med hjälp av ett python-program och läser Telemetrin från hubben med hjälp av ett enkelt Server dels program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-python.md)
