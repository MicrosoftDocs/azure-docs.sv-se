---
title: Snabb start – skicka telemetri till Azure IoT Hub snabb start (C#) | Microsoft Docs
description: I den här snabbstarten kör du två C#-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 914df5b80dee7da041b268a3aaf25ac493d0cf5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624447"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Snabb start: skicka telemetri från en enhet till en IoT-hubb och Läs den med ett tjänst program (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabb starten skickar du telemetri från ett simulerat enhets program, via IoT Hub, till ett tjänst program för bearbetning.

Snabbstarten använder två färdiga C#-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du behöver .NET Core SDK 3,1 eller mer på din utvecklings dator.

    Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

    Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK 3,1 eller senare rekommenderas att kompilera Event Hubs Service-koden som används för att läsa telemetri i den här snabb starten.


* Ladda ned Azure IoT C#-exempel från [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och EXTRAHERA zip-arkivet.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDotnetDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyDotnetDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

3. Du behöver också _Event Hubs-kompatibel slut punkt_, _Event Hubs-kompatibel sökväg_ och _tjänstens primära nyckel_ från din IoT-hubb för att aktivera tjänst programmet för att ansluta till din IoT-hubb och hämta meddelandena. Följande kommandon hämtar dessa värden för din IoT-hubb:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anteckna de här tre värdena, som du kommer att använda senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Navigera sedan till mappen **IoT-hub\Quickstarts\SimulatedDevice**

2. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att skapa och köra det simulerade enhets programmet med enhets anslutnings strängen som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Tjänst programmet ansluter till slut punkten för **händelser** på tjänst sidan på din IoT Hub. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub tjänst program körs vanligt vis i molnet för att ta emot och bearbeta meddelanden från enheten till molnet.

1. Navigera till C#-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Navigera sedan till mappen **IoT-hub\Quickstarts\ReadD2cMessages**

2. I det lokala terminalfönstret kör du följande kommando för att installera de bibliotek som krävs för programmet:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att se parameter alternativen.

    ```cmd/sh
    dotnet run
    ```

4. I det lokala terminalfönstret kör du ett av följande kommando för att skapa och köra programmet med:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    eller

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Följande skärm bild visar resultatet när tjänst programmet tar emot telemetri som skickas av den simulerade enheten till hubben:

    ![Köra tjänst programmet](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med hjälp av ett C#-program och läser Telemetrin från hubben med hjälp av ett enkelt tjänst program.

Om du vill veta hur du styr den simulerade enheten från ett tjänst program fortsätter du till nästa snabb start.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-dotnet.md)
