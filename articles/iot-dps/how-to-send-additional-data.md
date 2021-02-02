---
title: Överföra en nytto Last mellan enhets-och Azure Device Provisioning-tjänsten
description: Det här dokumentet beskriver hur du överför en nytto Last mellan enhets-och enhets etablerings tjänsten (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259527"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Så här överför du nytto laster mellan enheter och DPS
DPS behöver ibland mer data från enheter för att korrekt etablera dem till rätt IoT Hub och dessa data måste tillhandahållas av enheten. Vice versa kan DPS returnera data till enheten för att under lätta klient sidans logik. 

## <a name="when-to-use-it"></a>När du ska använda detta
Den här funktionen kan användas som en förbättring för [anpassad allokering](./how-to-use-custom-allocation-policies.md). Till exempel vill du allokera dina enheter baserat på enhets modellen utan mänsklig inblandning. I det här fallet ska du använda [anpassad allokering](./how-to-use-custom-allocation-policies.md). Du kan konfigurera enheten för att rapportera modell informationen som en del av det [registrerade enhets anropet](/rest/api/iot-dps/runtimeregistration/registerdevice). DPS kommer att skicka enhetens nytto Last till den anpassade allokeringen av webhook. Och din funktion kan bestämma vilken IoT Hub enheten ska gå till när den tar emot information om enhets modellen. Om webhooken vill returnera data till enheten, kommer den att skicka tillbaka data som en sträng i webhook-svaret.  

## <a name="device-sends-data-payload-to-dps"></a>Enheten skickar data nytto Last till DPS
När din enhet skickar ett [register för enhets anrop](/rest/api/iot-dps/runtimeregistration/registerdevice) till DPS kan register samtalet förbättras för att ta andra fält i bröd texten. Texten ser ut ungefär så här: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS returnerar data till enheten
Om webhooken för den anpassade allokeringsregeln vill returnera data till enheten, kommer de att skickas tillbaka till en sträng i webhook-svaret. Ändringen anges i avsnittet nytto Last nedan. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-support
Den här funktionen är tillgänglig i C-, C#-, JAVA-och Node.js- [klient-SDK](./index.yml): er.  

## <a name="next-steps"></a>Nästa steg
* Utveckla med [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning service