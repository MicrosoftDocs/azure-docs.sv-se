---
title: Snabbstart – Kontrollera en enhet från Azure IoT Hub snabbstart (.NET) | Microsoft Docs
description: I den här snabbstarten kör du två C#-exempelprogram. Det enda programmet är ett tjänstprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb och som kan fjärrstyras.
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
ms.date: 03/04/2020
ms.openlocfilehash: 92f27c16b5dd45352a3a6cb65dd4bf78e88188f0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868581"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhetste telemetri till molnet för lagring eller bearbetning. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub.

Snabbstarten använder två färdiga .NET-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder som anropas från ett tjänstprogram. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett tjänstprogram som anropar direktmetoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du behöver .NET Core SDK 3.1 eller högre på utvecklingsdatorn.

    Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://dotnet.microsoft.com/download).

    Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    dotnet --version
    ```
* Om du inte redan har gjort det laddar du ned Azure IoT C#-exemplen från och https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip extraherar ZIP-arkivet.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här snabbstarten använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDotnetDevice:** Det här är namnet på den enhet som du registrerar. Vi rekommenderar att du använder **MyDotnetDevice** enligt bilden. Om du väljer ett annat namn för din enhet måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du behöver också anslutningssträngen för IoT _Hub-tjänsten_ så att tjänstprogrammet kan ansluta till hubben och hämta meddelandena. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört direktmetoden.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\SimulatedDeviceWithCommand.**

2. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att skapa och köra det simulerade enhetsprogrammet. Ersätt med enhetsanslutningssträngen `{DeviceConnectionString}` som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser. Ett IoT Hub tjänstprogram körs vanligtvis i molnet.

1. Navigera till C#-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\InvokeDeviceMethod.**

2. Installera de bibliotek som krävs för tjänstprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommandon för att skapa och köra tjänstprogrammet. Ersätt med tjänstanslutningssträngen `{ServiceConnectionString}` som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Följande skärmbild visar utdata när programmet gör ett direkt metodanrop till enheten och tar emot en bekräftelse:

    ![Köra tjänstprogrammet](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    När du har kört tjänstprogrammet visas ett meddelande i konsolfönstret som kör den simulerade enheten och hur mycket meddelanden som skickas ändras:

    ![Ändring i den simulerade klienten](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten anropade du en direktmetod på en enhet från ett tjänstprogram och svarade på direktmetods-anropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
