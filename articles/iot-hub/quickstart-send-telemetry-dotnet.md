---
title: Snabbstart – Skicka telemetri till Azure IoT Hub snabbstart (C#) | Microsoft Docs
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
ms.openlocfilehash: ec26e4a42cdeb7a693c8078c0f065241f32da949
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864000"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs den med ett tjänstprogram (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram, via IoT Hub, till ett tjänstprogram för bearbetning.

Snabbstarten använder två färdiga C#-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du behöver .NET Core SDK 3.1 eller högre på utvecklingsdatorn.

    Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://dotnet.microsoft.com/download).

    Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK 3.1 eller högre rekommenderas att kompilera Event Hubs tjänstkod som används för att läsa telemetri i den här snabbstarten.


* Ladda ned Azure IoT C#-exemplen från [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera ZIP-arkivet.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här snabbstarten använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDotnetDevice:** Det här är namnet på den enhet som du registrerar. Vi rekommenderar att du använder **MyDotnetDevice** enligt bilden. Om du väljer ett annat namn för din enhet måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Kör följande kommando i Azure Cloud Shell hämta _enhetsanslutningssträngen_ för den enhet som du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Du använder det här värdet senare i snabbstarten.

3. Du behöver även Event Hubs kompatibel slutpunkt, Event Hubs-kompatibel sökväg och  primär nyckel för tjänsten från din IoT-hubb så att tjänstprogrammet kan ansluta till din IoT-hubb och hämta meddelanden.   Följande kommandon hämtar dessa värden för din IoT-hubb:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anteckna dessa tre värden, som du kommer att använda senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\SimulatedDevice.**

2. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att skapa och köra det simulerade enhetsprogrammet med enhetsanslutningssträngen som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Tjänstprogrammet ansluter till slutpunkten händelser på **tjänstsidan** på din IoT Hub. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub tjänstprogram körs vanligtvis i molnet för att ta emot och bearbeta meddelanden från enheten till molnet.

1. Navigera till C#-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\ReadD2cMessages.**

2. I det lokala terminalfönstret kör du följande kommando för att installera de bibliotek som krävs för programmet:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att se parameteralternativen.

    ```cmd/sh
    dotnet run
    ```

4. I det lokala terminalfönstret kör du något av följande kommando för att skapa och köra programmet med :

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    eller

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Följande skärmbild visar utdata när tjänstprogrammet tar emot telemetri som skickas av den simulerade enheten till hubben:

    ![Köra tjänstprogrammet](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten ska du konfigurera en IoT-hubb, registrera en enhet, skicka simulerad telemetri till hubben med hjälp av ett C#-program och läsa telemetrin från hubben med hjälp av ett enkelt tjänstprogram.

Fortsätt till nästa snabbstart om du vill lära dig hur du styr din simulerade enhet från ett tjänstprogram.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-dotnet.md)
