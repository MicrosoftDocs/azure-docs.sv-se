---
title: Börja med Azure IoT Hub module Identity & modul, delad (Node.js)
description: 'Lär dig hur du skapar modul identitet och uppdatera modul dubbla med IoT SDK: er för Node.js.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 9de3f45a9d62a9d131583c133440ac8a311a468d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993228"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Kom igång med IoT Hub modulens identitet och modul (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Även om Azure IoT Hub enhets identitet och enhet dubbla aktiverar Server dels programmet för att konfigurera en enhet och ger insyn på enhetens villkor, ger modulens identitet och modul dubbla dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudien har du två Node.js-appar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.

* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

## <a name="prerequisites"></a>Förutsättningar

* Node.js version 10.0. x eller senare. [Förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhets identitet och en modul identitet i IoT Hub

I det här avsnittet skapar du en Node.js-app som skapar en enhets identitet och en modul identitet i identitets registret i din IoT-hubb. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet "identitets register" i [guiden för IoT Hub utvecklare](iot-hub-devguide-identity-registry.md). När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1. Skapa en katalog för att lagra din kod.

2. Inuti den katalogen måste du först köra **NPM init-y** för att skapa en tom package.jspå med standardinställningar. Det här är projekt filen för din kod.

3. Kör **NPM install-S Azure-iothub \@ -moduler – för hands version** för att installera service SDK i **node_modules** -underkatalogen.

    > [!NOTE]
    > Under katalog namnet node_modules använder Word-modulen för att betyda "ett Node-bibliotek". Termen här har inget att göra med IoT Hub moduler.

4. Skapa följande. js-fil i din katalog. Anropa det **add.js**. Kopiera och klistra in din Hubbs anslutnings sträng och Hub-namn.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Den här appen skapar en enhets identitet med ID **t myfirstdevice** och en modul identitet med ID **MyFirstModule** under enhet **t myfirstdevice**. (Om detta modul-ID redan finns i identitets registret hämtar koden bara den befintliga informationen om modulen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

Kör detta med Node add.js. Du får en anslutnings sträng för enhets identiteten och en annan för din modul identitet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Uppdatera modul dubbla med Node.js-enhets-SDK

I det här avsnittet skapar du en Node.js-app på din simulerade enhet som uppdaterar modulens dubbla rapporterade egenskaper.

1. **Hämta din moduls anslutnings sträng** – logga in på [Azure Portal](https://portal.azure.com/). Gå till din IoT Hub och klicka på IoT-enheter. Hitta T myfirstdevice, öppna den och se att myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

   ![Information om Azure-portalmodulen](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Precis som du gjorde i steget ovan, skapar du en katalog för enhets koden och använder NPM för att initiera den och installera enhets-SDK: n (**NPM install-S Azure-IoT-Device-AMQP \@ modules – för hands version**).

   > [!NOTE]
   > NPM-kommandot för installation kan vara långsamt. Vara patient, det tar upp massor av kod från paketets lagrings plats.

   > [!NOTE]
   > Om du ser ett fel som säger NPM ERR! register fel vid parsning av JSON, detta är säkert att ignorera. Om du ser ett fel som säger NPM ERR! register fel vid parsning av JSON, detta är säkert att ignorera.

3. Skapa en fil med namnet twin.js. Kopiera och klistra in din ID-sträng för modul.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Kör nu detta med kommandot **twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Sedan visas:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/quickstart-linux.md)