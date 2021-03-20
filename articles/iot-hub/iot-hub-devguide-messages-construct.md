---
title: Förstå Azures IoT Hub meddelande format | Microsoft Docs
description: Guide för utvecklare – beskriver formatet och förväntat innehåll i IoT Hub meddelanden.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6d6b7122963b51619f26b8d02a8be4ad39261afb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147695"
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT Hub-meddelanden

För att stödja sömlös interoperabilitet mellan protokoll, definierar IoT Hub ett gemensamt meddelande format för alla protokoll som riktar sig mot enheter. Detta meddelande format används för både [enhet-till-moln-routning](iot-hub-devguide-messages-d2c.md) och meddelanden från [moln till enhet](iot-hub-devguide-messages-c2d.md) . 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementerar enhets-till-moln-meddelanden med hjälp av ett mönster för strömnings meddelande hantering. IoT Hub s meddelanden från enhet till molnet är mer som [Event Hubs](../event-hubs/index.yml) *händelser* än [Service Bus](../service-bus-messaging/index.yml) *meddelanden* på att det finns en stor mängd händelser som passerar genom tjänsten som kan läsas av flera läsare.

Ett IoT Hub meddelande består av:

* En fördefinierad uppsättning *system egenskaper* enligt listan nedan.

* En uppsättning *program egenskaper*. En ord lista med sträng egenskaper som programmet kan definiera och komma åt, utan att du behöver deserialisera meddelande texten. IoT Hub ändrar aldrig dessa egenskaper.

* En täckande binär brödtext.

Egenskaps namn och värden får bara innehålla ASCII-tecken, plus ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` när du skickar meddelanden från enheten till molnet med hjälp av HTTPS-protokollet eller skickar meddelanden från moln till enhet.

Meddelanden från enhet till moln med IoT Hub har följande egenskaper:

* Meddelanden från enheten till molnet är hållbara och bevaras i en IoT Hubs standard **meddelanden/händelse** slut punkt i upp till sju dagar.

* Meddelanden från enheten till molnet kan vara högst 256 KB och kan grupperas i batchar för att optimera sändningar. Batchar kan vara högst 256 KB.

* IoT Hub tillåter inte godtycklig partitionering. Meddelanden från enheten till molnet partitioneras baserat på deras ursprungliga **deviceId**.

* Som förklaras i [kontrol lera åtkomsten till IoT Hub](iot-hub-devguide-security.md)kan IoT Hub aktivera autentisering per enhet och åtkomst kontroll.

* Du kan stämpla meddelanden med information som går in i program egenskaperna. Mer information finns i meddelande- [anrikninger](iot-hub-message-enrichments-overview.md).

Mer information om hur du kodar och avkodar meddelanden som skickas med olika protokoll finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>System egenskaper för **D2C** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Anges användaren?|Nyckelord för </br>routnings fråga|
| --- | --- | --- | --- |
| meddelande-ID |En användardefinierad identifierare för meddelandet som används för svar på begäran-svar. Format: en Skift läges känslig sträng (upp till 128 tecken) av ASCII 7-bitars alfanumeriska tecken + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  | Ja | messageId |
| iothub – enqueuedtime |Datum och tid då meddelandet från [enhet till molnet](iot-hub-devguide-d2c-guidance.md) togs emot av IoT Hub. | Inga | enqueuedTime |
| användar-id |Ett ID som används för att ange ursprung för meddelanden. När meddelanden genereras av IoT Hub har den angetts till `{iot hub name}` . | Ja | userId |
| iothub-Connection-Device-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **deviceId** för enheten som skickade meddelandet. | Inga | connectionDeviceId |
| iothub-Connection-module-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **moduleId** för enheten som skickade meddelandet. | Inga | connectionModuleId |
| iothub-Connection-auth-generation-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **connectionDeviceGenerationId** (som per [enhets identitets egenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties)) för enheten som skickade meddelandet. | Inga |connectionDeviceGenerationId |
| iothub-anslutnings-auth-Method |En autentiseringsmetod som anges av IoT Hub för meddelanden från enhet till moln. Den här egenskapen innehåller information om autentiseringsmetoden som används för att autentisera enheten som skickar meddelandet.| Inga | connectionAuthMethod |
| DT-dataschema | Det här värdet anges av IoT Hub på enhet-till-moln-meddelanden. Det innehåller det enhets modell-ID som angetts i enhets anslutningen. | Inga | Ej tillämpligt |
| DT-ämne | Namnet på komponenten som skickar meddelanden från enheten till molnet. | Ja | Ej tillämpligt |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>System egenskaper för **C2D** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Anges användaren?|
| --- | --- | --- |
| meddelande-ID |En användardefinierad identifierare för meddelandet som används för svar på begäran-svar. Format: en Skift läges känslig sträng (upp till 128 tecken) av ASCII 7-bitars alfanumeriska tecken + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  |Ja|
| sekvens-nummer |Ett tal (unikt per enhets kön) som tilldelas av IoT Hub till varje moln-till-enhet-meddelande. |Inga|
| på |Ett mål som anges i meddelanden från [moln till enhet](iot-hub-devguide-c2d-guidance.md) . |Inga|
| absolut-förfallo tid |Datum och tid då meddelandet upphör att gälla. |Inga|   |
| korrelations-id |En sträng egenskap i ett svarsmeddelande som vanligt vis innehåller MessageId för begäran, i mönster för begäran-svar. |Ja|
| användar-id |Ett ID som används för att ange ursprung för meddelanden. När meddelanden genereras av IoT Hub har den angetts till `{iot hub name}` . |Ja|
| iothub – ack |En generator för feedback-meddelande. Den här egenskapen används i meddelanden från molnet till enheten för att begära IoT Hub att generera återkopplings meddelanden som ett resultat av att meddelandet har använts av enheten. Möjliga värden: **ingen** (standard): inget feedback-meddelande har genererats, **positivt**: ta emot ett feedback-meddelande om meddelandet har slutförts, **negativt**: ta emot ett feedback-meddelande om meddelandet har upphört att gälla (eller maximalt antal leveranser nåddes) utan att ha slutförts av enheten, eller **full**: både positiva och negativa. |Ja|

### <a name="system-property-names"></a>System egenskaps namn

Systemets egenskaps namn varierar beroende på slut punkten som meddelandena dirigeras till. I tabellen nedan finns mer information om dessa namn.

|System egenskaps namn|Event Hubs|Azure Storage|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|Meddelande-ID|meddelande-ID|messageId|Meddelande|meddelande-ID|
|Användar-id|användar-id|userId|UserId|användar-id|
|Anslutnings enhets-ID|iothub-Connection-Device-ID| connectionDeviceId|iothub-Connection-Device-ID|iothub-Connection-Device-ID|
|ID för anslutnings modul|iothub-Connection-module-ID|connectionModuleId|iothub-Connection-module-ID|iothub-Connection-module-ID|
|Generations-ID för anslutnings auth|iothub-Connection-auth-generation-ID|connectionDeviceGenerationId| iothub-Connection-auth-generation-ID|iothub-Connection-auth-generation-ID|
|Metod för anslutnings verifiering|iothub-anslutnings-auth-Method|connectionAuthMethod|iothub-anslutnings-auth-Method|iothub-anslutnings-auth-Method|
|Innehålls|innehålls typ|Innehålls|Innehålls|iothub – innehålls typ|
|contentEncoding|innehålls kodning|contentEncoding|ContentEncoding|iothub – innehålls kodning|
|iothub – enqueuedtime|iothub – enqueuedtime|enqueuedTime| Ej tillämpligt |iothub – enqueuedtime|
|CorrelationId|korrelations-id|correlationId|CorrelationId|korrelations-id|
|DT-dataschema|DT-dataschema|DT-dataschema|DT-dataschema|DT-dataschema|
|DT-ämne|DT-ämne|DT-ämne|DT-ämne|DT-ämne|

## <a name="message-size"></a>Meddelandestorlek

IoT Hub mäter meddelande storleken på ett oberoende sätt, överväger bara den faktiska nytto lasten. Storleken i byte beräknas som summan av följande värden:

* Text storleken i byte.
* Storleken i byte på alla värden i meddelande systemets egenskaper.
* Storlek i byte för alla användar egenskaps namn och värden.

Egenskaps namn och värden är begränsade till ASCII-tecken, så längden på strängarna motsvarar storleken i byte.

## <a name="anti-spoofing-properties"></a>Egenskaper för skydd mot förfalskning

För att undvika enhets förfalskning i meddelanden från enhet till moln, IoT Hub stämplar alla meddelanden med följande egenskaper:

* **iothub-Connection-Device-ID**
* **iothub-Connection-auth-generation-ID**
* **iothub-anslutnings-auth-Method**

De två första innehåller den ursprungliga enhetens **deviceId** och **generationId** , som per [enhets identitets egenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties).

Egenskapen **iothub-Connection-auth-Method** innehåller ett JSON-serialiserat objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

* Information om storleks gränser för meddelanden i IoT Hub finns i [IoT Hub kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

* Information om hur du skapar och läser IoT Hub meddelanden i olika programmeringsspråk finns i [snabb starter](quickstart-send-telemetry-node.md).