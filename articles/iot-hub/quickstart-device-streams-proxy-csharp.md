---
title: Snabbstart – Azure IoT Hub C#-snabbstart för SSH och RDP
description: I den här snabbstarten kör du två C#-exempelprogram som aktiverar SSH- och RDP-scenarier över en IoT Hub enhetsström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 5bd33a2da6b2f1ae775f088950f14ac4df465fbf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863955"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP via en IoT Hub med hjälp av ett C#-proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstartsguiden omfattar två C#-program som gör det möjligt för klient-server-programtrafik (till exempel Secure Shell [SSH] och Remote Desktop Protocol [RDP] att skickas via en enhetsström som upprättas via en IoT-hubb. En översikt över konfigurationen finns i Exempel [på lokalt proxyprogram för SSH eller RDP.](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Den här artikeln beskriver först konfigurationen för SSH (med port 22) och beskriver sedan hur du ändrar konfigurationens port för RDP. Eftersom enhetsströmmar är program- och protokolloberoende kan samma exempel ändras för att hantera andra typer av programtrafik. Den här ändringen innebär vanligtvis bara att kommunikationsporten ändras till den som används av det avsedda programmet.

## <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:

  * Central US
  * USA, centrala (EUAP)
  * Sydostasien
  * Europa, norra

* De två exempelprogram som du kör i den här snabbstarten är skrivna i C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

    Du kan ladda ned [.NET Core SDK för flera plattformar från .NET](https://dotnet.microsoft.com/download).

    Verifiera den aktuella versionen av C# på utvecklingsdatorn med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* [Ladda ned Azure IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)och extrahera ZIP-arkivet.

* Ett giltigt användarkonto och autentiseringsuppgifter på enheten (Windows eller Linux) som används för att autentisera användaren.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Så här fungerar det

Följande bild illustrerar hur de enhets-lokala och tjänst-lokala proxyprogrammen i det här exemplet möjliggör anslutningar från början till slut mellan SSH-klienten och SSH-daemonprocesserna. Här förutsätter vi att daemonen körs på samma enhet som det enhets lokala proxyprogrammet.

![Installation av lokalt proxyprogram](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Det tjänst lokala proxyprogrammet ansluter till IoT-hubben och initierar en enhetsström till målenheten.

1. Det enhets lokala proxyprogrammet slutför handskakningen för ströminitiering och upprättar en strömningstunnel från slutpunkt till slutpunkt via IoT-hubbens slutpunkt för direktuppspelning till tjänstsidan.

1. Det enhets lokala proxyprogrammet ansluter till den SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhets lokala proxyprogrammet".

1. Det tjänst-lokala proxyprogrammet väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i det här fallet är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det tjänst-lokala proxyprogrammet". När användaren ansluter via SSH-klienten gör tunneln att SSH-programtrafik kan överföras mellan SSH-klienten och serverprogrammet.

> [!NOTE]
> SSH-trafik som skickas via en enhetsström går via IoT-hubbens slutpunkt för direktuppspelning i stället för att skickas direkt mellan tjänst och enhet. Mer information finns i fördelarna med [att använda IoT Hub-enhetsströmmar.](iot-hub-device-streams-overview.md#benefits)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhetsidentiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt *platshållaren YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet som du registrerar rekommenderar vi att du använder *MyDevice* på det sätt som visas. Om du väljer ett annat namn för din enhet använder du det namnet i hela den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Hämta *enhetsanslutningssträngen* för den enhet som du just registrerade genom att köra följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt *platshållaren YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. För att ansluta till din IoT-hubb och  upprätta en enhetsström behöver du även tjänstanslutningssträngen från IoT-hubben för att aktivera programmet på tjänstsidan. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt *platshållaren YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänstanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en ström från början till slut för att tunneltrafik i SSH.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

I ett lokalt terminalfönster navigerar du till `device-streams-proxy/device` katalogen i den uppackade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | IP-adressen där SSH-servern lyssnar. Adressen skulle vara `localhost` om det var samma IP-adress där det enhets lokala proxyprogrammet körs. |
| `targetServicePort` | Porten som används av ditt programprotokoll (för SSH är det som standard port 22).  |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Navigera till i den uppackade `iot-hub/quickstarts/device-streams-proxy/service` projektmappen i ett annat lokalt terminalfönster. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En lokal port som SSH-klienten ska ansluta till. Vi använder port 2222 i det här exemplet, men du kan använda andra godtyckliga nummer. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Köra SSH-klienten

Använd nu SSH-klientprogrammet och anslut till det tjänst lokala proxyprogrammet på port 2222 (i stället för direkt från SSH-daemon).

```
ssh {username}@localhost -p 2222
```

Nu uppmanas du att ange dina autentiseringsuppgifter i SSH-inloggningsfönstret.

Konsolens utdata på tjänstsidan (det tjänst lokala proxyprogrammet lyssnar på port 2222):

![Utdata från tjänst-lokal proxyprogram](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konsolens utdata på det enhets lokala proxyprogrammet, som ansluter till SSH-daemonen *IP_address:22:*

![Utdata från enhets-lokal proxyprogram](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Konsolens utdata för SSH-klientprogrammet. SSH-klienten kommunicerar med SSH-daemonen genom att ansluta till port 22, som det tjänst-lokala proxyprogrammet lyssnar på:

![SSH-klientprogramutdata](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP till en enhet via enhetsströmmar

Konfigurationen för RDP liknar konfigurationen för SSH (beskrivs ovan). Du använder RDP-mål-IP och port 3389 i stället och använder RDP-klienten (i stället för SSH-klienten).

### <a name="run-the-device-local-proxy-application-rdp"></a>Kör det enhets lokala proxyprogrammet (RDP)

I ett lokalt terminalfönster navigerar du till `device-streams-proxy/device` katalogen i den uppackade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | Värdnamnet eller IP-adressen där RDP-servern körs. Adressen skulle vara `localhost` om det var samma IP-adress där det enhets lokala proxyprogrammet körs. |
| `targetServicePort` | Porten som används av programprotokollet (för RDP är detta som standard port 3389).  |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Kör det tjänst-lokala proxyprogrammet (RDP)

Navigera till i den uppackade projektmappen `device-streams-proxy/service` i ett annat lokalt terminalfönster. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En lokal port som SSH-klienten ska ansluta till. Vi använder port 2222 i det här exemplet, men du kan ändra detta till andra godtyckliga nummer. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Köra RDP-klient

Använd RDP-klientprogrammet och anslut till det tjänst lokala proxyprogrammet på port 2222 (det här var en godtycklig tillgänglig port som du valde tidigare).

![RDP ansluter till det tjänst-lokala proxyprogrammet](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerade du en IoT-hubb, registrerade en enhet, distribuerade enhets-lokala och tjänst-lokala proxyprogram för att upprätta en enhetsström via IoT-hubben och använde proxyprogrammen för att skicka SSH- eller RDP-trafik. Samma paradigm kan hantera andra klient-server-protokoll, där servern körs på enheten (till exempel SSH-daemon).

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
