---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 32a2ef82888e61c18c298c61c9b7ad268e7db797
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244666"
---
I den här självstudien får du lära dig hur du skapar ett Plug and Play enhets program för flera komponenter, ansluter det till din IoT-hubb och använder Azure CLI för att Visa telemetri som skickas. Exempel programmet är skrivet i Java och ingår i Azure IoT-enhetens SDK för Java. Ett Solution Builder kan använda Azure CLI för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet i Java och ingår i Azure IoT-enhetens SDK för Java. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här kursen får du:

> [!div class="checklist"]
> * Hämta exempel koden.
> * Bygg exempel koden.
> * Kör enhets exempel programmet och kontrol lera att det ansluter till din IoT-hubb.
> * Granska käll koden.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* Java SE Development Kit 8. I [Java långsiktigt stöd för Azure och Azure Stack](/java/azure/jdk/), under **långsiktig support**, väljer du **Java 8**.
* [Apache maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Ladda ned koden

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md)har du redan klonat lagrings platsen.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT Java-SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-java) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Det kan ta flera minuter att slutföra den här åtgärden.

## <a name="build-the-code"></a>Skapa koden

I Windows navigerar du till rotmappen för den klonade Java SDK-lagringsplatsen. Kör följande kommando för att skapa beroenden:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Kör enhets exemplet

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Om du vill köra exempel programmet navigerar du till mappen *\device\iot-Device-samples\pnp-Device-sample\temperature-Controller-Device-Sample* och kör följande kommando:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](../articles/iot-pnp/concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

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

När enheten ansluter till din IoT-hubb registreras kommando hanterare i koden.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Det finns separata hanterare för önskade egenskaps uppdateringar för de två termostat-komponenterna:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Exempel koden skickar telemetri från varje termostat-komponent:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading`Metoden använder `PnpHhelper` klassen för att skapa meddelanden för varje komponent:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper`Klassen innehåller andra exempel metoder som du kan använda med en modell för flera komponenter.

Använd Azure IoT Explorer-verktyget för att Visa telemetri och egenskaper från de två termostat-komponenterna:

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Enhet för flera komponenter i Azure IoT Explorer":::

Du kan också använda Azure IoT Explorer-verktyget för att anropa kommandon i någon av de två termostat-komponenterna eller i standard komponenten.
