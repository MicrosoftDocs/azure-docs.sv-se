---
title: Ladda upp filer från enheter till Azure IoT Hub med noden | Microsoft Docs
description: Ladda upp filer från en enhet till molnet med hjälp av Azure IoT Device SDK för Node.js. Överförda filer lagras i en BLOB-behållare för Azure Storage.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: db4f78e14696c421adaedd16b0b3f8d598f12846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251906"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Ladda upp filer från enheten till molnet med IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-node-node-c2d.md) själv studie kurs som visar hur du använder [fil överförings funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure Blob Storage](../storage/index.yml). Självstudien visar hur du:

* Tillhandahålla en enhet på ett säkert sätt en Azure Blob-URI för att ladda upp en fil.

* Använd IoT Hub fil överförings meddelanden för att utlösa bearbetning av filen i Server delen av din app.

[Genom att skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-node.md) -snabb start demonstreras de grundläggande meddelande funktionerna från enhet till moln i IoT Hub. I vissa fall kan du dock inte enkelt mappa de data som enheterna skickar till de relativt små enhets-till-moln-meddelanden som IoT Hub accepterar. Exempel:

* Stora filer som innehåller bilder
* Video
* Exempel på vibrations data med hög frekvens
* Någon form av förbehandlade data.

De här filerna bearbetas vanligt vis i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) -stacken. När du behöver använda filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

I slutet av den här självstudien kör du två Node.js-konsol program:

* **SimulatedDevice.js**, som laddar upp en fil till lagringen med hjälp av en SAS-URI från IoT Hub.

* **ReadFileUploadNotification.js**, som tar emot fil överförings meddelanden från din IoT Hub.

> [!NOTE]
> IoT Hub stöder många enhets plattformar och språk (inklusive C, .NET, Java Script, python och Java) via SDK: er för Azure IoT-enheter. Se [Azure IoT Developer Center] för stegvisa instruktioner om hur du ansluter enheten till Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0. x eller senare. [Förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhets app

I det här avsnittet skapar du Device-appen för att ladda upp en fil till IoT Hub.

1. Skapa en tom mapp med namnet ```simulateddevice```.  Skapa en package.json-fil i mappen ```simulateddevice``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Installera **azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt**-paketet genom att köra följande kommando i kommandotolken i mappen ```simulateddevice```:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en **SimulatedDevice.js**-fil i mappen ```simulateddevice``` med hjälp av en textredigerare.

4. Lägg till följande ```require```-instruktioner i början av **SimulatedDevice.js**-filen:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Lägg till en `deviceconnectionstring`-variabel och använd den för att skapa en **Client**-instans.  Ersätt `{deviceconnectionstring}` med namnet på den enhet som du skapade i avsnittet *skapa en IoT Hub* :

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > För enkelhetens skull är anslutnings strängen inkluderad i koden: Detta är inte en rekommenderad metod och beroende på din användnings fråga och arkitektur kanske du vill överväga säkrare sätt att lagra den här hemligheten.

6. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Skapa ett motanrop och Använd funktionen **uploadToBlob** för att ladda upp filen.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Spara och stäng filen **SimulatedDevice.js**.

9. Kopiera en avbildnings fil till `simulateddevice` mappen och Byt namn på den `myimage.png` .

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att ta emot meddelanden om fil överföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Om du vill ta emot meddelanden om fil överföring behöver tjänsten **tjänst anslutnings** behörighet. Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om fil uppladdning

I det här avsnittet skapar du en Node.js-konsolsession som tar emot meddelanden om fil överförings meddelanden från IoT Hub.

Du kan använda **iothubowner** -anslutningssträngen från din IoT Hub för att slutföra det här avsnittet. Du hittar anslutnings strängen i [Azure Portal](https://portal.azure.com/) på bladet princip för **delad åtkomst** .

1. Skapa en tom mapp med namnet ```fileuploadnotification```.  Skapa en package.json-fil i mappen ```fileuploadnotification``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommando tolken i ```fileuploadnotification``` mappen kör du följande kommando för att installera **Azure-iothub SDK-** paketet:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Skapa en **FileUploadNotification.js** -fil i mappen med hjälp av en text redigerare `fileuploadnotification` .

4. Lägg till följande- `require` instruktioner i början av **FileUploadNotification.js** -filen:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Lägg till en `iothubconnectionstring`-variabel och använd den för att skapa en **Client**-instans.  Ersätt `{iothubconnectionstring}` placeholder-värdet med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > För enkelhetens skull är anslutnings strängen inkluderad i koden: Detta är inte en rekommenderad metod och beroende på din användnings fråga och arkitektur kanske du vill överväga säkrare sätt att lagra den här hemligheten.

6. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Öppna klienten och Använd funktionen **getFileNotificationReceiver** för att ta emot status uppdateringar.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Spara och Stäng **FileUploadNotification.jss** filen.

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

Kör följande kommando i kommando tolken i `fileuploadnotification` mappen:

```cmd/sh
node FileUploadNotification.js
```

Kör följande kommando i kommando tolken i `simulateddevice` mappen:

```cmd/sh
node SimulatedDevice.js
```

Följande skärm bild visar utdata från **SimulatedDevice** -appen:

![Utdata från den simulerade enhets appen](./media/iot-hub-node-node-file-upload/simulated-device.png)

Följande skärm bild visar utdata från **FileUploadNotification** -appen:

![Utdata från Read-File-Upload-Notification-appen](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i den lagrings behållare som du konfigurerade:

![Överförd fil](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder fil överförings funktionerna i IoT Hub för att förenkla fil överföringar från enheter. Du kan fortsätta att utforska funktionerna och scenarierna i IoT Hub med följande artiklar:

* [Skapa en IoT Hub program mässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)
