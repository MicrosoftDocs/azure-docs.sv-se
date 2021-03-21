---
title: Snabb start – Azure IoT Hub enhets strömmar C snabb start för SSH och RDP
description: I den här snabb starten kör du ett exempel C-program som fungerar som en proxy för att aktivera SSH-och RDP-scenarier över IoT Hub enhets strömmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: a6e3407d9ddb799ab0ee5846acc149048f593563
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202016"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabb start: Aktivera SSH och RDP över en IoT Hub enhets ström med hjälp av ett C proxy-program (för hands version)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. En översikt över installationen finns i [exempel sidan lokal Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

I den här snabb starten beskrivs inställningarna för SSH-trafik (Secure Shell) i tunnel trafik (med port 22) genom enhets strömmar. Installationen av RDP-trafik (Remote Desktop Protocol) är snarlik och kräver en enkel konfigurations ändring. Eftersom enhets strömmar är program-och protokoll-oberoende kan du ändra den här snabb starten för att hantera andra typer av program trafik.

## <a name="prerequisites"></a>Förutsättningar

* För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapas i följande regioner:

  * Central US
  * Centrala USA-EUAP
  * Norra Europa
  * Sydostasien

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med [Desktop-utveckling med](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbets belastningen C++ aktiverat.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Så här fungerar det

Följande bild illustrerar hur enhets-och tjänst lokala proxy-program möjliggör slutpunkt-till-slutpunkt-anslutningar mellan SSH-klienten och SSH-daemon-processer. Under den offentliga för hands versionen stöder C SDK endast enhets strömmar på enhets sidan. Därför täcker den här snabb starten instruktioner för att endast köra enhetens lokala proxy-program. Följ anvisningarna i något av följande snabb starter för att skapa och köra det medföljande programmet på tjänst sidan:

* [SSH/RDP över IoT Hub enhets strömmar med C#-proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP över IoT Hub enhets strömmar med NodeJS proxy](./quickstart-device-streams-proxy-nodejs.md).

![Konfiguration av lokal Proxy](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Den tjänst-lokala proxyn ansluter till IoT-hubben och startar en enhets ström till mål enheten.

2. Den enhets lokala proxyn Slutför hand skakningen för data strömmen och upprättar en slut punkt till slut punkt för strömning via IoT Hub till tjänst sidan.

3. Den enhets lokala proxyn ansluter till SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "köra enhetens lokala proxy program".

4. Den tjänst-lokala proxyn väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i det här fallet är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "köra enhetens lokala proxy program". När användaren ansluter via SSH-klienten möjliggör tunneln att SSH-programtrafik överförs mellan SSH-klienten och Server programmen.

> [!NOTE]
> SSH-trafik som skickas över en enhets ström tunnlas via IoT Hub: s strömnings slut punkt i stället för att skickas direkt mellan tjänsten och enheten. Mer information finns i [fördelarna med att använda enhets strömmar för IoT Hub](iot-hub-device-streams-overview.md#benefits). Dessutom illustrerar bilden SSH-daemon som körs på samma enhet (eller dator) som enhetens lokala proxy. I den här snabb starten tillhandahåller IP-adressen för SSH-daemon en enhets lokal Proxy och daemon att köras på olika datorer också.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten använder du [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Du förbereder en utvecklings miljö som används för att klona och bygga [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK: n för GitHub innehåller exempel koden som används i den här snabb starten.

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att Visual Studio-förutsättningarna (Visual Studio och *Desktop Development med C++* -arbetsbelastning) är installerade på datorn *innan* du startar cmake-installationen. När kraven är uppfyllda och hämtningen har verifierats kan du installera CMake build-systemet.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden bör ta några minuter.

1. Skapa en *cmake* under katalog i rot katalogen på git-lagringsplatsen och navigera till mappen. Kör följande kommandon från *Azure-IoT-SDK-c-* katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon från *cmake* -katalogen för att skapa en version av SDK som är specifika för din utvecklings klient plattform.

   * I Linux:

      ```bash
      cmake ..
      make -j
      ```

   * I Windows kör du följande kommandon i kommando tolken för utvecklare för Visual Studio 2015 eller 2017. En Visual Studio-lösning för den simulerade enheten kommer att skapas i *cmake* -katalogen.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](/cli/azure/ext/azure-iot/iot) för att registrera en simulerad enhet.

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

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en slutpunkt-till-slutpunkt-ström för att dirigera SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

1. Redigera käll filen **iothub_client_c2d_streaming_proxy_sample. c** i mappen `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` och ange enhets anslutnings strängen, mål enhetens IP/värdnamn och SSH-port 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompilera exemplet:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Kör det kompilerade programmet på enheten:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Som det beskrivs i avsnittet "hur det fungerar" måste en lokal Proxy i varje slut (både tjänsten och enhets sidorna) etableras genom att upprätta en slut punkt till slut punkt för att dirigera SSH-trafik. Under den offentliga för hands versionen stöder IoT Hub C SDK endast enhets strömmar på enhets sidan. Följ anvisningarna i någon av följande snabb starter för att skapa och köra tjänsten-lokal Proxy:

   * [SSH/RDP över IoT Hub enhets strömmar med C#-proxy-appar](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP över IoT Hub enhets strömmar med Node.js proxy-appar](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Upprätta en SSH-session

När både enhets-och tjänstelokala proxyservrar körs använder du SSH-klientprogrammet och ansluter till den lokala proxyn på port 2222 (i stället för SSH daemon direkt).

```cmd/sh
ssh {username}@localhost -p 2222
```

I det här läget kommer du att bli ombedd att ange dina autentiseringsuppgifter i SSH-inloggnings fönstret.

Följande bild visar konsolens utdata på enhetens lokala proxy, som ansluter till SSH-daemonen på `IP_address:22` :

![Enhet – lokal Proxy-utdata](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Följande bild visar konsolens utdata från SSH-klientprogrammet. SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, som den tjänst lokala proxyn lyssnar på:

![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, distribuerat ett enhets-och ett tjänst lokalt proxy-program för att upprätta en enhets ström via IoT Hub och använde proxyservrarna för att dirigera SSH-trafik.

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
