---
title: 'Utveckla för Android-saker plattform med Azure IoT SDK: er | Microsoft Docs'
description: 'Guide för utvecklare – lär dig mer om att utveckla Android-saker med Azure IoT Hub SDK: er.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94843765"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Utveckla för Android-saker-plattform med Azure IoT-SDK: er

[Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) : er tillhandahåller första nivå stöd för populära plattformar som Windows, Linux, OSX, MBED och mobila plattformar som Android och iOS.  Som en del av vårt åtagande att möjliggöra större valmöjligheter och flexibilitet i IoT-distributioner, stöder Java SDK även [Android-objekts](https://developer.android.com/things/) plattform.  Utvecklare kan utnyttja fördelarna med Android-sakernas operativ system på enhets sidan, medan [Azure IoT Hub](about-iot-hub.md) som den centrala meddelande hubben som skalas till miljon tals anslutna enheter samtidigt.

I den här självstudien beskrivs stegen för att bygga ett program på enhets sidan på Android-saker med Azure IoT Java SDK.

## <a name="prerequisites"></a>Förutsättningar

* En Android-enhet som stöder maskin vara med Android-objekt som körs.  Du kan följa [dokumentationen för Android-saker](https://developer.android.com/things/get-started/kits#flash-at) om hur du kan Flash Android saker OS.  Se till att din Android-enhet är ansluten till Internet med viktig kring utrustning, till exempel tangent bord, bildskärm och mus.  I den här självstudien används Raspberry Pi 3.

* Senaste versionen av [Android Studio](https://developer.android.com/studio/)

* Senaste versionen av [git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet.

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **MyAndroidThingsDevice** : det här är det namn som angetts för den registrerade enheten. Använd MyAndroidThingsDevice som det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln samt uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta *enhets anslutnings strängen* för den enhet som du just har registrerat. Ersätt `YourIoTHubName` nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="building-an-android-things-application"></a>Skapa ett Android saker-program

1. Det första steget för att skapa ett Android saker-program ansluter till dina Android-enheter. Anslut Android-enheten till en bildskärm och Anslut den till Internet. Android-saker tillhandahåller [dokumentation](https://developer.android.com/things/get-started/kits) om hur du ansluter till WiFi. När du har anslutit till Internet bör du anteckna IP-adressen som visas under nätverk.

2. Använd [ADB](https://developer.android.com/studio/command-line/adb) -verktyget för att ansluta till din Android-enhet med den IP-adress som anges ovan. Dubbelklicka på anslutningen genom att använda det här kommandot från terminalen. Du bör se enheterna som listas som "anslutna".

   ```
   adb devices
   ```

3. Hämta vårt exempel för Android/Android-saker från den här [lagrings platsen](https://github.com/Azure-Samples/azure-iot-samples-java) eller Använd git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. I Android Studio öppnar du Android-projektet i "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Öppna gradle. Properties-filen och Ersätt "Device_connection_string" med enhets anslutnings strängen som du antecknade tidigare.
 
6. Klicka på Kör-debug och välj din enhet för att distribuera den här koden till dina Android-enheter.

7. När programmet har startats kan du se att det finns ett program som körs på Android saker-enheten. Det här exempel programmet skickar slumpmässigt genererade temperatur avläsningar.

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Du kan visa data via din IoT Hub när den tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du hanterar anslutningar och Reliable Messaging](iot-hub-reliability-features-in-sdks.md) med hjälp av IoT Hub SDK: er.
* Lär dig mer om hur du [utvecklar för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Stöd för Azure IoT SDK-plattformen](iot-hub-device-sdk-platform-support.md)