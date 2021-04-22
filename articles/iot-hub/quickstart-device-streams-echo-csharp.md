---
title: Snabbstart – Kommunicera med enhetsappen i C# med Azure IoT Hub enhetsströmmar
description: I den här snabbstarten kör du två C#-exempelprogram som kommunicerar via en enhetsström som upprättas via IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c4f6e06524412c76661623cb5ef2985a41f2d7fc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864124"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i C# via IoT Hub enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstarten omfattar två C#-program som drar nytta av enhetsströmmar för att skicka data fram och tillbaka (eko).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:
  * Central US
  * USA, centrala (EUAP)
  * Norra Europa
  * Sydostasien

* De två exempelprogram som du kör i den här snabbstarten är skrivna i C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

    Ladda ned [.NET Core SDK för flera plattformar från .NET](https://dotnet.microsoft.com/download).

    Verifiera den aktuella versionen av C# på utvecklingsdatorn med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* [Ladda ned Azure IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera ZIP-arkivet. Du behöver den både på enhetssidan och på tjänstsidan.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhetsidentiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt *platshållaren YourIoTHubName* med det namn som du valde för din IoT-hubb.
   > * För namnet på den enhet som du registrerar rekommenderar vi att du använder *MyDevice* på det sätt som visas. Om du väljer ett annat namn för din enhet använder du det namnet i hela den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Hämta *enhetsanslutningssträngen* för den enhet som du just registrerade genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > Ersätt *platshållaren YourIoTHubName* med det namn som du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Du behöver även *tjänstanslutningssträngen* från din IoT-hubb för att göra så att programmet på tjänstsidan kan ansluta till din IoT-hubb och upprätta en enhetsström. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt *platshållaren YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänstanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enhetsströmmar

I det här avsnittet kör du både programmet på enhetssidan och programmet på tjänstsidan och kommunicerar mellan de två.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

I ett lokalt terminalfönster navigerar du till `iot-hub/Quickstarts/device-streams-echo/service` katalogen i den uppackade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Programmet väntar på att enhetsprogrammet ska bli tillgängligt.

> [!NOTE]
> En timeout uppstår om programmet på enhetssidan inte svarade i tid.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Navigera till katalogen i den uppackade `iot-hub/Quickstarts/device-streams-echo/device` projektmappen i ett annat lokalt terminalfönster. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för din IoT Hub. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till din enhet. När dataströmmen har upprättats skickar programmet en strängbuffert till tjänsten via dataströmmen. I det här exemplet upprepar programmet på tjänstsidan helt enkelt samma data till enheten, vilket visar en lyckad dubbelriktad kommunikation mellan de två programmen.

Konsolens utdata på enhetssidan:

![Konsolens utdata på enhetssidan](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsolens utdata på tjänstsidan:

![Konsolutdata på tjänstsidan](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Trafiken som skickas via dataströmmen skickas via IoT-hubben i stället för att skickas direkt. Fördelarna som ges beskrivs i [Fördelar med enhetsströmmar.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerade du en IoT-hubb, registrerade en enhet, etablerade en enhetsström mellan C#-program på enhets- och tjänstsidan och använde dataströmmen för att skicka data fram och tillbaka mellan programmen.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
