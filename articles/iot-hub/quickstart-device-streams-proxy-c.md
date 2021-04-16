---
title: Snabbstart – Azure IoT Hub C-snabbstart för SSH och RDP
description: I den här snabbstarten kör du ett C-exempelprogram som fungerar som proxy för att aktivera SSH- och RDP-scenarier över IoT Hub enhetsströmmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c9c09914e30bdeae809924bc4256c773f42abd52
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479949"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP över en IoT Hub enhetsström med hjälp av ett C-proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. En översikt över konfigurationen finns på [sidan Exempel på lokal proxy.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Den här snabbstarten beskriver konfigurationen för tunneltrafik med Secure Shell (SSH) (med port 22) via enhetsströmmar. Konfigurationen för RDP Remote Desktop Protocol trafik (Rdp) är liknande och kräver en enkel konfigurationsändring. Eftersom enhetsströmmar är program- och protokolloberoende kan du ändra den här snabbstarten för att hantera andra typer av programtrafik.

## <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:

  * Central US
  * USA, centrala (EUAP)
  * Norra Europa
  * Sydostasien

* Installera [Visual Studio 2019 med](https://www.visualstudio.com/vs/) [Desktop-utveckling med C++-arbetsbelastningen](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) aktiverad.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Så här fungerar det

Följande bild illustrerar hur enhets- och tjänst-lokala proxyprogram möjliggör anslutningar från början till slut mellan SSH-klienten och SSH-daemonprocesserna. Under den offentliga förhandsversionen stöder C SDK endast enhetsströmmar på enhetssidan. Därför omfattar den här snabbstarten instruktioner för att endast köra det enhets lokala proxyprogrammet. Om du vill skapa och köra tillhörande program på tjänstsidan följer du anvisningarna i någon av följande snabbstarter:

* [SSH/RDP över IoT Hub enhetsströmmar med C#-proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP över IoT Hub enhetsströmmar med NodeJS-proxy](./quickstart-device-streams-proxy-nodejs.md).

![Installation av lokal proxy](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Den tjänst lokala proxyn ansluter till IoT-hubben och startar en enhetsström till målenheten.

2. Den enhets lokala proxyn slutför handskakningen för ströminitiering och upprättar en strömningstunnel från slutpunkt till slutpunkt via IoT-hubbens slutpunkt för direktuppspelning till tjänstsidan.

3. Den enhets lokala proxyn ansluter till den SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhets lokala proxyprogrammet".

4. Den tjänst-lokala proxyn väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i det här fallet är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhets lokala proxyprogrammet". När användaren ansluter via SSH-klienten gör tunneln att SSH-programtrafik kan överföras mellan SSH-klienten och serverprogrammen.

> [!NOTE]
> SSH-trafik som skickas via en enhetsström skickas via tunneltrafik via IoT-hubbens slutpunkt för direktuppspelning i stället för att skickas direkt mellan tjänsten och enheten. Mer information finns i fördelarna med [att använda Iot Hub-enhetsströmmar.](iot-hub-device-streams-overview.md#benefits) Bilden visar dessutom den SSH-daemon som körs på samma enhet (eller dator) som den enhets lokala proxyn. I den här snabbstarten, förutsatt att IP-adressen för SSH-daemon finns, kan även den enhets lokala proxyn och daemonen köras på olika datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten använder du [SDK för Azure IoT-enheter för C.](iot-hub-device-sdk-c-intro.md) Du förbereder en utvecklingsmiljö som används för att klona och [skapa Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:t på GitHub innehåller exempelkoden som används i den här snabbstarten.

1. Ladda ned [CMake-byggsystemet](https://cmake.org/download/).

    Det är viktigt att Visual Studio (Visual Studio och skrivbordsutveckling med *C++-arbetsbelastning)* är installerade på datorn *innan* du startar CMake-installationen. När förutsättningarna är uppfyllda och nedladdningen har verifierats kan du installera CMake-byggsystemet.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona [GitHub-lagringsplatsen för Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden bör ta några minuter.

1. Skapa en *cmake-underkatalog* i rotkatalogen för git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från katalogen *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon från *cmake-katalogen* för att skapa en version av SDK:n som är specifik för din utvecklingsklientplattform.

   * I Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Kör följande kommandon i Kommandotolken för utvecklare i Windows för Visual Studio 2015 eller 2017. En Visual Studio lösning för den simulerade enheten genereras i *cmake-katalogen.*

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

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget för](/cli/azure/ext/azure-iot/iot) att registrera en simulerad enhet.

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

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en ström från början till slut för att tunneltrafik i SSH.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

1. Redigera källfilen **iothub_client_c2d_streaming_proxy_sample.c** i mappen och ange enhetens anslutningssträng, `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` målenhetens IP/värdnamn och SSH-port 22:

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

Som beskrivs i avsnittet "Så här fungerar det" kräver upprättandet av en ström från början till slut för tunneltrafik för SSH en lokal proxy i varje ände (på både tjänsten och enhetens sidor). Under den offentliga förhandsversionen IoT Hub C SDK endast stöd för enhetsströmmar på enhetssidan. Om du vill skapa och köra den tjänst-lokala proxyn följer du anvisningarna i någon av följande snabbstarter:

   * [SSH/RDP över IoT Hub med C#-proxyappar](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP över IoT Hub enhetsströmmar med hjälp Node.js-proxyappar](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Upprätta en SSH-session

När både enhets- och tjänst lokala proxyservrar körs använder du SSH-klientprogrammet och ansluter till den tjänst lokala proxyn på port 2222 (i stället för SSH-daemon direkt).

```cmd/sh
ssh {username}@localhost -p 2222
```

Nu uppmanas du att ange dina autentiseringsuppgifter i SSH-inloggningsfönstret.

Följande bild visar konsolens utdata på den enhets lokala proxyn, som ansluter till SSH-daemonen på `IP_address:22` :

![Utdata från enhets lokal proxy](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Följande bild visar konsolens utdata för SSH-klientprogrammet. SSH-klienten kommunicerar med SSH-daemonen genom att ansluta till port 22, som den tjänst-lokala proxyn lyssnar på:

![SSH-klientutdata](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerade du en IoT-hubb, registrerade en enhet, distribuerade ett enhets- och ett tjänst lokalt proxyprogram för att upprätta en enhetsström via IoT Hub och använde proxyservrarna för att tunnel-SSH-trafik.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
