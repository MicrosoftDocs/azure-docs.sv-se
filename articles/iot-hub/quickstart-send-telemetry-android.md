---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du ett Android-exempelprogram som skickar simulerad telemetri till en IoT-hubb och läser telemetrin från IoT-hubben för bearbetning i molnet.
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
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: 4bc8dfb879f362463153819cdf99869522c55c1b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863829"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Snabbstart: Skicka IoT-telemetri från en Android-enhet

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

I den här snabbstarten skickar du telemetri till en Azure IoT Hub från ett Android-program som körs på en fysisk eller simulerad enhet. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten används ett förskrivet Android-program för att skicka telemetrin. Telemetrin läses från IoT-hubben med hjälp av Azure Cloud Shell. Innan du kör programmet skapar du en IoT-hubb och registrera ren enhet med hubben.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio med Android SDK 27](https://developer.android.com/studio/). Mer information finns i [android-installation](https://developer.android.com/studio/install). Android SDK-27 används av exemplet i den här artikeln.

* [Ett Android-exempelprogram](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Det här exemplet är en del av [lagringsplatsen azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Port 8883 är öppen i brandväggen. Enhetsexempel i den här snabbstarten använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice:** Det här är namnet på den enhet som du registrerar. Vi rekommenderar att du använder **MyAndroidDevice** så som visas. Om du väljer ett annat namn för din enhet måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommando i Azure Cloud Shell hämta _enhetsanslutningssträngen_ för den enhet som du just registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du använder det här värdet senare i den här snabbstarten för att skicka telemetri.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

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

    ![Program](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](/cli/azure/iot) för att övervaka de enhetsmeddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten ska du konfigurera en IoT-hubb, registrera en enhet, skicka simulerad telemetri till hubben med hjälp av ett Android-program och läsa telemetrin från hubben med hjälp av Azure Cloud Shell.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-android.md)
