---
title: Snabbstart – Kontrollera en enhet från Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du två Java-exempelprogram. Det enda programmet är ett tjänstprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet körs på en fysisk eller simulerad enhet som är ansluten till hubben och kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5946220a688206a35d0d68cd4efd4c356d27d7ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868554"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

I den här snabbstarten använder du en direktmetod för att styra en simulerad enhet som är ansluten till Azure IoT Hub. IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhetste telemetri till molnet för lagring eller bearbetning. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub. Den här snabbstarten använder två program: ett simulerat enhetsprogram som svarar på direkta metoder som anropas från ett program för backend-tjänsten och ett tjänstprogram som anropar direktmetoden på Android-enheten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio med Android SDK 27](https://developer.android.com/studio/). Mer information finns i [Installera Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Android-exempelprogrammet för enhets-SDK,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)som [ingår i Azure IoT-exempel (Java).](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Service SDK-exempel på Android-program](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), som ingår i Azure IoT-exempel (Java).

* Port 8883 är öppen i brandväggen. Enhetsexempel i den här snabbstarten använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda den IoT-hubb som du redan har skapat.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda samma enhet som registrerats i föregående snabbstart.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice:** Det här är namnet på den enhet som du registrerar. Vi rekommenderar att du använder **MyAndroidDevice** så som visas. Om du väljer ett annat namn för din enhet måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du behöver även ha en _tjänstanslutningssträng_ för att kunna aktivera tjänstprogrammet för serverdelen och ansluta till din IoT-hubb för att köra metoder och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

**YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Båda exemplen för den här snabbstarten är en del av lagringsplatsen azure-iot-samples-java på GitHub. Ladda ned eller klona lagringsplatsen [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

Exempelprogrammet för enhets-SDK kan köras på en fysisk Android-enhet eller en Android-emulator. Exemplet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört direktmetoden.

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog i den klonade eller nedladdade kopian av [lagringsplatsen azure-iot-sample-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. I Android Studio du *gradle.properties* för exempelprojektet och **ersätter platshållaren Device_Connection_String** med enhetsanslutningssträngen som du antecknade tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du på **File**  >  Sync Project with Gradle Files (Filsynkroniseringsprojekt **med Gradle Files).** Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om projektsynkronisering misslyckas kan det ha någon av följande orsaker:
   >
   > * Versionerna av Android Gradle-plugin-programmet och Gradle som refereras till i projektet är inaktuella för din version av Android Studio. Följ [de här anvisningarna](https://developer.android.com/studio/releases/gradle-plugin) för att referera till och installera rätt versioner av plugin-programmet och Gradle för installationen.
   > * Licensavtalet för Android SDK har inte signerats. Följ instruktionerna i Build-utdata för att signera licensavtalet och ladda ned SDK:n.

4. När bygget är klart klickar du **på Kör**  >  **kör "app".** Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Exempel på skärmbild av Android-appen för klientenhet](media/quickstart-control-device-android/sample-screenshot.png)

Den här appen måste vara igång på en fysisk enhet eller emulator medan du kör sdk-exemplet för tjänsten för att uppdatera telemetriintervallet under körningen.

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget för](/cli/azure/iot) att övervaka meddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-control-device-android/read-data.png)

Som standard skickar telemetriappen telemetri från Android-enheten var femte sekund. I nästa avsnitt använder du ett direktmetodsanrop för att uppdatera telemetriintervallet för Android IoT-enheten.

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser.

Kör den här appen på en separat fysisk Android-enhet eller en Android-emulator.

Ett IoT Hub-tjänstprogram körs vanligtvis i molnet, där det är enklare att minska riskerna med den känsliga anslutningssträngen som styr alla enheter på en IoT Hub. I det här exemplet kör vi det som en Android-app endast i demonstrationssyfte. De andra språkversionerna av den här snabbstarten innehåller exempel som är mer anpassade till ett typiskt program för backend-tjänster.

1. Öppna Android-tjänstexempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog i den klonade eller nedladdade kopian av [lagringsplatsen azure-iot-sample-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample*.

2. I Android Studio du *gradle.properties* för exempelprojektet. Uppdatera värdena för egenskaperna **ConnectionString och** **DeviceId** med tjänstanslutningssträngen som du antecknade tidigare och Android-enhets-ID:t som du registrerade.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. I Android Studio klickar du på **File**  >  Sync Project with Gradle Files (Filsynkroniseringsprojekt **med Gradle-filer).** Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om projektsynkronisering misslyckas kan det ha någon av följande orsaker:
   >
   > * Versionerna av Android Gradle-plugin-programmet och Gradle som refereras till i projektet är inaktuella för din version Android Studio. Följ [de här instruktionerna](https://developer.android.com/studio/releases/gradle-plugin) för att referera till och installera rätt versioner av plugin-programmet och Gradle för installationen.
   > * Licensavtalet för Android SDK har inte signerats. Följ anvisningarna i Build-utdata för att signera licensavtalet och ladda ned SDK:n.

4. När bygget är klart klickar du **på Kör**  >  **kör "app".** Konfigurera appen att köras på en separat fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in uppdaterar du värdet **Set Messaging Interval** (Ange meddelandeintervall) till **1000** och klickar på **Invoke** (Anropa).

    Meddelandeintervallet för telemetri anges i millisekunder. Standardintervallet för telemetri för enhetsexemplet är inställt på 5 sekunder. Den här ändringen uppdaterar Android IoT-enheten så att telemetri skickas varje sekund.

    ![Ange telemetriintervall](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Appen får en bekräftelse som anger om metoden har körts korrekt eller inte.

    ![Bekräftelse av direktmetod](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
