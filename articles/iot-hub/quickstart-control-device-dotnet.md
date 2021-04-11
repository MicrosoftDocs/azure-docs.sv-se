---
title: Snabb start – styra en enhet från Azure IoT Hub snabb start (.NET) | Microsoft Docs
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
ms.openlocfilehash: b89cc9dfb0a98f61b74ecf42471d08129661fb22
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059895"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhets telemetri till molnet för lagring eller bearbetning. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub.

Snabbstarten använder två färdiga .NET-program:

* Ett simulerat enhets program som svarar på direkta metoder som anropas från ett tjänst program. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett tjänst program som anropar de direkta metoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du behöver .NET Core SDK 3,1 eller mer på din utvecklings dator.

    Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

    Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    dotnet --version
    ```
* Om du inte redan har gjort det kan du hämta Azure IoT C#-exempel från https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip och EXTRAHERA zip-arkivet.

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDotnetDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyDotnetDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

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

Du behöver också anslutnings strängen för IoT Hub _-tjänsten_ för att aktivera tjänst programmet för att ansluta till hubben och hämta meddelandena. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänst anslutnings strängen skiljer sig från den enhets anslutnings sträng som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört den direkta metoden.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Navigera sedan till mappen **IoT-hub\Quickstarts\SimulatedDeviceWithCommand**

2. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommando för att skapa och köra det simulerade enhets programmet, ersätta `{DeviceConnectionString}` med den enhets anslutnings sträng som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metod anrop till en enhet via din IoT Hub och lyssnar efter bekräftelser. Ett IoT Hub tjänst program körs vanligt vis i molnet.

1. Navigera till C#-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Navigera sedan till mappen **IoT-hub\Quickstarts\InvokeDeviceMethod**

2. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs för tjänst programmet:

    ```cmd/sh
    dotnet restore
    ```

3. I det lokala terminalfönstret kör du följande kommandon för att skapa och köra tjänst programmet, och ersätter `{ServiceConnectionString}` med anslutnings strängen för tjänsten som du antecknade tidigare:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Följande skärm bild visar resultatet när programmet gör ett direkt metod anrop till enheten och får en bekräftelse:

    ![Köra tjänst programmet](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    När du har kört tjänst programmet visas ett meddelande i konsol fönstret som kör den simulerade enheten och den hastighet som den skickar ändringar i meddelanden:

    ![Ändring i den simulerade klienten](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten kallades du en direkt metod på en enhet från ett tjänst program och svarade på det direkta metod anropet i ett simulerat enhets program.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
