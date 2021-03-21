---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8d3f35a733a0f78fabc33df857d911ba3cd222f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244614"
---
IoT Plug and Play fören klar IoT genom att göra det möjligt att interagera med enhetens funktioner utan att du behöver ha kunskap om den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Java för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten på Windows installerar du följande program vara i din lokala Windows-miljö:

* Java SE Development Kit 8. I [Java långsiktigt stöd för Azure och Azure Stack](/java/azure/jdk/), under **långsiktig support**, väljer du **Java 8**.
* [Apache maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klona SDK-lagringsplatsen med exempel koden

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md)har du redan klonat lagrings platsen.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Microsoft Azure IoT SDK: er för Java](https://github.com/Azure/azure-iot-sdk-java) GitHub-lagringsplatsen på den här platsen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Skapa och kör exempel enheten

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

I den här snabb starten använder du en exempel termostat-enhet som är skriven i Java som IoT Plug and Play-enhet. Så här kör du exempel enheten:

1. Öppna ett terminalfönster och navigera till den lokala mappen som innehåller Microsoft Azure IoT SDK för Java-lagringsplats som du har klonat från GitHub.

1. Det här terminalfönstret används som **enhetens** Terminal. Gå till rotmappen för den klonade lagrings platsen. Installera alla beroenden genom att köra följande kommando:

1. Kör följande kommando för att skapa exempel enhets programmet:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Om du vill köra programmet för enhets exempel navigerar du till mappen *device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* och kör följande kommando:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exempel enheten körs när du slutför nästa steg.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I [Konfigurera din miljö för iot plug and Play snabb starter och självstudier](../articles/iot-pnp/set-up-environment.md) som du har skapat två miljövariabler för att konfigurera exemplet för att ansluta till din IoT-hubb och-enhet:

* **IOTHUB_CONNECTION_STRING**: den IoT Hub-anslutningssträng som du antecknade tidigare.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

I den här snabb starten använder du en exempel IoT-lösning som skrivits i Java för att interagera med den exempel enhet som du nyss konfigurerade.

> [!NOTE]
> I det här exemplet används namn området **com. Microsoft. Azure. SDK. IoT. service** från **IoT Hub-tjänst klienten**. Mer information om API: er, inklusive digitala dubbla API: er, finns i [service Developer-guiden](../articles/iot-pnp/concepts-developer-guide-service.md).

1. Öppna ett annat terminalfönster som ska användas som **tjänstens** Terminal.

1. I den klonade Java SDK-lagringsplatsen navigerar du till mappen *service\iot-service-samples\pnp-service-sample\thermostat-service-Sample*

1. Kör exempel tjänst programmet genom att köra följande kommando:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Hämta enhetens dubbla

Följande kodfragment visar hur du hämtar enheten i tjänsten:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Uppdatera en enhet med dubbla

Följande kodfragment visar hur du använder en *korrigering* för att uppdatera egenskaper via enheten:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

Enhetens utdata visar hur enheten svarar på den här egenskaps uppdateringen.

### <a name="invoke-a-command"></a>Anropa ett kommando

Följande kodfragment visar hur du anropar ett kommando på enheten:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

Enhetens utdata visar hur enheten svarar på det här kommandot.
