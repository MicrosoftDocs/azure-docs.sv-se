---
title: Meddelanden från moln till enhet med Azure IoT Hub (Node) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för Node.js. Du ändrar en simulerad enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91446884"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Skicka meddelanden från moln till enhet med IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-node.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en simulerad enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Det visar hur du:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.
* Ta emot meddelanden från molnet till enheten på en enhet.
* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om moln-till-enhet-meddelanden i [guiden för IoT Hub utvecklare](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Node.js-konsol program:

* **SimulatedDevice**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage**, som skickar ett meddelande från moln till enhet till den simulerade Device-appen via IoT Hub, och sedan får leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhets plattformar och språk (inklusive C, Java, python och Java Script) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner för hur du ansluter din enhet till den här själv studie kursen och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0. x eller senare. [Förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade Device-appen

I det här avsnittet ändrar du den simulerade Device-app som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) för att ta emot meddelanden från molnet till enheten från IoT Hub.

1. Öppna **SimulatedDevice.js** -filen med hjälp av en text redigerare. Den här filen finns i mappen **IoT-hub\Quickstarts\simulated-Device** av rotmappen i den Node.js exempel kod som du laddade ned i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-node.md) -snabb start.

2. Registrera en hanterare med enhets klienten för att ta emot meddelanden som skickas från IoT Hub. Lägg till anropet `client.on` strax efter den rad som skapar enhets klienten som i följande kodfragment:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

I det här exemplet anropar enheten funktionen **Complete** för att meddela IoT Hub att den har bearbetat meddelandet och att den kan tas bort i enhets kön på ett säkert sätt. Anropet till **Complete** krävs inte om du använder MQTT-transport och kan utelämnas. Det krävs för AMQP och HTTPS.

Med AMQP och HTTPS, men inte MQTT, kan enheten också:

* Överge ett meddelande som resulterar i att IoT Hub behåller meddelandet i enhets kön för framtida konsumtion.
* Avvisa ett meddelande som permanent tar bort meddelandet från enhets kön.

Om något händer som hindrar enheten från att kunna slutföra, avbryta eller avvisa meddelandet, kommer IoT Hub efter en fast tids gräns, köa meddelandet för leverans igen. Därför måste meddelande bearbetnings logiken i enhets appen vara *idempotenta*, så att samma meddelande får flera gånger samma resultat.

Mer detaljerad information om hur IoT Hub bearbetar meddelanden från molnet till enheten, inklusive information om livs cykeln för moln-till-enhet-meddelanden, finns i [skicka meddelanden från moln till enhet från en IoT-hubb](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Om du använder HTTPS i stället för MQTT eller AMQP som transport söker **DeviceClient** -instansen efter meddelanden från IoT Hub sällan (minst var 25: e minut). Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd finns i avsnittet [om kommunikation mellan moln och enheter](iot-hub-devguide-c2d-guidance.md) och [Välj ett kommunikations protokoll](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

I det här avsnittet skapar du en Node.js-konsolsession som skickar meddelanden från molnet till enheten till den simulerade Device-appen. Du behöver enhets-ID för enheten som du har lagt till i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-node.md) -snabb start. Du behöver också den IoT Hub-anslutningssträng som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.

1. Skapa en tom mapp med namnet **sendcloudtodevicemessage**. I mappen **sendcloudtodevicemessage** skapar du en package.jspå en fil med hjälp av följande kommando i kommando tolken. Acceptera alla standardvärden:

    ```shell
    npm init
    ```

2. I kommando tolken i mappen **sendcloudtodevicemessage** kör du följande kommando för att installera paketet **Azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. Med hjälp av en text redigerare skapar du en **SendCloudToDeviceMessage.js** -fil i mappen **sendcloudtodevicemessage**

4. Lägg till följande- `require` instruktioner i början av **SendCloudToDeviceMessage.js** -filen:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Lägg till följande kod i **SendCloudToDeviceMessage.js** -filen. Ersätt plats hållarna {IoT Hub-anslutningssträng} och {Device ID} med IoT Hub-anslutningssträngen och enhets-ID: t som du antecknade tidigare:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Lägg till följande funktion för att skriva ut åtgärds resultat till-konsolen:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Lägg till följande funktion för att skriva ut meddelanden om leverans feedback till-konsolen:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Lägg till följande kod för att skicka ett meddelande till enheten och hantera feedback-meddelandet när enheten bekräftar meddelandet från molnet till enheten:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Spara och Stäng **SendCloudToDeviceMessage.js** -filen.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommando tolken i mappen **simulerad enhet** kör du följande kommando för att skicka telemetri till IoT Hub och för att lyssna efter meddelanden från moln till enhet:

    ```shell
    node SimulatedDevice.js
    ```

    ![Kör den simulerade Device-appen](./media/iot-hub-node-node-c2d/receivec2d.png)

2. I en kommando tolk i mappen **sendcloudtodevicemessage** kör du följande kommando för att skicka ett meddelande från moln till enhet och väntar på bekräftelsen:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Kör appen för att skicka kommandot från molnet till enheten](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktions koden bör du implementera principer för omförsök (till exempel exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [Azure IoT Remote Monitoring Solution Accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
