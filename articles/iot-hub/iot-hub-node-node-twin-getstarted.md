---
title: Kom igång med Azure IoT Hub enhetstvillingarna (Node) | Microsoft Docs
description: Hur du använder Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT-SDK:er för Node.js för att implementera den simulerade enhetsappen och en tjänstapp som lägger till taggarna och kör IoT Hub frågan.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2047de2c1e61554ba96af8471905a22291861184
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484582"
---
# <a name="get-started-with-device-twins-nodejs"></a>Kom igång med enhetstvillingarna (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Node.js konsolappar:

* **AddTagsAndQuery.js**, en Node.js-backend-app som lägger till taggar och frågar enhetstvillingarna.

* **TwinSimulatedDevice.js**, en Node.js-app, som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare och rapporterar dess anslutningsvillkor.

> [!NOTE]
> Artikeln om [Azure IoT-SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT-SDK:er som du kan använda för att skapa både enhets- och backend-appar.
>

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Node.js version 10.0.x eller senare.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här artikeln använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta IoT Hub-anslutningssträngen

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en Node.js-konsolapp som lägger till platsmetadata till enhetstvillingen som är associerad **med myDeviceId**. Den frågar sedan enhetstvillingarna som lagras i IoT-hubben genom att välja de enheter som finns i USA och sedan de som rapporterar en mobilanslutning.

1. Skapa en ny tom mapp med **namnet addtagsandqueryapp**. I mappen **addtagsandqueryapp** skapar du en package.jsfil med följande kommando i kommandotolken. Parametern `--yes` accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. I kommandotolken i **mappen addtagsandqueryapp** kör du följande kommando för att installera **paketet azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Med en textredigerare skapar du en ny **AddTagsAndQuery.js** i **mappen addtagsandqueryapp.**

4. Lägg till följande  kod iAddTagsAndQuery.jsfilen. Ersätt `{iot hub connection string}` med den IoT Hub-anslutningssträng som du kopierade [i Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Registry-objektet** exponerar alla metoder som krävs för att interagera med enhetstvillingarna från tjänsten. Den tidigare koden initierar  först Registry-objektet, hämtar sedan enhetstvillingen för **myDeviceId** och uppdaterar slutligen dess taggar med önskad platsinformation.

    När taggarna har uppdaterats anropas **funktionen queryTwins.**

5. Lägg till följande kod i slutet av **AddTagsAndQuery.js** implementera **funktionen queryTwins:**

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Föregående kod kör två frågor: den första väljer endast enhetstvillingarna för enheter som finns på **Redmond43-anläggningen,** och den andra förfinar frågan så att endast de enheter som också är anslutna via mobilnät markeras.

    När koden skapar **frågeobjektet** anger den det maximala antalet returnerade dokument i den andra parametern. Frågeobjektet innehåller en **boolesk hasMoreResults-egenskap** som du kan använda för att anropa **nextAsTwin-metoderna** flera gånger för att hämta alla resultat.  En metod som **heter next** är tillgänglig för resultat som inte är enhetstvillingarna, till exempel resultatet av sammansättningsfrågor.

6. Kör programmet med:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för frågan som begränsar resultatet till enheter som använder ett mobilnät.

   ![Se en enhet i frågeresultatet](media/iot-hub-node-node-twin-getstarted/service1.png)

I nästa avsnitt skapar du en enhetsapp som rapporterar anslutningsinformationen och ändrar resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en Node.js-konsolapp som ansluter till din hubb som **myDeviceId** och uppdaterar sedan enhetstvillingens rapporterade egenskaper så att den innehåller informationen om att den är ansluten via ett mobilnät.

1. Skapa en ny tom mapp med namnet **reportconnectivity**. I mappen **reportconnectivity** skapar du en ny fil package.jsmed hjälp av följande kommando i kommandotolken. Parametern `--yes` accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. I kommandotolken i **mappen reportconnectivity** kör du följande kommando för att installera **paketen azure-iot-device** och **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en ny filReportConnectivity.js **i** mappen **reportconnectivity** med hjälp av en textredigerare.

4. Lägg till följande  kod iReportConnectivity.jsfilen. Ersätt med enhetsanslutningssträngen som du kopierade när du skapade `{device connection string}` **enhetsidentiteten myDeviceId** i Registrera en ny enhet [i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    **Klientobjektet** exponerar alla metoder som du behöver för att interagera med enhetstvillingarna från enheten. När den tidigare koden har  initierat klientobjektet hämtar den enhetstvillingen för **myDeviceId** och uppdaterar den rapporterade egenskapen med anslutningsinformationen.

5. Kör enhetsappen

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Du bör se meddelandet `twin state reported`.

6. Nu när enheten har rapporterat sin anslutningsinformation bör den visas i båda frågorna. Gå tillbaka i mappen **addtagsandqueryapp** och kör frågorna igen:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Den här **gången bör myDeviceId** visas i båda frågeresultaten.

    ![Visa myDeviceId i båda frågeresultaten](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du lade till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp för att rapportera enhetens anslutningsinformation i enhetstvillingen. Du har också lärt dig hur du frågar efter den här informationen med hjälp IoT Hub SQL-liknande frågespråk.

Använd följande resurser för att lära dig hur du:

* skicka telemetri från enheter med [självstudien Kom igång med IoT Hub,](quickstart-send-telemetry-node.md)

* konfigurera enheter med enhetstvillingens önskade egenskaper med självstudien [Använd önskade egenskaper för att konfigurera](tutorial-device-twins.md) enheter

* kontrollera enheter interaktivt (till exempel aktivera en fläkt från en användarstyrd app) med självstudien [Använda direktmetoder.](quickstart-control-device-node.md)
