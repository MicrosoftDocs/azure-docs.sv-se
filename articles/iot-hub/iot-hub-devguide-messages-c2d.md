---
title: Förstå Azure IoT Hub meddelanden från moln till enhet | Microsoft Docs
description: Den här utvecklarguiden beskriver hur du använder moln-till-enhet-meddelanden med din IoT-hubb. Den innehåller information om meddelandets livscykel och konfigurationsalternativ.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761789"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Skicka meddelanden från moln till enhet från en IoT-hubb

Om du vill skicka enkelvägsmeddelanden till en enhetsapp från din lösnings backend-enhet skickar du meddelanden från molnet till enheten från din IoT Hub till din enhet. En diskussion om andra alternativ för moln till enhet som stöds av Azure IoT Hub finns i Vägledning för kommunikation [från moln till enhet.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden från moln till enhet via en tjänstriktad slutpunkt, */messages/devicebound.* En enhet tar sedan emot meddelandena via en enhetsspecifik slutpunkt, */devices/{deviceId}/messages/devicebound*.

Om du vill rikta varje moln-till-enhet-meddelande till en  enskild enhet anger din IoT-hubb egenskapen till */devices/{deviceId}/messages/devicebound*.

Varje enhetskö innehåller högst 50 meddelanden från molnet till enheten. Om du vill försöka skicka fler meddelanden till samma enhet resulterar det i ett fel.

## <a name="the-cloud-to-device-message-life-cycle"></a>Livscykeln för meddelanden från moln till enhet

För att garantera leverans av meddelanden minst en gång bevarar din IoT-hubb meddelanden från moln till enhet i köer per enhet. För att IoT-hubben ska kunna ta bort meddelanden från kön måste enheterna uttryckligen bekräfta *slutförande*. Den här metoden garanterar återhämtning mot anslutnings- och enhetsfel.

Livscykeltillståndsdiagrammet visas i följande diagram:

![Livscykel för meddelanden från moln till enhet](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

När IoT Hub-tjänsten skickar ett meddelande till en enhet anger tjänsten meddelandetillståndet *till Enqueued*. När en enhet vill *ta emot* ett meddelande låser IoT-hubben meddelandet genom att ställa in tillståndet på *Osynlig.*  Med det här tillståndet kan andra trådar på enheten börja ta emot andra meddelanden. När en enhetstråd slutför bearbetningen av ett meddelande meddelar den IoT-hubben genom *att slutföra* meddelandet. IoT-hubben ställer sedan in tillståndet till *Slutfört.*

En enhet kan också:

* *Avvisa* meddelandet, vilket gör att IoT-hubben ställer in det på tillståndet *Dead lettered (Död bokstav).* Enheter som ansluter via Message Queuing MQTT-protokollet (Telemetry Transport) kan inte avvisa meddelanden från molnet till enheten.

* *Lämna* meddelandet, vilket gör att IoT-hubben lägger tillbaka meddelandet i kön, med tillståndet *inställt på Enqueued*. Enheter som ansluter via MQTT-protokollet kan inte lämna meddelanden från molnet till enheten.

En tråd kan misslyckas med att bearbeta ett meddelande utan att meddela IoT-hubben. I det här fallet övergår meddelanden automatiskt från osynligt tillstånd  tillbaka till tillståndet  *Iqueued* efter en timeout för synlighet (eller *timeout* för lås). Värdet för den här time out-tiden är en minut och kan inte ändras.

Egenskapen **för maximalt antal** leveranser på IoT-hubben avgör det maximala antalet gånger som ett meddelande kan övergå mellan *tillstånden Enqueued (Iqueued)* och Invisible *(Osynlig).* Efter det antalet övergångar anger IoT-hubben status för meddelandet till *Dead lettered (Död bokstav).* På samma sätt anger IoT-hubben tillståndet för ett meddelande till *Dead lettered efter* dess förfallotid. Mer information finns i [Time to live](#message-expiration-time-to-live).

Artikeln Så här skickar du meddelanden från [moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) visar hur du skickar meddelanden från molnet till enheten från molnet och tar emot dem på en enhet.

Normalt slutför en enhet ett moln-till-enhet-meddelande när förlust av meddelandet inte påverkar programlogiken. Ett exempel på detta kan vara när enheten har bevarat meddelandeinnehållet lokalt eller har kört en åtgärd. Meddelandet kan också medföra tillfällig information vars förlust inte skulle påverka programmets funktioner. Ibland kan du för långvariga uppgifter:

* Slutför meddelandet från moln till enhet när enheten har bevarat uppgiftsbeskrivningen i lokal lagring.

* Meddela lösningens backend-enhet med ett eller flera enhet-till-moln-meddelanden i olika steg i uppgiftens förlopp.

## <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelande (time to live)

Varje moln-till-enhet-meddelande har en förfallotid. Den här tiden anges med något av följande:

* Egenskapen **ExpiryTimeUtc** i tjänsten
* IoT-hubben använder standardvärdet *time to live* som anges som en IoT-hubbegenskap

Se [Konfigurationsalternativ för moln till enhet.](#cloud-to-device-configuration-options)

Ett vanligt sätt att dra nytta av att ett meddelande upphör att gälla och undvika att skicka meddelanden till frånkopplade enheter är att ange värden *för kort* tid till live. Den här metoden uppnår samma resultat som att upprätthålla enhetens anslutningstillstånd, men det är mer effektivt. När du begär bekräftelser av meddelanden meddelar IoT-hubben dig vilka enheter som är:

* Kan ta emot meddelanden.
* Är inte online eller har misslyckats.

## <a name="message-feedback"></a>Feedback om meddelanden

När du skickar ett moln-till-enhet-meddelande kan tjänsten begära leverans av feedback per meddelande om det slutliga tillståndet för meddelandet. Du gör detta genom att ange programegenskapen **iothub-ack** i meddelandet från molnet till enheten som skickas till något av följande fyra värden:

| Egenskapsvärde för Ack | Beteende |
| ------------ | -------- |
| inget     | IoT-hubben genererar inget feedbackmeddelande (standardbeteende). |
| positivt | Om meddelandet från moln till enhet når tillståndet *Slutfört* genererar IoT-hubben ett feedbackmeddelande. |
| negativt | Om moln-till-enhet-meddelandet når tillståndet *Dead lettered* (Dödbokstav) genererar IoT-hubben ett feedbackmeddelande. |
| fullständig     | IoT-hubben genererar ett feedbackmeddelande i båda fallen. |

Om **Ack-värdet** är *fullt* och du inte får något feedbackmeddelande innebär det att feedbackmeddelandet har upphört att gälla. Tjänsten kan inte veta vad som hände med det ursprungliga meddelandet. I praktiken bör en tjänst se till att den kan bearbeta feedbacken innan den upphör att gälla. Den maximala förfallotiden är två dagar, vilket lämnar tiden för att få igång tjänsten igen om ett fel inträffar.

Enligt förklaringen i [Slutpunkter](iot-hub-devguide-endpoints.md)levererar IoT-hubben feedback via en tjänstriktad slutpunkt, */messages/servicebound/feedback*, som meddelanden. Semantiken för att ta emot feedback är samma som för meddelanden från moln till enhet. När det är möjligt batchas meddelandefeedback i ett enda meddelande, med följande format:

| Egenskap     | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | En tidsstämpel som anger när feedbackmeddelandet togs emot av hubben |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

Systemet skickar feedback antingen när batchen når 64 meddelanden eller inom 15 sekunder från det senast skickade, beroende på vilket som kommer först. 

Brödtexten är en JSON-serialiserad matris med poster, var och en med följande egenskaper:

| Egenskap           | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | En tidsstämpel som anger när resultatet av meddelandet inträffade (till exempel när hubben tog emot feedbackmeddelandet eller det ursprungliga meddelandet upphörde) |
| OriginalMessageId  | MessageId *för* det moln-till-enhet-meddelande som denna feedbackinformation relaterar till |
| StatusCode         | En obligatorisk sträng som används i feedbackmeddelanden som genereras av IoT-hubben: <br/> *Klart* <br/> *Upphört* <br/> *DeliveryCountExceeded* <br/> *Avslagen* <br/> *Renade* |
| Description        | Strängvärden för *StatusCode* |
| DeviceId           | *DeviceId för* målenhetens moln-till-enhet-meddelande som den här feedbacken gäller |
| DeviceGenerationId | *DeviceGenerationId* för målenhetens moln-till-enhet-meddelande som den här feedbacken gäller |

För att moln-till-enhet-meddelandet ska korrelera sin feedback med det ursprungliga meddelandet måste tjänsten ange ett *MessageId*.

Brödtexten i ett feedbackmeddelande visas i följande kod:

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

När en enhet tas bort tas även eventuell väntande feedback bort. Enhetsfeedback skickas i batchar. Om en enhet tas bort i det smala fönstret (ofta mindre än 1 sekund) mellan när enheten bekräftar mottagandet av meddelandet och när nästa feedbackbatch förbereds sker inte feedbacken.

Du kan åtgärda det här beteendet genom att vänta en stund på att väntande feedback tas emot innan du tar bort enheten. Relaterad meddelandefeedback bör antas gå förlorad när en enhet tas bort.

## <a name="cloud-to-device-configuration-options"></a>Konfigurationsalternativ för moln till enhet

Varje IoT-hubb visar följande konfigurationsalternativ för meddelanden från moln till enhet:

| Egenskap                  | Beskrivning | Intervall och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för meddelanden från moln till enhet | ISO_8601 upp till 2 dagar (minst 1 minut); standard: 1 timme |
| maxDeliveryCount          | Maximalt antal leveranser för moln-till-enhet per enhet-köer | 1 till 100; standard: 10 |
| feedback.ttlAsIso8601     | Kvarhållning för tjänstbundna feedbackmeddelanden | ISO_8601 upp till 2 dagar (minst 1 minut); standard: 1 timme |
| feedback.maxDeliveryCount | Maximalt antal leveranser för feedbackkön | 1 till 100; standard: 10 |
| feedback.lockDurationAsIso8601 | Maximalt antal leveranser för feedbackkön | ISO_8601 mellan 5 och 300 sekunder (minst 5 sekunder); standard: 60 sekunder. |

Du kan ange konfigurationsalternativ på något av följande sätt:

* **Azure Portal:** Under **Inställningar** på din IoT-hubb väljer **du Inbyggda slutpunkter** och **expanderar Meddelanden från moln till enhet.** (Det finns för närvarande inte stöd för att ange egenskaperna **feedback.maxDeliveryCount** och **feedback.lockDurationAsIso8601** i Azure Portal.)

    ![Ange konfigurationsalternativ för meddelanden från moln till enhet i portalen](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI:** Använd [kommandot az iot hub update:](/cli/azure/iot/hub#az_iot_hub_update)

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

Information om de SDK:er som du kan använda för att ta emot meddelanden från molnet till enheten finns i [Azure IoT-SDK:er.](iot-hub-devguide-sdks.md)

Om du vill prova att ta emot meddelanden från moln till enhet kan du gå [självstudien Skicka moln till](iot-hub-csharp-csharp-c2d.md) enhet.
