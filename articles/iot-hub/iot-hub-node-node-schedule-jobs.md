---
title: Schemalägga jobb med Azure IoT Hub (Node) | Microsoft Docs
description: Så här schemalägger du Azure IoT Hub jobb för att anropa en direktmetod på flera enheter. Du använder Azure IoT-SDK:er för Node.js att implementera de simulerade enhetsapparna och en tjänstapp för att köra jobbet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc0ea9817b9cbc27816354c48abbe26d79a83f04
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477918"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Schemalägga och sända jobb (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en helt hanterad tjänst som gör att en backend-app kan skapa och spåra jobb som schemalägger och uppdaterar miljontals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

Konceptuellt omsluter ett jobb en av dessa åtgärder och spårar körningsförloppet mot en uppsättning enheter, som definieras av en enhetstvillingfråga.  En backend-app kan till exempel använda ett jobb för att anropa en omstartsmetod på 10 000 enheter, som anges av en enhetstvillingfråga och schemaläggs vid ett senare tillfälle. Programmet kan sedan följa förloppet när var och en av dessa enheter tar emot och kör omstartsmetoden.

Läs mer om var och en av dessa funktioner i följande artiklar:

* Enhetstvilling och egenskaper: [Kom igång med enhetstvillingarna](iot-hub-node-node-twin-getstarted.md) och [Självstudie: Använda egenskaper för enhetstvilling](tutorial-device-twins.md)

* Direktmetoder: [IoT Hub utvecklarguide – direkta metoder och](iot-hub-devguide-direct-methods.md) [Självstudie: direkta metoder](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Node.js simulerad enhetsapp som har en direktmetod som aktiverar **lockDoor**, som kan anropas av lösningens backend-enhet.

* Skapa en Node.js-konsolapp som anropar **metoden lockDoor** direct i den simulerade enhetsappen med hjälp av ett jobb och uppdaterar önskade egenskaper med hjälp av ett enhetsjobb.

I slutet av den här självstudien har du två Node.js appar:

* **simDevice.js**, som ansluter till din IoT-hubb med enhetsidentiteten och tar emot en **direkt lockDoor-metod.**

* **scheduleJobService.js**, som anropar en direktmetod i den simulerade enhetsappen och uppdaterar enhetstvillingens önskade egenskaper med hjälp av ett jobb.

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0.x eller senare. [Förbereda utvecklingsmiljön beskriver hur](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) du installerar Node.js den här självstudien i Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här artikeln använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du Node.js-konsolapp som svarar på en direktmetod som anropas av molnet, som utlöser en **simulerad lockDoor-metod.**

1. Skapa en ny tom mapp med **namnet simDevice**.  I mappen **simDevice** skapar du en package.jspå filen med följande kommando i kommandotolken.  Acceptera alla standardvärden:

   ```console
   npm init
   ```

2. I kommandotolken i **mappen simDevice** kör du följande kommando för att installera **SDK-paketet azure-iot-device** och **paketet azure-iot-device-mqtt:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Med en textredigerare skapar du en **simDevice.js** fil i **mappen simDevice.**

4. Lägg till följande "require"-instruktioner  i början avsimDevice.jsfilen:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**. Ersätt `{yourDeviceConnectionString}` platshållarvärdet med enhetsanslutningssträngen som du kopierade tidigare.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att hantera **lockDoor-metoden.**

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

7. Lägg till följande kod för att registrera hanteraren för **lockDoor-metoden.**

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

8. Spara och stäng **simDevice.js** filen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Hantering av tillfälliga fel.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta IoT Hub-anslutningssträngen

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för att anropa en direktmetod och uppdatera en enhetstvillings egenskaper

I det här avsnittet skapar du en Node.js-konsolapp som initierar en **fjärrlåsningsdörr** på en enhet med en direktmetod och uppdaterar enhetstvillingens egenskaper.

1. Skapa en ny tom mapp med namnet **scheduleJobService**.  I mappen **scheduleJobService** skapar du en package.jsfil med följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```console
    npm init
    ```

2. I kommandotolken i mappen **scheduleJobService** kör du följande kommando för att installera **sdk-paketet azure-iothub** Device och **paketet azure-iot-device-mqtt:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Använd en textredigerare och skapa en **nyscheduleJobService.js** i mappen **scheduleJobService.**

4. Lägg till följande "require"-instruktioner  i början avscheduleJobService.jsfilen:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Lägg till följande variabeldeklarationer. Ersätt `{iothubconnectionstring}` platshållarvärdet med det värde som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string). Om du har registrerat en annan enhet **än myDeviceId** måste du ändra den i frågevillkoret.

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

7. Lägg till följande kod för att schemalägga jobbet som anropar enhetsmetoden:
  
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

8. Lägg till följande kod för att schemalägga jobbet för att uppdatera enhetstvillingen:

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

9. Spara och stäng **scheduleJobService.js** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommandotolken i **mappen simDevice** kör du följande kommando för att börja lyssna efter direktmetoden för omstart.

    ```console
    node simDevice.js
    ```

2. I kommandotolken i mappen **scheduleJobService** kör du följande kommando för att utlösa jobben för att låsa dörren och uppdatera tvillingen

    ```console
    node scheduleJobService.js
    ```

3. Du ser enhetssvaret på direktmetoden och jobbstatusen i -konsolen.

   Nedan visas enhetssvaret på direktmetoden:

   ![Utdata från simulerad enhetsapp](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Följande visar tjänstschemaläggningsjobben för direktmetoden och enhetstvillingens uppdatering och jobben som körs till slutförande:

   ![Köra den simulerade enhetsappen](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direktmetod till en enhet och uppdateringen av enhetstvillingens egenskaper.

Om du vill fortsätta att komma igång med IoT Hub och enhetshanteringsmönster, till exempel fjärruppdatering av inbyggd programvara via luften, kan du gå till Självstudie: Så här gör du en uppdatering [av den inbyggda programvaran.](tutorial-firmware-update.md)

Om du vill fortsätta komma igång IoT Hub i [Komma igång med Azure IoT Edge](../iot-edge/quickstart-linux.md).
