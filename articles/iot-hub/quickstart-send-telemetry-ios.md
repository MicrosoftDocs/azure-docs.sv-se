---
title: Snabb start – skicka telemetri till Azure IoT Hub snabb start (iOS)
description: I den här snabbstarten kör du ett iOS-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
ms.date: 04/03/2019
ms.openlocfilehash: 21d25bd461a1a7e0cd2718cbe0ea32be06cf9055
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067052"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här artikeln skickar du telemetri från ett simulerat program till IoT Hub. Sedan kan du visa data från ett program på serversidan.

I den här artikeln används ett färdigt Swift-program till att skicka telemetrin och ett CLI-verktyg till att läsa telemetrin från IoT Hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ladda ned kodexemplet från [Azure-exemplen](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

- Den senaste versionen av [XCode](https://developer.apple.com/xcode/) med den senaste versionen av iOS SDK. Den här snabb starten har testats med XCode 10,2 och iOS 12,2.

- Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

- Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **myiOSdevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **myiOSdevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Exempelprogrammet körs på en iOS-enhet som ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, och skickar simulerad telemetri om temperatur och luftfuktighet. 

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Öppna ett lokalt terminalfönster och navigera till mappen Azure-IoT-Samples-iOS som du laddade ned i förberedelserna. Navigera sedan till exempelprojektet:

```sh
cd quickstart/sample-device
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden. 

### <a name="run-the-sample-application"></a>Köra exempelprogrammet 

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera projektet **MQTT Client Sample** och expandera sedan mappen med samma namn.  
3. Öppna **ViewController.swift** för redigering i XCode. 
4. Sök efter **ConnectionString** -variabeln och uppdatera värdet med enhets anslutnings strängen som du gjorde en anteckning om tidigare.
5. Spara ändringarna. 
6. Kör projektet i enhetsemulatorn med knappen **Build and run** (Skapa och kör) eller tangentkombinationen **command + r**. 

   ![Kör projektet](media/quickstart-send-telemetry-ios/run-sample.png)

7. När emulatorn öppnas väljer du **Start** i exempelappen.

Följande skärmbild visar några exempelutdata från när programmet skickar simulerad telemetri till din IoT-hubb:

   ![Kör den simulerade enheten](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

Exempelappen du körde i XCode-emulatorn visar data om meddelanden som skickas från enheten. Du kan också visa data via din IoT-hubb när de tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Följande skärmbild visar utdata när tillägget tar emot telemetridata som skickats från den simulerade enheten till hubben:

Följande skärmbild visar vilken typ av telemetri du ser i det lokala terminalfönstret:

![Visa telemetrin](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben från en iOS-enhet och läser Telemetrin från hubben. 

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-node.md)
