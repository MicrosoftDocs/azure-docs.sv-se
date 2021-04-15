---
title: Kom igång med Azure IoT Hub enhetshantering (Node) | Microsoft Docs
description: Hur du använder IoT Hub för att initiera en omstart av en fjärransluten enhet. Du använder Azure IoT SDK för Node.js att implementera en simulerad enhetsapp som innehåller en direktmetod och en tjänstapp som anropar direktmetoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 557cdc5fb71f998dc0b69e52148cc93da7cc8ea7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477986"
---
# <a name="get-started-with-device-management-nodejs"></a>Kom igång med enhetshantering (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure Portal [för](https://portal.azure.com) att skapa en IoT Hub och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som innehåller en direktmetod som startar om enheten. Direktmetoder anropas från molnet.

* Skapa en Node.js-konsolapp som anropar direktmetoden för omstart i den simulerade enhetsappen via din IoT-hubb.

I slutet av den här självstudien har du två Node.js konsolappar:

* **dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, tar emot en direktmetod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.js**, som anropar en direktmetod i den simulerade enhetsappen, visar svaret och visar de uppdaterade rapporterade egenskaperna.

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön beskriver hur](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) du installerar Node.js den här självstudien i Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här artikeln använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet

* Utlösa en simulerad omstart av enheten

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor för att identifiera enheter och när de senast startades om

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommandotolken i mappen **manageddevice** kör du följande kommando för att installera **SDK-paketet azure-iot-device** och **paketet azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Med hjälp av en textredigerare skapar **dmpatterns_getstarted_device.js** en fil i **mappen manageddevice.**

4. Lägg till följande "require"-instruktioner  i början avdmpatterns_getstarted_device.jsfilen:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt `{yourdeviceconnectionstring}` platshållarvärdet med enhetsanslutningssträngen som du kopierade tidigare [i Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att implementera direktmetoden på enheten

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

7. Öppna anslutningen till din IoT-hubb och starta lyssnaren för direktmetod:

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

8. Spara och stäng **dmpatterns_getstarted_device.js** filen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Hantering av tillfälliga fel.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Hämta IoT Hub-anslutningssträngen

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärrstart på enheten med en direktmetod

I det här avsnittet skapar du en Node.js-konsolapp som initierar en fjärrstart på en enhet med hjälp av en direktmetod. Appen använder enhetstvillingfrågor för att identifiera den senaste omstartstiden för enheten.

1. Skapa en tom mapp med **namnet triggerrebootondevice**. I mappen **triggerrebootondevice** skapar du en package.jspå filen med följande kommando i kommandotolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommandotolken i **mappen triggerrebootondevice** kör du följande kommando för att installera **SDK-paketet azure-iothub** Device och **paketet azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Använd en textredigerare och skapa **endmpatterns_getstarted_service.js** i **mappen triggerrebootondevice.**

4. Lägg till följande "require"-instruktioner  i början avdmpatterns_getstarted_service.jsfilen:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Lägg till följande variabeldeklarationer och ersätt platshållarvärdet med den IoT Hub-anslutningssträng som du kopierade tidigare i `{iothubconnectionstring}` [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Lägg till följande funktion för att anropa enhetsmetoden för att starta om målenheten:

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

7. Lägg till följande funktion för att fråga efter enheten och hämta den senaste omstartstiden:

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

8. Lägg till följande kod för att anropa de funktioner som utlöser direktmetoden för omstart och fråga efter den senaste omstarten:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Spara och stäng **dmpatterns_getstarted_service.js** fil.

## <a name="run-the-apps"></a>Kör apparna

Nu är du redo att köra apparna.

1. I kommandotolken i **mappen manageddevice** kör du följande kommando för att börja lyssna efter direktmetoden för omstart.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Kör följande kommando i kommandotolken i mappen **triggerrebootondevice** för att utlösa fjärrstarten och fråga efter enhetstvillingen för att hitta den senaste omstartstiden.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Du ser enhetssvaret på direktmetoden för omstart och omstartsstatusen i -konsolen.

   Nedan visas enhetssvaret på den direktinstartsmetod som skickas av tjänsten:

   ![utdata för manageddevice-appen](./media/iot-hub-node-node-device-management-get-started/device.png)

   Följande visar den tjänst som utlöser omstarten och avsöker enhetstvillingen under den senaste omstarten:

   ![utdata för triggerrebootondevice-appen](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
