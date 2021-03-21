---
title: Snabb start – skicka telemetri till Azure IoT Hub snabb start (C) | Microsoft Docs
description: I den här snabbstarten kör du två C-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: aad28c704ba8c46f4c2d0111e545dc4d2381250d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201948"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Snabb start: skicka telemetri från en enhet till en IoT-hubb och Läs den med ett Server dels program (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder ett C-exempelprogram från [SDK för Azure IoT-enheter så att C](iot-hub-device-sdk-c-intro.md) kan skicka telemetri till en IoT-hubb. SDK:n för Azure IoT-enheter är skrivna i [ANSI C (C99)](https://wikipedia.org/wiki/C99) för portabilitet och bred plattformskompatibilitet. Innan du kör exempelkoden skapar du en IoT-hubb och registrerar den simulerade enheten med den hubben.

Den här artikeln är skriven för Windows, men du kan även slutföra den här snabb starten på Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med arbets belastningen ["Skriv bords utveckling med C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) aktive rad.

* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten använder du [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). 

I följande miljöer kan du använda SDK: n genom att installera dessa paket och bibliotek:

* **Linux**: apt – Hämta paket är tillgängliga för Ubuntu 16,04 och 18,04 med följande CPU-arkitekturer: amd64, arm64, armhf och i386. Mer information finns i [Använda apt-get för att skapa ett klientprojekt för C-enheten i Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **Mbed**: för utvecklare som skapar enhets program på Mbed-plattformen har vi publicerat ett bibliotek och exempel som hjälper dig att komma igång på några minuter med Azure IoT Hub. Mer information finns i [Använda mbed-biblioteket](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: om du utvecklar på Arduino kan du utnyttja Azure IoT-biblioteket som finns i Arduino IDE Library Manager. Mer information finns i [Azure IoT Hub-biblioteket för Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: SDK:n för IoT Hub-enheter är tillgänglig som CocoaPods för Mac- och iOS-enhetsutveckling. Mer information finns i [iOS-exempel för Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

I den här snabb starten ska du dock förbereda en utvecklings miljö som används för att klona och bygga [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:n på GitHub inkluderar den exempelkod som används i den här snabbstarten.

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Hitta taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som `-b` parameter värde:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kör följande kommando för att skapa en version av SDK som är specifika för din utvecklings klient plattform. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen.

    ```cmd
    cmake ..
    ```

    Om `cmake` du inte hittar din C++-kompilator kan du få build-fel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet ska du använda Azure Cloud Shell med IoT- [tillägget](/cli/azure/ext/azure-iot/iot) för att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyCDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyCDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar en sträng som simulerad telemetri.

1. Använd en textredigerare och öppna källfilen iothub_convenience_sample.c och granska exempelkoden för att skicka telemetri. Filen finns på följande plats under arbets katalogen där du klonade Azure IoT C SDK:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Hitta deklarationen för konstanten `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Ersätt värdet för `connectionString` konstanten med enhets anslutnings strängen som du antecknade tidigare. Spara dina ändringar i **iothub_convenience_sample.c**.

3. I ett lokalt terminalfönster navigerar du till projektkatalogen *iothub_convenience_sample* i CMake-katalogen som du skapade i Azure IoT C SDK. Ange följande kommando från din arbets katalog:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Kör CMake i ditt lokala terminalfönster för att skapa exemplet med det uppdaterade `connectionString`-värdet:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Kör följande kommando i det lokala terminalfönstret för att köra det simulerade enhets programmet:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till IoT-hubben:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

I det här avsnittet ska du använda Azure Cloud Shell med IoT- [tillägget](/cli/azure/ext/azure-iot/iot) för att övervaka enhets meddelanden som skickas av den simulerade enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med ett C-program och läser telemetri från hubben med hjälp av Azure Cloud Shell.

Om du vill lära dig mer om hur du utvecklar med Azure IoT Hub C SDK kan du fortsätta till följande instruktioner:

> [!div class="nextstepaction"]
> [Utveckla med hjälp av Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)
