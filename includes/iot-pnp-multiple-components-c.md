---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613005"
---
I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i C och ingår i Azure IoT-enhetens SDK för C. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här kursen får du:

> [!div class="checklist"]
> * Hämta exempel koden.
> * Bygg exempel koden.
> * Kör enhets exempel programmet och kontrol lera att det ansluter till din IoT-hubb.
> * Granska käll koden.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Du kan slutföra den här kursen på Linux eller Windows. Shell-kommandona i den här självstudien följer Linux-konventionen för avgränsare " `/` ", om du följer med i Windows måste du byta avgränsare för " `\` ".

Kraven varierar beroende på operativ system:

### <a name="linux"></a>Linux

I den här självstudien förutsätter vi att du använder Ubuntu Linux. Stegen i den här självstudien har testats med Ubuntu 18,04.

Om du vill slutföra den här självstudien på Linux installerar du följande program vara i din lokala Linux-miljö:

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

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

## <a name="download-the-code"></a>Ladda ned koden

Om du har slutfört [snabb starten: Anslut ett exempel på IoT plug and Play Device-program som körs på Linux eller Windows till IoT Hub (C)](../articles/iot-pnp/quickstart-connect-device.md) du redan har laddat ned koden.

I den här självstudien förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C SDK.

Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [Azure IoT C SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen på den här platsen:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du kan skapa och köra koden med Visual Studio eller `cmake` på kommando raden.

### <a name="use-visual-studio"></a>Använda Visual Studio

1. Öppna rotmappen för den klonade lagrings platsen. Efter några sekunder skapar **cmake** -stödet i Visual Studio allt du behöver för att köra och felsöka projektet.
1. När Visual Studio är klar går du till **Solution Explorer** och navigerar till exempel *iothub_client/samples/PnP/pnp_temperature_controller/*.
1. Högerklicka på filen *pnp_temperature_controller. c* och välj **Lägg till fel söknings konfiguration**. Välj **standard**.
1. Denlaunch.vs.jsfilen öppnas *i* Visual Studio. Redigera den här filen som du ser i följande kodfragment för att ange nödvändiga miljövariabler. Du har antecknat omfångs-ID och registrerings primär nyckel när du har [konfigurerat din miljö för IoT plug and Play snabb starter och självstudier](../articles/iot-pnp/set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Högerklicka på filen *pnp_temperature_controller. c* och välj **Ange som start objekt**.
1. Om du vill spåra kod körningen i Visual Studio lägger du till en Bryt punkt i `main` funktionen i filen *pnp_temperature_controller. c* .
1. Nu kan du köra och felsöka exemplet från **fel söknings** menyn.

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

### <a name="use-cmake-at-the-command-line"></a>Använd `cmake` på kommando raden

Så här skapar du exemplet:

1. Skapa en _cmake_ -undermapp i rotmappen för den klonade enhetens SDK och navigera till mappen:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att generera och bygga projektfiler för SDK och exempel:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Så här kör du exemplet:

1. Från mappen _cmake_ navigerar du till mappen som innehåller den körbara filen och kör den:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Det här exemplet implementerar en modell med [flera komponenter](../articles/iot-pnp/concepts-modeling-guide.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

### <a name="iot-plug-and-play-helper-functions"></a>Hjälp funktioner för IoT Plug and Play

I det här exemplet använder koden vissa hjälp funktioner från mappen */vanliga* :

*pnp_device_client_ll* innehåller metoden Connect för IoT-plug and Play med `model-id` inkluderad som parameter: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol*: innehåller IoT plug and Play Helper-funktioner:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Dessa hjälp funktioner är allmänt tillräckliga för att användas i ditt eget projekt. I det här exemplet används de tre filer som motsvarar varje komponent i modellen:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

I *pnp_deviceinfo_component* -filen `SendReportedPropertyForDeviceInformation` använder funktionen exempelvis två hjälp funktioner:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Varje komponent i exemplet följer det här mönstret.

### <a name="code-flow"></a>Kod flöde

`main`Funktionen initierar anslutningen och skickar modell-ID:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Koden använder `PnP_CreateDeviceClientLLHandle` för att ansluta till IoT Hub, anges `modelId` som ett alternativ och konfigurera enhets metoden och enhetens dubbla återanrops hanterare för direkta metoder och enhets dubbla uppdateringar:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` innehåller även anslutnings informationen. Miljövariabeln **IOTHUB_DEVICE_SECURITY_TYPE** bestämmer om exemplet använder en anslutnings sträng eller enhets etablerings tjänsten för att ansluta till IoT Hub.

När enheten skickar ett modell-ID blir den en IoT Plug and Play-enhet.

Med återanrops hanterare på plats reagerar enheten på dubbla uppdateringar och direkta metod anrop:

* För enhetens dubbla motringning `PnP_TempControlComponent_DeviceTwinCallback` anropar `PnP_ProcessTwinData` funktionen för att bearbeta data. `PnP_ProcessTwinData` använder *besöks mönster* för att parsa JSON och sedan besöka varje egenskap och anropar `PnP_TempControlComponent_ApplicationPropertyCallback` varje element.

* För kommandona motringning `PnP_TempControlComponent_DeviceMethodCallback` använder funktionen hjälp funktionen för att parsa kommando-och komponent namn:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    `PnP_TempControlComponent_DeviceMethodCallback`Funktionen anropar sedan kommandot på komponenten:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main`Funktionen initierar skrivskyddade egenskaper som skickas till IoT Hub:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main`Funktionen anger en slinga för att uppdatera händelse-och telemetridata för varje komponent:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry`Funktionen visar hur du använder `PNP_THERMOSTAT_COMPONENT` struct. Exemplet använder den här structen för att lagra information om de två termostater i temperatur styrenheten. Koden använder `PnP_CreateTelemetryMessageHandle` funktionen för att förbereda meddelandet och skicka det:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main`Funktionen förstör slutligen de olika komponenterna och stänger anslutningen till hubben.
