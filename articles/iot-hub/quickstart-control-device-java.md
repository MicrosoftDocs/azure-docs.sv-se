---
title: 'Snabb start: styra en enhet från Azure IoT Hub med Java'
description: I den här snabbstarten kör du två Java-exempelprogram. Ett program är en serverdelsprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb och som kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 33952f34170b84650b39fceae0953b3bcdd12c51
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059790"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Snabb start: styra en enhet som är ansluten till en Azure IoT Hub med Java

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

I den här snabb starten använder du en direkt metod för att styra en simulerad enhet som är ansluten till Azure IoT Hub med ett Java-program. IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhets telemetri till molnet för lagring eller bearbetning. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub. I den här snabb starten används två Java-program: ett simulerat enhets program som svarar på direkta metoder som anropas från ett Server dels program och ett tjänst program som anropar den direkta metoden på den simulerade enheten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. I [Java långsiktigt stöd för Azure och Azure Stack](/java/azure/jdk/), under **långsiktig support**, väljer du **Java 8**.

    Du kan kontrollera den aktuella versionen av Java på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    java -version
    ```

* [Apache maven 3](https://maven.apache.org/download.cgi).

    Du kan kontrollera den aktuella versionen av Maven på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    mvn --version
    ```

* [Ett exempel på ett Java-projekt](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* Port 8883 öppna i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyJavaDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyJavaDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyJavaDevice \
      --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

**YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänst anslutnings strängen skiljer sig från den enhets anslutnings sträng som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört den direkta metoden.

1. Navigera till Java-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device-2**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** i en textredigerare som du väljer.

    Ersätt värdet för `connString` variabeln med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice. java**.

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa programmet för simulerad enhet:

    ```cmd/sh
    mvn clean package
    ```

4. I det lokala terminalfönstret kör du följande kommandon för att köra programmet för simulerad enhet:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Utdata från telemetri som skickas av enheten till din IoT-hubb](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Serverdelsprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metod anrop till en enhet via din IoT Hub och lyssnar efter bekräftelser. Ett IoT Hub-serverdelsprogram körs normalt i molnet.

1. Navigera till Java-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **Quickstarts\back-end-application**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** i en textredigerare som du väljer.

    Ersätt värdet för `iotHubConnectionString` variabeln med tjänst anslutnings strängen som du gjorde en anteckning om tidigare. Spara sedan ändringarna i **BackEndApplication. java**.

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa serverdelsprogrammet:

    ```cmd/sh
    mvn clean package
    ```

4. I det lokala terminalfönstret kör du följande kommandon för att köra serverdelsprogrammet:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Följande skärm bild visar resultatet när programmet gör ett direkt metod anrop till enheten och får en bekräftelse:

    ![Utdata som programmet gör ett direkt metod anrop via din IoT Hub](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    När du har kört serverdelsprogrammet visas ett meddelande i det konsolfönster som kör den simulerade enheten och hastigheten med vilken den skickar meddelandeändringar:

    ![Konsol meddelande från enhet visar hur ofta den ändras](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
