---
title: Snabb start – kommunicera med enhetens app i Node.js med Azure IoT Hub enhets strömmar
description: I den här snabb starten ska du köra ett Node.js-program på tjänst sidan som kommunicerar med en IoT-enhet via en enhets ström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624396"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snabb start: kommunicera med ett enhets program i Node.js via IoT Hub enhets strömmar (för hands version)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

I den här snabb starten kör du ett program på Server sidan och konfigurerar kommunikationen mellan en enhet och tjänst genom att använda enhets strömmar. Azure IoT Hub enhets strömmar gör att tjänst-och enhets program kan kommunicera på ett säkert och användarvänligt sätt. Under den offentliga för hands versionen stöder Node.js SDK endast enhets strömmar på tjänst sidan. Därför omfattar den här snabbstarten bara instruktioner för att köra programmet på tjänstsidan.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [kommunikationen med enhetens appar i C via IoT Hub enhets strömmar](./quickstart-device-streams-echo-c.md) eller [kommunicera med enhetens appar i C# via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md).

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

    ```cmd/sh
    node --version
    ```

* [Ett exempel på ett Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Microsoft Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapats i följande regioner:
>
> * Central US
> * Centrala USA-EUAP
> * Norra Europa
> * Sydostasien

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   Min **enhet**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder min **enhet** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Du måste också ha en *tjänstanslutningssträng* för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänst anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

I det här avsnittet ska du köra både program på enhets sidan och programmet på tjänst sidan och kommunicera mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För ett program på enhets sidan använder du en av de medföljande enhets programmen som finns i följande snabb starter:

* [Kommunicera med enhetens appar i C via IoT Hub enhets strömmar](./quickstart-device-streams-echo-c.md)

* [Kommunicera med enhetens appar i C# via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md)

Se till att programmet på enhetssidan körs innan du fortsätter till nästa steg.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Node.js-programmet på tjänstsidan i den här snabbstarten har följande funktioner:

* Skapar en enhetsström till en IoT-enhet.
* Läser indata från kommandoraden och skickar dem till enhetsprogrammet, som returnerar dem.

Koden visar initierings processen för en enhets ström, samt hur du kan använda den för att skicka och ta emot data.

Förutsatt att programmet på enhets sidan körs följer du stegen nedan i ett lokalt terminalfönster för att köra programmet på tjänst sidan i Node.js:

* Ange autentiseringsuppgifter för tjänsten och enhets-ID som miljövariabler.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändra plats hållaren ServiceConnectionString så att den matchar din tjänst anslutnings sträng och att **enheten** matchar ditt enhets-ID om du har gett ditt eget namn.

* Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör exempelkoden med hjälp av noden.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet läser programmet på tjänst sidan bara `stdin` på terminalen och skickar det till enheten, som sedan skickar den tillbaka. Detta demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen.

![Utdata för konsol på tjänst Sidan](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Du kan sedan avsluta programmet genom att trycka på Retur igen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, upprättat en enhets ström mellan program på enhets-och tjänst sidan och använde data strömmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
