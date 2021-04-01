---
title: Kom igång med Azure IoT Hub Device, dubbla (Node) | Microsoft Docs
description: 'Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT SDK: er för Node.js för att implementera den simulerade Device-appen och en service-app som lägger till taggarna och kör IoT Hubs frågan.'
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 65ced3812072bd2650fc36bbb7a7b0f3f75e0def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336793"
---
# <a name="get-started-with-device-twins-nodejs"></a>Kom igång med dubbla enheter (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Node.js-konsol program:

* **AddTagsAndQuery.js** en Node.js backend-app, som lägger till taggar och frågor enhets dubbla.

* **TwinSimulatedDevice.js** en Node.js-app, som simulerar en enhet som ansluter till din IoT Hub med enhets identiteten som skapades tidigare och rapporterar dess anslutnings tillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) : er innehåller information om Azure IoT SDK: er som du kan använda för att bygga både enhets-och backend-appar.
>

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Node.js version 10.0. x eller senare.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en Node.js-konsolsession som lägger till platsens metadata till den enhet som är kopplad till **myDeviceId**. Den frågar sedan enheten efter varandra i IoT Hub som väljer de enheter som finns i USA och sedan de som rapporterar en mobil anslutning.

1. Skapa en ny tom mapp med namnet **addtagsandqueryapp**. I mappen **addtagsandqueryapp** skapar du en ny package.jspå en fil med hjälp av följande kommando i kommando tolken. `--yes`Parametern accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. I kommando tolken i mappen **addtagsandqueryapp** kör du följande kommando för att installera paketet **Azure-iothub** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Med hjälp av en text redigerare skapar du en ny **AddTagsAndQuery.js** -fil i mappen **addtagsandqueryapp** .

4. Lägg till följande kod i **AddTagsAndQuery.js** -filen. Ersätt `{iot hub connection string}` med IoT Hub anslutnings strängen som du kopierade i [Hämta IoT Hub-anslutningssträngen](#get-the-iot-hub-connection-string).

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

    Objektet **Registry** visar alla de metoder som krävs för att interagera med enheten tillsammans från tjänsten. Föregående kod initierar först **Registry** -objektet och hämtar sedan enheten för **myDeviceId**, och uppdaterar slutligen dess Taggar med önskad plats information.

    Efter uppdatering av taggarna anropar funktionen **queryTwins** .

5. Lägg till följande kod i slutet av  **AddTagsAndQuery.js** för att implementera funktionen **queryTwins** :

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

    Föregående kod kör två frågor: först väljer den först enheten med dubbla enheter som finns i **Redmond43** -anläggningen, och den andra refinar frågan så att endast de enheter som också är anslutna via mobil nät verket återställs.

    När-koden skapar **frågespråket** anges det maximala antalet returnerade dokument i den andra parametern. **Frågespråket** innehåller en **hasMoreResults** Boolean-egenskap som du kan använda för att anropa **nextAsTwin** -metoderna flera gånger för att hämta alla resultat. En metod som heter **Next** är tillgänglig för resultat som inte är enhets dubbla, till exempel resultatet av agg regerings frågor.

6. Kör programmet med:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för den fråga som begränsar resultatet till enheter som använder ett mobil nät verk.

   ![Se den enda enheten i frågeresultatet](media/iot-hub-node-node-twin-getstarted/service1.png)

I nästa avsnitt skapar du en enhets app som rapporterar anslutnings informationen och ändrar resultatet för frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en Node.js-konsolsession som ansluts till hubben som **myDeviceId** och uppdaterar sedan dess enhets dubbla rapporter så att de innehåller den information som den är ansluten till med ett mobilt nätverk.

1. Skapa en ny tom mapp med namnet **reportconnectivity**. I mappen **reportconnectivity** skapar du en ny package.jspå en fil med hjälp av följande kommando i kommando tolken. `--yes`Parametern accepterar alla standardvärden.

    ```cmd/sh
    npm init --yes
    ```

2. I kommando tolken i mappen **reportconnectivity** kör du följande kommando för att installera paketen **Azure-IoT-Device** och **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Med hjälp av en text redigerare skapar du en ny **ReportConnectivity.js** -fil i mappen **reportconnectivity** .

4. Lägg till följande kod i **ReportConnectivity.js** -filen. Ersätt `{device connection string}` med enhets anslutnings strängen som du kopierade när du skapade **myDeviceId** enhets identitet i [Registrera en ny enhet i IoT Hub](#register-a-new-device-in-the-iot-hub).

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

    **Klient** objekt visar alla metoder som du behöver för att interagera med enheten tillsammans från enheten. Föregående kod, efter att den initierat **klient** -objektet, hämtar enheten med dubbla för **myDeviceId** och uppdaterar den rapporterade egenskapen med anslutnings informationen.

5. Köra appen Device

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Du bör se meddelandet `twin state reported`.

6. Nu när enheten rapporterade anslutnings information, bör den visas i båda frågorna. Gå tillbaka till mappen **addtagsandqueryapp** och kör frågorna igen:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Den här gången ska **myDeviceId** visas i båda frågeresultaten.

    ![Visa myDeviceId i båda frågeresultaten](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en simulerad Device-app för att rapportera enhetens anslutnings information på enheten. Du har också lärt dig hur du frågar den här informationen med hjälp av SQL-liknande IoT Hub frågespråk.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med själv studie kursen [komma igång med IoT Hub](quickstart-send-telemetry-node.md)

* konfigurera enheter med enhetens dubbla egenskaper med hjälp av självstudierna [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md)

* kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-node.md) .
