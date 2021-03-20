---
title: Meddelanden från moln till enhet med Azure IoT Hub (iOS) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för iOS.'
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 15c0df33b8f09ec71f2be913d72f0785dc766375
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027541"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Skicka meddelanden från moln till enhet med IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-ios.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en simulerad enhets app som skickar enhet till molnet-meddelanden.

I den här självstudiekursen lär du dig att:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om moln-till-enhet-meddelanden i [meddelande avsnittet i guiden för IoT Hub utvecklare](iot-hub-devguide-messaging.md).

I slutet av den här artikeln kör du två Swift iOS-projekt:

* **Sample-Device**, samma app skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **exempel-service**, som skickar ett meddelande från moln till enhet till den simulerade enheten via IoT Hub och sedan får leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhets plattformar och språk (inklusive C, Java, python och Java Script) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner för hur du ansluter din enhet till den här själv studie kursen och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* En aktiv IoT-hubb i Azure.

* Kod exemplet från [Azure-exempel](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* Den senaste versionen av [XCode](https://developer.apple.com/xcode/) med den senaste versionen av iOS SDK. Denna snabbstart har testats med XCode 9.3 och iOS 11.3.

* Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simulera en IoT-enhet

I det här avsnittet ska du simulera en iOS-enhet som kör ett SWIFT-program för att ta emot meddelanden från molnet till enheten från IoT Hub. 

Det här är exempel enheten som du skapar i artikeln [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md). Om du redan har den som körs kan du hoppa över det här avsnittet.

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Öppna ett terminalfönster och navigera till mappen Azure-IoT-Samples-iOS som du laddade ned i förberedelserna. Navigera sedan till exempelprojektet:

```sh
cd quickstart/sample-device
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden.

### <a name="run-the-sample-device-application"></a>Kör programmet exempel enhet

1. Hämta anslutnings strängen för enheten. Du kan kopiera den här strängen från [Azure Portal](https://portal.azure.com) på bladet med enhets information eller hämta den med följande CLI-kommando:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera **MQTT-klientens exempel** projekt och sedan mapp med samma namn.  

3. Öppna **ViewController.swift** för redigering i XCode. 

4. Sök efter **ConnectionString** -variabeln och uppdatera värdet med enhets anslutnings strängen som du kopierade i det första steget.

5. Spara ändringarna. 

6. Kör projektet i enhetsemulatorn med knappen **Build and run** (Skapa och kör) eller tangentkombinationen **command + r**.

   ![Skärm bild som visar knappen Skapa och kör i enhetens emulator.](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simulera en tjänst enhet

I det här avsnittet simulerar du en andra iOS-enhet med en SWIFT-app som skickar meddelanden från molnet till enheten via IoT Hub. Den här konfigurationen är användbar för IoT-scenarier där det finns en iPhone eller iPad som fungerar som en styrenhet för andra iOS-enheter som är anslutna till en IoT-hubb.

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Gå till mappen Azure IoT iOS-exempel som du laddade ned i kraven. Navigera sedan till exempel tjänst projektet:

```sh
cd quickstart/sample-service
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden.

### <a name="run-the-sample-service-application"></a>Kör exempel tjänst programmet

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expandera **AzureIoTServiceSample** -projektet och expandera sedan mappen med samma namn.  

3. Öppna **ViewController.swift** för redigering i XCode. 

4. Sök efter **ConnectionString** -variabeln och uppdatera värdet med tjänst anslutnings strängen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

5. Spara ändringarna.

6. I Xcode ändrar du inställningarna för emulatorn till en annan iOS-enhet än du använde för att köra IoT-enheten. XCode kan inte köra flera emulatorer av samma typ.

   ![Ändra emulator-enhet](media/iot-hub-ios-swift-c2d/change-device.png)

7. Kör projektet i enhetens emulator med knappen **skapa och kör** eller med **kommando + r** för nyckel kombination.

   ![Skärm bild som visar knappen Skapa och kör.](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

Du är nu redo att använda de två programmen för att skicka och ta emot meddelanden från molnet till enheten.

1. I **exempel appen iOS app** som körs på den simulerade IoT-enheten klickar du på **Starta**. Programmet börjar skicka enhets-till-moln-meddelanden, men börjar också lyssna efter meddelanden från molnet till enheten.

   ![Visa exempel på IoT Device-appen](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. I **IoTHub-tjänstens exempel** app som körs på den simulerade tjänst enheten anger du ID: t för den IoT-enhet som du vill skicka ett meddelande till. 

3. Skriv ett meddelande i klartext och klicka sedan på **Skicka**.

    Flera åtgärder sker så snart du klickar på Skicka. Tjänst exemplet skickar meddelandet till din IoT-hubb, som appen har åtkomst till på grund av den tjänst anslutnings sträng som du har angett. Din IoT Hub kontrollerar enhets-ID, skickar meddelandet till mål enheten och skickar ett bekräftelse kvitto till käll enheten. Den app som körs på din simulerade IoT-enhet söker efter meddelanden från IoT Hub och skriver ut texten från den senaste på skärmen.

    Dina utdata bör se ut som i följande exempel:

   ![Visa meddelanden från moln till enhet](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i dokumentationen för [Azure IoT Solution acceleratorer](https://azure.microsoft.com/documentation/suites/iot-suite/) .

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
