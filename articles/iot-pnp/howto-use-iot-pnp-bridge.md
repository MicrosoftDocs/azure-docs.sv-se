---
title: Så här ansluter du ett IoT Plug and Play Bridge-exempel som körs på Linux eller Windows till en IoT-hubb | Microsoft Docs
description: Skapa och kör IoT Plug and Play Bridge på Linux eller Windows som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834110"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Så här ansluter du ett IoT Plug and Play Bridge-exempel som körs på Linux eller Windows till IoT Hub

Den här artikeln visar hur du skapar IoT Plug and Play Bridges exempel miljö kort, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. IoT Plug and Play-bryggan är skriven i C och innehåller Azure IoT-enhetens SDK för C. I slutet av den här själv studie kursen ska du kunna köra IoT Plug and Play-bryggan och se den rapportera telemetri i Azure IoT Explorer:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="En skärm bild som visar Azure IoT Explorer med en tabell med rapporterade telemetri (fuktighet, temperatur) från IoT Plug and Play Bridge.":::

## <a name="prerequisites"></a>Förutsättningar

Du kan köra exemplet i artikeln i Windows eller Linux. Shell-kommandona i den här instruktions guiden följer Windows-konventionen för Sök vägs avgränsare `\` , om du följer med i Linux, se till att byta avgränsare för `/` .

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här artikeln använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _IoT Hub anslutnings sträng_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här artikeln:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här artikeln:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Hämta och köra bryggan

I den här artikeln har du två alternativ för att köra bryggan. Du kan:

- Hämta en inbyggd körbar fil och kör den enligt beskrivningen i det här avsnittet.
- Ladda ned käll koden och [skapa och kör sedan bryggan](#build-and-run-the-bridge) enligt beskrivningen i följande avsnitt.

Hämta och köra bryggan:

1. Gå till sidan IoT Plug and Play- [versioner](https://github.com/Azure/iot-plug-and-play-bridge/releases).
1. Ladda ned den färdiga körbara filen för operativ systemet: **pnpbridge_bin.exe** för Windows eller **pnpbridge_bin** för Linux.
1. Hämta exemplet [config.jsi](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) konfigurations filen för miljö sensor exemplet. Kontrol lera att konfigurations filen finns i samma mapp som den körbara filen.
1. Redigera *config.jspå* filen:

    - Lägg till `connection-string` värdet som är _enhets anslutnings strängen_ som du antecknade tidigare.
    - Lägg till `symmetric_key` värdet som är nyckel värde för delad åtkomst från _enhets anslutnings strängen_.
    - Ersätt `root_interface_model_id` värdet med `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    Det första avsnittet av *config.jspå* filen ser nu ut som i följande kodfragment:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Kör den körbara filen i kommando rads miljön. Bridge genererar utdata som ser ut så här:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Skapa och köra bryggan

Om du föredrar att bygga den körbara filen själv kan du ladda ned käll koden och bygga skript.

Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [IoT plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

När du har klonat lagrings platsen uppdaterar du undermodulerna. Undermodulerna innehåller Azure IoT SDK för C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Det kan ta flera minuter att slutföra den här åtgärden.

> [!TIP]
> Om du stöter på problem med uppdatering av den git-klonade undermodulen, är detta ett känt problem med Windows fil Sök vägar. Du kan köra följande kommando för att lösa problemet: `git config --system core.longpaths true`

Kraven för att bygga bryggan skiljer sig mellan olika operativ system:

### <a name="windows"></a>Windows

Om du vill skapa IoT Plug and Play Bridge på Windows installerar du följande program vara:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="linux"></a>Linux

I den här artikeln förutsätter vi att du använder Ubuntu Linux. Stegen i den här artikeln har testats med Ubuntu 18,04.

Om du vill skapa IoT Plug and Play Bridge på Linux installerar du **gcc**, **git**, **cmake** och alla nödvändiga beroenden med `apt-get` kommandot:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Skapa IoT Plug and Play-bryggan

Navigera till mappen *pnpbridge* i katalogen för lagrings platsen.

För Windows kör du följande i en [kommando tolk för utvecklare för Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

På samma sätt för Linux kör du följande:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>I Windows kan du öppna lösningen som genererats av cmake-kommandot i Visual Studio 2019. Öppna projekt filen *azure_iot_pnp_bridge. SLN* i katalogen cmake och ange *pnpbridge_bin* projektet som start projekt i lösningen. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

### <a name="edit-the-configuration-file"></a>Redigera konfigurations filen

Du kan lära dig mer om config-filer i [IoT plug and Play Bridge Concepts-dokument](concepts-iot-pnp-bridge.md).

Öppna *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.js* filen i en text redigerare.

- Lägg till `connection-string` värdet som är _enhets anslutnings strängen_ som du antecknade tidigare.
- Lägg till `symmetric_key` värdet som är nyckel värde för delad åtkomst från _enhets anslutnings strängen_.
- Ersätt `root_interface_model_id` värdet med `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

Det första avsnittet av *config.jspå* filen ser nu ut som i följande kodfragment:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Kör IoT Plug and Play-bryggan

Starta exemplet på IoT Plug and Play Bridge-miljö sensorn. Parametern är sökvägen till `config.json` filen som du redigerade i föregående avsnitt:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Bridge genererar utdata som ser ut så här:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Använd följande kommandon för att köra bryggan på Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Ladda ned modell filerna

Du kan använda Azure IoT Explorer senare för att visa enheten när den ansluter till din IoT-hubb. Azure IoT Explorer behöver en lokal kopia av modell filen som matchar det **modell-ID** som din enhet skickar. Med modell filen kan IoT Explorer Visa telemetri, egenskaper och kommandon som enheten implementerar.

Hämta modeller för Azure IoT Explorer:

1. Skapa en mapp med namnet *modeller* på den lokala datorn.
1. Spara [EnvironmentalSensor.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) till mappen *modeller* som du skapade i föregående steg.
1. Om du öppnar den här modell filen i en text redigerare kan du se att modellen definierar en komponent med `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` ID: t. Detta är samma modell-ID som du använde i *config.js* i filen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När bryggan har startat använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar. Du kan se telemetri, egenskaper och kommandon som definierats i `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modellen.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

* [Vad är IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Skapa, distribuera och utöka IoT Plug and Play Bridge](howto-build-deploy-extend-pnp-bridge.md)
* [IoT Plug and Play-brygga på GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
