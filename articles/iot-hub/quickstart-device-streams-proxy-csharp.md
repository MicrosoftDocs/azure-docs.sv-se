---
title: Snabb start – Azure IoT Hub enhet som strömmar C#-snabb start för SSH och RDP
description: I den här snabb starten kör du två exempel C#-program som aktiverar SSH-och RDP-scenarier över en IoT Hub enhets ström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 12e26818f86fc4abdc1873d031182fd994c04687
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624379"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabb start: Aktivera SSH och RDP över en IoT Hub enhets ström med hjälp av ett C#-proxyprogram (för hands version)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabb starts guiden omfattar två C#-program som aktiverar trafik mellan klient-och serverprogram (till exempel säker gränssnitt [SSH] och Remote Desktop Protocol [RDP] som ska skickas via en enhets ström som upprättas via en IoT-hubb. En översikt över installationen finns i [exempel på Local proxy-programexempel för SSH eller RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Den här artikeln beskriver först inställningarna för SSH (med port 22) och beskriver hur du ändrar installations port för RDP. Eftersom enhets strömmar är program-och protokoll-oberoende kan samma exempel ändras för att hantera andra typer av program trafik. Ändringen avser vanligt vis bara att ändra kommunikations porten till den som används av det avsedda programmet.

## <a name="prerequisites"></a>Förutsättningar

* För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapas i följande regioner:

  * Central US
  * Centrala USA-EUAP
  * Sydostasien
  * Europa, norra

* De två exempel programmen som du kör i den här snabb starten skrivs i C#. Du behöver .NET Core SDK 2.1.0 eller senare på din utvecklings dator.

    Du kan hämta [.net Core SDK för flera plattformar från .net](https://www.microsoft.com/net/download/all).

    Kontrol lera den aktuella versionen av C# på utvecklings datorn med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* [Ladda ned Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)och extrahera zip-arkivet.

* Ett giltigt användar konto och autentiseringsuppgifter på enheten (Windows eller Linux) som används för att autentisera användaren.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Så här fungerar det

Följande bild illustrerar hur enhets lokala och lokala proxy-program i det här exemplet möjliggör slut punkt till slut punkt mellan SSH-klienten och SSH-daemon-processer. Här förutsätter vi att Daemon körs på samma enhet som enhetens lokala proxy-program.

![Konfiguration av lokalt proxy-program](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Det tjänst lokala proxy-programmet ansluter till IoT-hubben och initierar en enhets ström till mål enheten.

1. Enhetens lokala proxy-program Slutför data strömmens hand skakning och upprättar en slut punkt till slut punkt för strömning via IoT Hub-slutpunkten till tjänst sidan.

1. Den enhets lokala proxy applikationen ansluter till SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "köra enhetens lokala proxy program".

1. Det lokala proxy-programmet väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i det här fallet är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "köra tjänsten-Local proxy app". När användaren ansluter via SSH-klienten möjliggör tunneln att SSH-programtrafik överförs mellan SSH-klienten och serverprogrammet.

> [!NOTE]
> SSH-trafik som skickas över en enhets ström tunnlas via IoT Hub: s strömnings slut punkt i stället för att skickas direkt mellan tjänsten och enheten. Mer information finns i [fördelarna med att använda enhets strömmar för IoT Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabb starten använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhets identiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.
   > * För namnet på enheten som du registrerar rekommenderar vi att du använder min *enhet* som visas. Om du väljer ett annat namn på enheten använder du det namnet i den här artikeln och uppdaterar enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Kör följande kommandon i Cloud Shell för att hämta *enhets anslutnings strängen* för den enhet som du just har registrerat:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhets anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Om du vill ansluta till din IoT-hubb och upprätta en enhets ström behöver du även *tjänst anslutnings strängen* från din IoT-hubb för att aktivera program på tjänst sidan. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du valt för din IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänst anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en slutpunkt-till-slutpunkt-ström för att dirigera SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

I ett lokalt terminalfönster navigerar du till `device-streams-proxy/device` katalogen i den zippade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhets anslutnings strängen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | IP-adressen där SSH-servern lyssnar. Adressen är `localhost` om den var samma IP-adress där enhetens lokala proxy-program körs. |
| `targetServicePort` | Den port som används av ditt program protokoll (för SSH är som standard port 22).  |

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

I ett annat lokalt terminalfönster navigerar du till `iot-hub/quickstarts/device-streams-proxy/service` i den zippade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En lokal port som SSH-klienten ska ansluta till. Vi använder port 2222 i det här exemplet, men du kan använda andra godtyckliga siffror. |

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

### <a name="run-the-ssh-client"></a>Kör SSH-klienten

Använd nu ditt SSH-klientcertifikat och Anslut till tjänstens lokala proxy-program på port 2222 (i stället för SSH daemon direkt).

```
ssh {username}@localhost -p 2222
```

I det här läget kommer du att bli ombedd att ange dina autentiseringsuppgifter i SSH-inloggnings fönstret.

Konsol utmatning på tjänst sidan (tjänstens lokala proxy-program lyssnar på port 2222):

![Utdata från tjänst-lokalt proxy-program](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konsol utdata i programmet för enhets lokalt proxy, som ansluter till SSH-daemonen på *ip_address: 22*:

![Enhet – lokalt proxyprogram utdata](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Konsolens utdata från SSH-klientprogrammet. SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, som det tjänst lokala proxy-programmet lyssnar på:

![Utdata för SSH-klientcertifikat](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP till en enhet via enhetsströmmar

Installations programmet för RDP liknar inställningarna för SSH (se ovan). Du använder RDP-målets IP-adress och port 3389 i stället och använder RDP-klienten (i stället för SSH-klienten).

### <a name="run-the-device-local-proxy-application-rdp"></a>Kör enhet – lokalt proxy-program (RDP)

I ett lokalt terminalfönster navigerar du till `device-streams-proxy/device` katalogen i den zippade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhets anslutnings strängen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | Värd namnet eller IP-adressen som RDP-servern kör. Adressen är `localhost` om den var samma IP-adress där enhetens lokala proxy-program körs. |
| `targetServicePort` | Den port som används av ditt program protokoll (för RDP är som standard port 3389).  |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Kör tjänst-Local proxy-programmet (RDP)

I ett annat lokalt terminalfönster navigerar du till `device-streams-proxy/service` i den zippade projektmappen. Ha följande information till hands:

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

Använd nu RDP-klientprogrammet och Anslut till det lokala proxy-programmet på port 2222 (det var en valfri tillgänglig port som du valde tidigare).

![RDP ansluter till det tjänst lokala proxy-programmet](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, distribuerade enhets lokala och tjänst lokala proxyservrar för att upprätta en enhets ström via IoT Hub och använde proxy-programmen för att dirigera SSH-eller RDP-trafik. Samma paradigm kan hantera andra klient-server-protokoll, där servern körs på enheten (till exempel SSH daemon).

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
