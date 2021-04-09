---
title: 'Snabb start: skicka telemetri till Azure IoT (Node.js)'
description: I den här snabbstarten kör du två Node.js-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 7aa95e2117dc3bb2e837e62ef42e3a770f2266d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842149"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Snabb start: skicka telemetri från en enhet till en IoT-hubb och Läs den med ett Server dels program (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 I den här snabb starten skickar du telemetri från ett simulerat enhets program via Azure IoT Hub till ett Server dels program för bearbetning. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabb starten används två fördefinierade Node.js-program: en för att skicka Telemetrin och en för att läsa Telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Node.js 10 +](https://nodejs.org). Om du använder Azure Cloud Shell ska du inte uppdatera den installerade versionen av Node.js. Den Azure Cloud Shell har redan den senaste Node.js versionen.

    Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    node --version
    ```

* [Ett exempel på ett Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Port 8883 öppna i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyNodeDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyNodeDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

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

1. Öppna ditt lokala terminalfönster och navigera till Node.js-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.js** i en valfri textredigerare.

    Ersätt värdet för `connectionString` variabeln med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice.js**.

1. Installera de lokala bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

1. Öppna ett annat lokalt terminalfönster och navigera till Node.js-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

1. Öppna filen **ReadDeviceToCloudMessages.js** i en valfri textredigerare. Uppdatera följande variabler och spara ändringarna i filen.

    | Variabel | Värde |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Ersätt värdet för variabeln med den Event Hubs-kompatibla slut punkten som du antecknade tidigare. |
    | `eventHubsCompatiblePath`     | Ersätt värdet för variabeln med Event Hubs-kompatibel sökväg som du antecknade tidigare. |
    | `iotHubSasKey`                | Ersätt värdet för variabeln med tjänstens primära nyckel som du gjorde en anteckning om tidigare. |

1. Installera de bibliotek som krävs för serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Kör serverdelsprogrammet](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med ett Node.js program och läser Telemetrin från hubben med hjälp av ett enkelt Server dels program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-node.md)
