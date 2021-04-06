---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613029"
---
Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet skrivs i C och ingår i Azure IoT-enhetens SDK för C. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Du kan köra den här snabb starten på Linux eller Windows. Shell-kommandona i den här snabb starten följer Linux-konventionen för Sök vägs avgränsare ' `/` ', om du följer med i Windows måste du byta avgränsare för ' `\` '.

Kraven varierar beroende på operativ system:

### <a name="linux"></a>Linux

I den här snabb starten förutsätter vi att du använder Ubuntu Linux. Stegen i den här snabb starten har testats med Ubuntu 18,04.

För att slutföra den här snabb starten på Linux installerar du följande program vara i din lokala Linux-miljö:

Installera **gcc**, **git**, **cmake** och alla nödvändiga beroenden med `apt-get` kommandot:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

För att slutföra den här snabb starten på Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

## <a name="download-the-code"></a>Ladda ned koden

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen på den här platsen:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Det kan ta flera minuter att slutföra den här åtgärden.

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden:

1. Skapa en _cmake_ under katalog i rotmappen för enhets-SDK: n och navigera till mappen:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa SDK och exempel:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> I Windows kan du öppna lösningen som genereras av `cmake` kommandot i Visual Studio 2019. Öppna projekt filen *azure_iot_sdks. SLN* i katalogen _cmake_ och ange **pnp_simple_thermostat** projektet som start projekt i lösningen. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Köra exempel programmet i SDK som simulerar en IoT Plug and Play-enhet som skickar telemetri till din IoT-hubb:

Från mappen _cmake_ navigerar du till mappen som innehåller den körbara filen och kör den:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Om du vill spåra kod körningen i Visual Studio på Windows lägger du till en Bryt punkt i `main` funktionen i filen _pnp_simple_thermostat. c_ .

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](../articles/iot-pnp/concepts-modeling-guide.md). [DTDL-modell filen för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden använder standard-funktionen för att ansluta till din IoT-hubb:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Den kod som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventioner.

I koden används Parson-biblioteket för att parsa JSON-objekt i de nytto laster som skickas från din IoT Hub:

```c
// JSON parser
#include "parson.h"
```
