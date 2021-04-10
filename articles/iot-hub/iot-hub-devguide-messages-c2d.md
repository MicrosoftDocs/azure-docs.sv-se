---
title: Förstå Azure IoT Hub meddelanden från molnet till enheten | Microsoft Docs
description: Den här guiden beskriver hur du använder meddelanden från molnet till enheten med IoT Hub. Den innehåller information om meddelandets livs cykel och konfigurations alternativ.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 154b496a6c14d307c09ddcd1b42bf4ba568cb315
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607899"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Skicka meddelanden från moln till enhet från en IoT-hubb

Skicka meddelanden från moln till enhet från din IoT Hub till din enhet om du vill skicka enkelriktade meddelanden till en enhets app från Server delen i lösningen. En beskrivning av andra moln-till-enhet-alternativ som stöds av Azure IoT Hub finns i [rikt linjer för kommunikation mellan moln och enheter](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden från molnet till enheten via en */Messages/devicebound*-slutpunkt. En enhet tar sedan emot meddelandena via en enhets bestämd slut punkt, */Devices/{deviceId}/Messages/devicebound*.

För att rikta in varje moln-till-enhet-meddelande på en enskild enhet ställer IoT Hub **in på-egenskapen till** */Devices/{deviceId}/Messages/devicebound*.

Varje enhets kön innehåller högst 50 meddelanden från molnet till enheten. För att försöka skicka fler meddelanden till samma enhet resulterar det i ett fel.

## <a name="the-cloud-to-device-message-life-cycle"></a>Livs cykeln för meddelande från moln till enhet

För att garantera att meddelandet skickas minst en gång, är din IoT-hubb kvar meddelanden från molnet till enheten i köer för varje enhet. För att IoT-hubben ska ta bort meddelanden från kön måste enheterna uttryckligen bekräfta att de är *klara*. Den här metoden garanterar återhämtning mot anslutnings-och enhets problem.

Diagrammet livs cykel tillstånd visas i följande diagram:

![Livs cykel för meddelande från moln till enhet](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

När tjänsten IoT Hub skickar ett meddelande till en enhet, ställer tjänsten in meddelandets tillstånd i *kö*. När en enhet vill *ta emot* ett meddelande *låser* IoT-hubben meddelandet genom att ställa in läget på *osynligt*. Det här läget tillåter att andra trådar på enheten börjar ta emot andra meddelanden. När en enhets tråd slutför bearbetningen av ett meddelande, meddelas IoT-hubben genom att *slutföra* meddelandet. IoT Hub anger sedan statusen till *slutförd*.

En enhet kan också:

* *Avvisa* meddelandet, vilket leder till att IoT-hubben anger det som status för *obeställbara meddelanden* . Enheter som ansluter via MQTT-protokollet (Message Queuing telemetri transport) kan inte förkasta meddelanden från molnet till enheten.

* *Överge* meddelandet, vilket gör att IoT-hubben kan placera meddelandet i kön igen, med tillstånds inställningen *placerad i kö*. Enheter som ansluter via MQTT-protokollet kan inte överge meddelanden från molnet till enheten.

En tråd kunde inte bearbeta ett meddelande utan att meddela IoT-hubben. I det här fallet övergår meddelanden automatiskt från det *osynliga* läget tillbaka till läget i *kö* efter en *Synlighets* -timeout (eller *låsnings* -timeout). Värdet för denna timeout är en minut och kan inte ändras.

Egenskapen **Max Delivery Count** i IoT Hub avgör det maximala antalet gånger som ett meddelande kan övergå mellan tillståndet i *kö* och *osynligt* . Efter det antalet över gångar anger IoT-hubben status för meddelandet till *död brev*. På samma sätt anger IoT Hub statusen för ett meddelande till *obeställbara meddelanden* efter förfallo tiden. Mer information finns i [Time to Live](#message-expiration-time-to-live).

Avsnittet [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) visar hur du skickar meddelanden från moln till enhet från molnet och tar emot dem på en enhet.

En enhet kompletterar vanligt vis ett meddelande från molnet till enheten när meddelandets förlust inte påverkar program logiken. Ett exempel på detta kan vara när enheten har sparat meddelande innehållet lokalt eller har genomfört en åtgärd. Meddelandet kan också innehålla övergående information, vars förlust inte påverkar programmets funktioner. Ibland kan du för långvariga uppgifter:

* Slutför meddelandet från molnet till enheten när enheten har sparat aktivitets beskrivningen i den lokala lagringen.

* Meddela lösningens Server del med ett eller flera meddelanden från enheten till molnet i olika stadier av aktiviteten.

## <a name="message-expiration-time-to-live"></a>Förfallo datum för meddelande (Time to Live)

Varje meddelande från moln till enhet har en förfallo tid. Den här tiden anges av något av följande:

* Egenskapen **ExpiryTimeUtc** i tjänsten
* IoT Hub, genom att använda standard *tiden till Live* som har angetts som en IoT Hub-egenskap

Se [konfigurations alternativ för moln till enhet](#cloud-to-device-configuration-options).

Ett vanligt sätt att dra nytta av förfallo datum för meddelanden och undvika att skicka meddelanden till frånkopplade enheter är att ställa in kort *tid till Live* -värden. Den här metoden ger samma resultat som att bibehålla enhetens anslutnings tillstånd, men det är mer effektivt. När du begär meddelande bekräftelser, meddelar IoT-hubben vilka enheter som är:

* Kan ta emot meddelanden.
* Är inte online eller har misslyckats.

## <a name="message-feedback"></a>Feedback om meddelande

När du skickar ett meddelande från molnet till enheten kan tjänsten begära leverans av feedback per meddelande om det slutliga status meddelandet. Du gör detta genom att ange egenskapen **iothub-ack-** program i meddelandet från molnet till enheten som skickas till något av följande fyra värden:

| Bekräftelse egenskaps värde | Beteende |
| ------------ | -------- |
| inget     | IoT Hub genererar inget feedback-meddelande (standard beteende). |
| positivt | Om meddelandet från molnet till enheten når statusen *slutfört* , genererar IoT Hub ett feedback-meddelande. |
| negativt | Om meddelandet från molnet till enheten når status för *obeställbara meddelanden* genererar IoT Hub ett feedback-meddelande. |
| fullständig     | IoT Hub genererar ett feedback-meddelande i båda fallen. |

Om **ack** -värdet är *fullt* och du inte får något feedback-meddelande, innebär det att feedback-meddelandet har upphört att gälla. Tjänsten vet inte vad som hände med det ursprungliga meddelandet. I praktiken bör en tjänst se till att den kan bearbeta feedbacken innan den upphör att gälla. Den längsta förfallo tiden är två dagar, vilket lämnar tid för att få tjänsten att köras igen om ett fel uppstår.

Som förklaras i [slut punkter](iot-hub-devguide-endpoints.md)ger IoT Hub feedback via en tjänsteriktad slut punkt, */Messages/servicebound/feedback*, som meddelanden. Semantiken för att ta emot feedback är samma som för meddelanden från moln till enhet. När så är möjligt, är meddelandets feedback batch i ett enda meddelande med följande format:

| Egenskap     | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | En tidsstämpel som anger när feedback-meddelandet togs emot av hubben |
| UserId       | `{iot hub name}` |
| Innehålls  | `application/vnd.microsoft.iothub.feedback.json` |

Systemet skickar feedback antingen när batchen når 64 meddelanden eller i 15 sekunder från det senaste skickade, beroende på vilket som kommer först. 

Texten är en JSON-serialiserad matris med poster, var och en med följande egenskaper:

| Egenskap           | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | En tidsstämpel som visar när resultatet av meddelandet har inträffat (till exempel när hubben fick feedback-meddelandet eller att det ursprungliga meddelandet upphör att gälla) |
| OriginalMessageId  | *Messageid* för det meddelande från molnet till enheten som den här feedback-informationen avser |
| StatusCode         | En obligatorisk sträng som används i feedback-meddelanden som genereras av IoT Hub: <br/> *Klart* <br/> *Upphört* <br/> *DeliveryCountExceeded* <br/> *Avslagen* <br/> *Rensas* |
| Beskrivning        | Sträng värden för *StatusCode* |
| DeviceId           | *DeviceID* för mål enheten för det meddelande från molnet till enheten som den här återkopplingen avser |
| DeviceGenerationId | *DeviceGenerationId* för mål enheten för det moln-till-enhet-meddelande som den här återkopplingen avser |

För att ett meddelande från moln till enhet ska korreleras med det ursprungliga meddelandet måste tjänsten ange ett *messageid*.

Bröd texten i ett feedback-meddelande visas i följande kod:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Väntande feedback för borttagna enheter**

När en enhet tas bort raderas även eventuella väntande kommentarer. Feedback från enheten skickas i batchar. Om en enhet tas bort i det smala fönstret (ofta mindre än en sekund) mellan när enheten bekräftar att meddelandet har mottagits och när nästa feedback-batch förbereds, sker inte återkopplingen.

Du kan åtgärda det här beteendet genom att vänta en stund tills du får vänta på feedback innan du tar bort enheten. Relaterad feedback om meddelanden ska antas försvinna när en enhet tas bort.

## <a name="cloud-to-device-configuration-options"></a>Konfigurations alternativ för moln till enhet

Varje IoT-hubb exponerar följande konfigurations alternativ för meddelanden från molnet till enheten:

| Egenskap                  | Beskrivning | Intervall och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för meddelanden från moln till enhet | ISO_8601 intervall upp till två dagar (minst 1 minut); standard: 1 timme |
| maxDeliveryCount          | Maximalt antal leveranser för köer för moln-till-enhet per enhet | 1 till 100; standard: 10 |
| feedback. ttlAsIso8601     | Kvarhållning för service-kopplade e-postmeddelanden | ISO_8601 intervall upp till två dagar (minst 1 minut); standard: 1 timme |
| feedback. maxDeliveryCount | Maximalt antal leveranser för feedback-kön | 1 till 100; standard: 10 |
| feedback. lockDurationAsIso8601 | Maximalt antal leveranser för feedback-kön | ISO_8601 intervall mellan 5 och 300 sekunder (minst 5 sekunder); standard: 60 sekunder. |

Du kan ställa in konfigurations alternativen på något av följande sätt:

* **Azure Portal**: under **Inställningar** på din IoT-hubb väljer du **inbyggda slut punkter** och expanderar **molnet till enhets meddelanden**. (Du kan ange **feedback. maxDeliveryCount** och **feedback. lockDurationAsIso8601** egenskaper stöds inte för närvarande i Azure Portal.)

    ![Ange konfigurations alternativ för meddelanden från molnet till enheten i portalen](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Använd [uppdaterings kommandot för AZ IoT Hub](/cli/azure/iot/hub#az-iot-hub-update) :

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Nästa steg

Information om de SDK: er som du kan använda för att ta emot meddelanden från molnet till enheten finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

Om du vill prova att ta emot meddelanden från molnet till enheten läser du själv studie kursen [Skicka moln till enhet](iot-hub-csharp-csharp-c2d.md) .
