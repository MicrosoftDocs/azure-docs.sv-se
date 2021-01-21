---
title: Snabb start – kommunicera med enhetens app i C# med Azure IoT Hub enhets strömmar
description: I den här snabb starten kör du två exempel C#-program som kommunicerar via en enhets ström som upprättats via IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624616"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabb start: kommunicera med ett enhets program i C# via IoT Hub enhets strömmar (för hands version)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. I den här snabb starten ingår två C#-program som utnyttjar enhets strömmar för att skicka data fram och tillbaka (eko).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapas i följande regioner:
  * Central US
  * Centrala USA-EUAP
  * Norra Europa
  * Sydostasien

* De två exempel programmen som du kör i den här snabb starten skrivs i C#. Du behöver .NET Core SDK 2.1.0 eller senare på din utvecklings dator.

    Hämta [.net Core SDK för flera plattformar från .net](https://www.microsoft.com/net/download/all).

    Kontrol lera den aktuella versionen av C# på utvecklings datorn med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* [Ladda ned Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera zip-arkivet. Du behöver det både på enhets sidan och på tjänst sidan.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhets identiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.
   > * För namnet på enheten som du registrerar rekommenderar vi att du använder min *enhet* som visas. Om du väljer ett annat namn på enheten använder du det namnet i den här artikeln och uppdaterar enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Kör följande kommando i Cloud Shell för att hämta *enhets anslutnings strängen* för den enhet som du just har registrerat:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhets anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Du behöver även *tjänstanslutningssträngen* från din IoT-hubb för att göra så att programmet på tjänstsidan kan ansluta till din IoT-hubb och upprätta en enhetsström. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänst anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enhets strömmar

I det här avsnittet ska du köra både program på enhets sidan och programmet på tjänst sidan och kommunicera mellan de två.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

I ett lokalt terminalfönster navigerar du till `iot-hub/Quickstarts/device-streams-echo/service` katalogen i den zippade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänst anslutnings strängen för din IoT Hub. |
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
Programmet väntar på att enhets programmet ska bli tillgängligt.

> [!NOTE]
> En timeout uppstår om programmet på enhetssidan inte svarade i tid.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

I ett annat lokalt terminalfönster navigerar du till `iot-hub/Quickstarts/device-streams-echo/device` katalogen i den zippade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhets anslutnings strängen för IoT Hub. |

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

I slutet av det sista steget initierar programmet på tjänst sidan en ström till enheten. När data strömmen har upprättats skickar programmet en strängkonstant till tjänsten över data strömmen. I det här exemplet upprepar programmet på tjänst sidan bara tillbaka samma data på enheten, vilket visar att en lyckad dubbelriktad kommunikation mellan de två programmen har genomförts.

Konsol utmatning på enhets sidan:

![Konsol utmatning på enhets Sidan](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsol utdata på tjänst sidan:

![Konsol utdata på tjänst Sidan](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Trafiken som skickas via data strömmen tunnlas via IoT-hubben i stället för att skickas direkt. De fördelar som anges beskrivs i [fördelarna med enhets strömmar](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, upprättat en enhets ström mellan C#-program på enhets-och tjänst sidorna och använde data strömmen för att skicka data fram och tillbaka mellan programmen.

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
