---
title: Schemalägg jobb med Azure IoT Hub (Node) | Microsoft Docs
description: 'Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du kan använda Azure IoT SDK: er för Node.js för att implementera de simulerade enhetens appar och en tjänst-app för att köra jobbet.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: e1992c806619154fa7b3c33500b2e54fbc919f20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151429"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Schema-och sändnings jobb (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som gör det möjligt för en backend-app att skapa och spåra jobb som schemalägger och uppdaterar miljon tals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

Ett jobb är konceptuellt, och spårar förloppet för körningen mot en uppsättning enheter som definieras av en enhets dubbla frågor.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart på 10 000 enheter, som anges av en enhets dubbla frågor och schemaläggs vid ett senare tillfälle. Programmet kan sedan spåra förloppet när var och en av enheterna får och kör metoden starta om.

Lär dig mer om var och en av dessa funktioner i dessa artiklar:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-node-node-twin-getstarted.md) och [Självstudier: hur du använder enhetens dubbla egenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub Developer Guide – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: direkta metoder](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Node.js simulerad Device-app som har en direkt metod som aktiverar **lockDoor**, som kan anropas av lösningens Server del.

* Skapa en Node.js-konsolsession som anropar **lockDoor** Direct-metoden i den simulerade Device-appen med ett jobb och uppdaterar önskade egenskaper med ett enhets jobb.

I slutet av den här självstudien har du två Node.js-appar:

* **simDevice.js**, som ansluter till din IoT-hubb med enhets identiteten och tar emot en **lockDoor** Direct-metod.

* **scheduleJobService.js**, som anropar en direkt metod i den simulerade Device-appen och uppdaterar enhetens dubbla egenskaper med ett jobb.

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0. x eller senare. [Förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Node.js-konsolsession som svarar på en direkt metod som anropas av molnet, vilket utlöser en simulerad **lockDoor** -metod.

1. Skapa en ny tom mapp med namnet **simDevice**.  I mappen **simDevice** skapar du en package.jspå en fil med hjälp av följande kommando i kommando tolken.  Acceptera alla standardvärden:

   ```console
   npm init
   ```

2. I kommando tolken i mappen **simDevice** kör du följande kommando för att installera paketet **Azure-IoT-Device** SDK och **Azure-IoT-Device-MQTT** :

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Med hjälp av en text redigerare skapar du en ny **simDevice.js** -fil i mappen **simDevice** .

4. Lägg till följande "require"-instruktioner i början av **simDevice.js** -filen:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**. Ersätt `{yourDeviceConnectionString}` placeholder-värdet med enhets anslutnings strängen som du kopierade tidigare.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att hantera metoden **lockDoor** .

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Lägg till följande kod för att registrera hanteraren för **lockDoor** -metoden.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Spara och Stäng **simDevice.jss** filen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägg jobb för att anropa en direkt metod och uppdatera en enhets dubbla egenskaper

I det här avsnittet skapar du en Node.js-konsolsession som initierar en fjärran sluten **lockDoor** på en enhet med hjälp av en direkt metod och uppdaterar enhetens dubbla egenskaper.

1. Skapa en ny tom mapp med namnet **scheduleJobService**.  I mappen **scheduleJobService** skapar du en package.jspå en fil med hjälp av följande kommando i kommando tolken.  Acceptera alla standardvärden:

    ```console
    npm init
    ```

2. I kommando tolken i mappen **scheduleJobService** kör du följande kommando för att installera paketet **Azure-iothub** Device SDK och paketet **Azure-IoT-Device-MQTT** :

    ```console
    npm install azure-iothub uuid --save
    ```

3. Med hjälp av en text redigerare skapar du en ny **scheduleJobService.js** -fil i mappen **scheduleJobService** .

4. Lägg till följande "require"-instruktioner i början av **scheduleJobService.js** -filen:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Lägg till följande variabel deklarationer. Ersätt `{iothubconnectionstring}` placeholder-värdet med värdet som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Om du har registrerat en annan enhet än **myDeviceId**, ska du se till att ändra den i villkoret för frågan.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Lägg till följande funktion som används för att övervaka körningen av jobbet:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Lägg till följande kod för att schemalägga jobbet som anropar enhets metoden:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Lägg till följande kod för att schemalägga jobbet för att uppdatera enheten:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Spara och Stäng **scheduleJobService.jss** filen.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommando tolken i mappen **simDevice** kör du följande kommando för att börja lyssna efter metoden starta om Direct.

    ```console
    node simDevice.js
    ```

2. I kommando tolken i mappen **scheduleJobService** kör du följande kommando för att utlösa jobben för att låsa dörren och uppdatera den dubbla

    ```console
    node scheduleJobService.js
    ```

3. Du ser enhets svaret till den direkta metoden och jobb status i-konsolen.

   Följande visar enhets svaret till den direkta metoden:

   ![Simulerad enhets programs utdata](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Nedan visas jobb för schemaläggning av tjänster för den direkta uppdateringen av Direct-metoden och enheten, och de jobb som körs till slut för ande:

   ![Kör den simulerade Device-appen](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

Om du vill fortsätta att komma igång med IoT Hub-och enhets hanterings mönster, t. ex. fjärran sluten av den inbyggda program varan, kan du läsa mer i [självstudie:](tutorial-firmware-update.md)

För att fortsätta komma igång med IoT Hub, se [komma igång med Azure IoT Edge](../iot-edge/quickstart-linux.md).