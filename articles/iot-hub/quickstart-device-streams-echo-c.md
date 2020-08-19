---
title: Kommunicera med enhetens app i C med Azure IoT Hub enhets strömmar
description: I den här snabb starten kör du ett C-program på enhets sidan som kommunicerar med en IoT-enhet via en enhets ström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 7b5487233e2bd0e532efad4e108c41f188b51d91
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "83727186"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabb start: kommunicera med ett enhets program i C via IoT Hub enhets strömmar (för hands version)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Under den offentliga för hands versionen stöder C SDK endast enhets strömmar på enhets sidan. Därför täcker den här snabb starten instruktioner för att endast köra program på enhets sidan. Information om hur du kör ett motsvarande program på tjänst sidan finns i följande artiklar:

* [Kommunicera med enhetens appar i C# via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md)

* [Kommunicera med enhets program i Node.js via IoT Hub enhets strömmar](./quickstart-device-streams-echo-nodejs.md)

C-programmet på enhetssidan i den här snabbstarten har följande funktioner:

* Upprätta en enhetsström till en IoT-enhet.

* Ta emot data som skickas från programmet på tjänst sidan och ECHO tillbaka.

Koden visar initierings processen för en enhets ström, samt hur du kan använda den för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande krav:

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med **Desktop-utveckling med** arbets belastningen C++ aktiverat.

* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI till din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT-kommandon (Device Provisioning service) i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapas i följande regioner:

  * Central US
  * Centrala USA-EUAP
  * Norra Europa
  * Sydostasien

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten använder du [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Du förbereder en utvecklings miljö som används för att klona och bygga [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK: n för GitHub innehåller exempel koden som används i den här snabb starten.

   > [!NOTE]
   > Innan du påbörjar den här proceduren måste du vara säker på att Visual Studio installeras med **Desktop-utveckling med** arbets belastningen C++.

1. Installera [cmake build-systemet](https://cmake.org/download/) enligt beskrivningen på hämtnings sidan.

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

   * Öppna en [kommando tolk för utvecklare i Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs)i Windows. Kör kommandot för din version av Visual Studio. Den här snabb starten använder Visual Studio 2019. Dessa kommandon skapar en Visual Studio-lösning för den simulerade enheten i *cmake* -katalogen.

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

Du måste registrera en enhet med IoT Hub innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) för att registrera en simulerad enhet.

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
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhets anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enhets strömmar

I det här avsnittet ska du köra både program på enhets sidan och programmet på tjänst sidan och kommunicera mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Följ dessa steg om du vill köra programmet på enhets sidan:

1. Ange autentiseringsuppgifter för enheten genom att redigera käll filen **iothub_client_c2d_streaming_sample. c** i `iothub_client/samples/iothub_client_c2d_streaming_sample` mappen och lägga till enhets anslutnings strängen.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Kompilera koden med följande kommandon:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Kör det kompilerade programmet:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Som tidigare nämnts stöder IoT Hub C SDK endast enhets strömmar på enhets sidan. Följ anvisningarna i något av följande snabb starter för att skapa och köra det medföljande programmet på tjänst sidan:

* [Kommunicera med en enhets app i C# via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md)

* [Kommunicera med en enhets app i Node.js via IoT Hub enhets strömmar](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, upprättat en enhets ström mellan ett C-program på enheten och ett annat program på tjänst sidan, och använde data strömmen för att skicka data fram och tillbaka mellan programmen.

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
