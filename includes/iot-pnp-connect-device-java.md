---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b4913b510dabd0fc2c8456d4c199c6b11569597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611835"
---
Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet i Java och ingår i Azure IoT-enhetens SDK för Java. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten på Windows installerar du följande program vara i din lokala Windows-miljö:

* Java SE Development Kit 8. I [Java långsiktigt stöd för Azure och Azure Stack](/java/azure/jdk/), under **långsiktig support**, väljer du **Java 8**.
* [Apache maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Ladda ned koden

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device Java SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT Java-SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-java) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Skapa koden

I Windows navigerar du till rotmappen för den klonade Java SDK-lagringsplatsen.

Kör följande kommando för att skapa exempel programmet:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Kör enhets exemplet

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Navigera till mappen *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample*

Kör exempel programmet genom att köra följande kommando:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](../articles/iot-pnp/concepts-modeling-guide.md). [DTDL-modell filen för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden använder standard- `DeviceClient` klassen för att ansluta till din IoT-hubb. Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Modell-ID: t lagras i koden som visas i följande kodfragment:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Den kod som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventioner.

Exemplet använder ett JSON-bibliotek för att parsa JSON-objekt i de nytto laster som skickas från din IoT Hub:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
