---
title: Kom igång med Azure IoT Hub Device Management (Node) | Microsoft Docs
description: Så här använder du IoT Hub enhets hantering för att starta en fjärran sluten enhet. Du använder Azure IoT SDK för Node.js för att implementera en simulerad enhets app som innehåller en direkt metod och en tjänst-app som anropar den direkta metoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91252025"
---
# <a name="get-started-with-device-management-nodejs"></a>Kom igång med enhets hantering (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd [Azure Portal](https://portal.azure.com) för att skapa en IoT Hub och skapa en enhets identitet i din IoT-hubb.

* Skapa en simulerad enhets app som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en Node.js-konsolsession som anropar metoden starta om direkt i den simulerade appen via din IoT Hub.

I slutet av den här självstudien har du två Node.js-konsol program:

* **dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhets identiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.js**, som anropar en direkt metod i den simulerade Device-appen, visar svaret och visar uppdaterade rapporterade egenskaper.

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0. x eller senare. [Förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet

* Utlösa omstart av en simulerad enhet

* Använd de rapporterade egenskaperna för att aktivera enhets dubbla frågor för att identifiera enheter och när de startades om senast

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommando tolken i mappen **manageddevice** kör du följande kommando för att installera paketet **Azure-IoT-Device** SDK och **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Med hjälp av en text redigerare skapar du en **dmpatterns_getstarted_device.js** -fil i mappen **manageddevice**

4. Lägg till följande "require"-instruktioner i början av **dmpatterns_getstarted_device.js** -filen:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt `{yourdeviceconnectionstring}` placeholder-värdet med enhets anslutnings strängen som du kopierade tidigare i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att implementera den direkta metoden på enheten

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Öppna anslutningen till IoT-hubben och starta den direkta metoden lyssnare:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Spara och Stäng **dmpatterns_getstarted_device.jss** filen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärran sluten omstart på enheten med en direkt metod

I det här avsnittet skapar du en Node.js-konsolsession som initierar en fjärromstart på en enhet med hjälp av en direkt metod. Appen använder enhets dubbla frågor för att identifiera den senaste omstarts tiden för enheten.

1. Skapa en tom mapp med namnet **triggerrebootondevice**. I mappen **triggerrebootondevice** skapar du en package.jspå en fil med hjälp av följande kommando i kommando tolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommando tolken i mappen **triggerrebootondevice** kör du följande kommando för att installera paketet **Azure-iothub** Device SDK och paketet **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Med hjälp av en text redigerare skapar du en **dmpatterns_getstarted_service.js** -fil i mappen **triggerrebootondevice**

4. Lägg till följande "require"-instruktioner i början av **dmpatterns_getstarted_service.js** -filen:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Lägg till följande variabel deklarationer och Ersätt `{iothubconnectionstring}` plats hållarnas värde med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Lägg till följande funktion för att anropa enhets metoden för att starta om mål enheten:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Lägg till följande funktion för att fråga efter enheten och hämta den senaste omstarts tiden:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Lägg till följande kod för att anropa de funktioner som utlöser metoden för omstart direkt och fråga efter senaste omstart:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Spara och Stäng **dmpatterns_getstarted_service.jss** filen.

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. I kommando tolken i mappen **manageddevice** kör du följande kommando för att börja lyssna efter metoden starta om Direct.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. I kommando tolken i mappen **triggerrebootondevice** kör du följande kommando för att utlösa fjärromstart och fråga efter enhetens för att hitta den senaste omstarts tiden.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Du ser enhets svaret för metoden starta om direkt och status för omstart i konsolen.

   Nedan visas enhets svaret på den direkta metoden för omstart som skickas av tjänsten:

   ![manageddevice app-utdata](./media/iot-hub-node-node-device-management-get-started/device.png)

   Följande visar den tjänst som utlöser omstarten och avsöker enheten för den senaste omstarts tiden:

   ![triggerrebootondevice app-utdata](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
