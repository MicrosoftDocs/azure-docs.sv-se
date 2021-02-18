---
title: Skicka telemetri för enheten till Azure IoT Hub snabb start (Node.js)
description: I den här snabb starten använder du Azure IoT Hub Device SDK för Node.js för att skicka telemetri från en enhet till en IoT-hubb.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 895b49779a202b817af1ede5082a11a5b1736afd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654996"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Snabb start: skicka telemetri från en enhet till en IoT Hub (Node.js)

**Gäller för**: [enhets program utveckling](about-iot-develop.md#device-application-development)

I den här snabb starten får du lära dig ett grundläggande program utvecklings arbets flöde för IoT-enheter. Du använder Azure CLI för att skapa en Azure IoT-hubb och en simulerad enhet. sedan använder du Azure IoT Node.js SDK för att få åtkomst till enheten och skicka telemetri till hubben.

## <a name="prerequisites"></a>Förutsättningar
- Om du inte har en Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI. Du kan köra alla kommandon i den här snabb starten med hjälp av Azure Cloud Shell, ett interaktivt CLI-gränssnitt som körs i din webbläsare. Om du använder Cloud Shell behöver du inte installera något. Om du föredrar att använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.76 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Om du använder Azure Cloud Shell ska du inte uppdatera den installerade versionen av Node.js. Den Azure Cloud Shell har redan den senaste Node.js versionen.

    Verifiera den aktuella versionen av Node.js på din utvecklings dator med hjälp av följande kommando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Använd Node.js SDK för att skicka meddelanden
I det här avsnittet ska du använda Node.js SDK för att skicka meddelanden från din simulerade enhet till IoT Hub. 

1. Öppna ett nytt terminalfönster. Du kommer att använda den här terminalen för att installera Node.js SDK och arbeta med Node.js exempel kod. Du bör nu ha två terminaler öppna: den som du nyss öppnade för att arbeta med Node.js och CLI-gränssnittet som du använde i föregående avsnitt för att ange Azure CLI-kommandon. 

1. Kopiera [Azure IoT Node.js SDK Device-exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) till den lokala datorn:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Gå till katalogen *Azure-IoT-SDK-Node/Device/samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Installera Azure IoT Node.js SDK och nödvändiga beroenden:

    ```console
    npm install
    ```
    Det här kommandot installerar de nödvändiga beroendena som anges i *package.js* filen i katalogen enhets exempel.

1. Ange enhets anslutnings strängen som en miljö variabel som kallas `DEVICE_CONNECTION_STRING` . Det sträng värde som ska användas är den sträng som du fick i föregående avsnitt när du har skapat den simulerade Node.js enheten. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > För Windows CMD finns det inga citat tecken runt anslutnings strängen.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. I ditt öppna CLI-gränssnitt kör du kommandot [AZ IoT Hub Monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events&preserve-view=true) för att börja övervaka händelser på din simulerade IoT-enhet.  Händelse meddelanden skrivs ut i terminalen när de tas emot.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. I din Node.js Terminal kör du koden för den installerade exempel filen *simple_sample_device.js* . Den här koden ansluter till den simulerade IoT-enheten och skickar ett meddelande till IoT Hub.

    Köra Node.js-exemplet från terminalen:
    ```console
    node ./simple_sample_device.js
    ```

    Du kan också köra Node.js koden från exemplet i Java Script IDE:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

När Node.js koden skickar ett simulerat telemetri från enheten till IoT Hub visas meddelandet i CLI-gränssnittet som övervakar händelser:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Enheten är nu säker ansluten och skickar telemetri till Azure IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver de Azure-resurser som skapats i den här snabb starten kan du ta bort dem med hjälp av Azure CLI.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Ta bort en resursgrupp med namnet:
1. Kör kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) . Det här kommandot tar bort resurs gruppen, IoT Hub och den enhets registrering som du skapade.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kör kommandot [AZ Group List](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list&preserve-view=true) för att bekräfta att resurs gruppen har tagits bort.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig ett grundläggande Azure IoT Application-arbetsflöde för att på ett säkert sätt ansluta en enhet till molnet och skicka enhet till moln-telemetri. Du använde Azure CLI för att skapa en IoT-hubb och en simulerad enhet. sedan använde du Azure IoT Node.js SDK för att få åtkomst till enheten och skicka telemetri till hubben. 

I nästa steg ska du utforska Azure IoT Node.js SDK genom program exempel.

- [Fler Node.js exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): den här katalogen innehåller fler exempel från Node.js SDK-lagringsplatsen för att demonstrera IoT Hub scenarier. 
