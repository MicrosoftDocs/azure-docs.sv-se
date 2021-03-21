---
title: Azure IoT Hub MQTT 5 API-referens (för hands version)
description: Läs mer om IoT Hub MQTT 5 API-referens
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603561"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>API-referens för IoT Hub data plan MQTT 5

Det här dokumentet definierar åtgärder som är tillgängliga i version 2,0 (API-version: `2020-10-01-preview` ) för IoT Hub data Plans-API.

## <a name="operations"></a>Operations

### <a name="get-twin"></a>Få dubbla

Hämta dubbla tillstånd

#### <a name="request"></a>Förfrågan

**Ämnes namn:**`$iothub/twin/get`

**Egenskaper**: ingen

**Nytto Last**: Tom

#### <a name="success-response"></a>Lyckat svar

**Egenskaper**: ingen

**Nytto Last**: dubbla

#### <a name="alternative-responses"></a>Alternativa svar

| Status | Name | Beskrivning |
| :----- | :--- | :---------- |
| 0100 |  Felaktig begäran | Åtgärds meddelandet har fel format och kan inte bearbetas. |
| 0101 |  Inte auktoriserad | Klienten har inte behörighet att utföra åtgärden. |
| 0102 |  Tillåts inte | Åtgärden är inte tillåten. |
| 0501 |  Begränsas | begär ande frekvensen är för hög per SKU |
| 0502 |  Kvoten överskreds | den dagliga kvoten per aktuell SKU har överskridits |
| 0601 |  Serverfel | internt Server fel |
| 0602 |  Tidsgräns | tids gränsen nåddes för åtgärden innan den kunde slutföras |
| 0603 |  Servern är upptagen | servern är upptagen |

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Uppdatering dubbla rapporteras

Snabb korrigering, dubbla rapporterat tillstånd

#### <a name="request"></a>Förfrågan

**Ämnes namn:**`$iothub/twin/patch/reported`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| IF-version | u64 | nej |  |

**Nytto Last**: TwinState

#### <a name="success-response"></a>Lyckat svar

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| version | u64 | ja | Version av rapporterat tillstånd efter att korrigeringen tillämpades |

**Nytto Last**: Tom

#### <a name="alternative-responses"></a>Alternativa svar

| Status | Name | Beskrivning |
| :----- | :--- | :---------- |
| 0104 |  Förhands villkor misslyckades | villkoret uppfylldes inte på grund av att begäran avbröts |
| 0100 |  Felaktig begäran | Åtgärds meddelandet har fel format och kan inte bearbetas. |
| 0101 |  Inte auktoriserad | Klienten har inte behörighet att utföra åtgärden. |
| 0102 |  Tillåts inte | Åtgärden är inte tillåten. |
| 0501 |  Begränsas | begär ande frekvensen är för hög per SKU |
| 0502 |  Kvoten överskreds | den dagliga kvoten per aktuell SKU har överskridits |
| 0601 |  Serverfel | internt Server fel |
| 0602 |  Tidsgräns | tids gränsen nåddes för åtgärden innan den kunde slutföras |
| 0603 |  Servern är upptagen | servern är upptagen |

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Ta emot kommandon

Ta emot och hantera kommandon

#### <a name="message"></a>Meddelande

**Ämnes namn:**`$iothub/commands`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| sekvens-nej | u64 | ja | Meddelandets ordnings nummer |
| i kö-tid | time | ja | Tidsstämpel för när meddelandet har matats in i systemet |
| leverans antal | u32 | ja | Antal gånger som meddelande leveransen försökte utföras |
| skapande tid | time | nej | Tidsstämpel för när meddelandet skapades (tillhandahålls av avsändaren) |
| meddelande-ID | sträng | nej | Meddelande identitet (tillhandahålls av avsändare) |
| användar-id | sträng | nej | Användar identitet (tillhandahålls av avsändare) |
| korrelations-id | sträng | nej | Korrelations identitet (tillhandahålls av avsändare) |
| Innehållstyp | sträng | nej | fastställer nytto lastens innehålls typ |
| innehålls kodning | sträng | nej | fastställer nytto lastens innehålls kodning |

**Nytto Last**: valfri byte-sekvens

#### <a name="success-acknowledgment"></a>Lyckad bekräftelse

Indikerar att kommandot accepterades för hantering av klienten

**Egenskaper**: ingen

**Nytto Last**: Tom

#### <a name="alternative-acknowledgments"></a>Alternativa bekräftelser

| Orsaks kod | Status | Name | Beskrivning |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Lämna | Indikerar att kommandot inte bearbetas just nu och bör omlevereras i framtiden. |
| 131 | 0100 | Avvisa | Indikerar att kommandot avvisas av klienten och bör inte försökas igen. |

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Ta emot direkta metoder

Ta emot och hantera direkta metod anrop

#### <a name="request"></a>Förfrågan

**Ämnes namn:**`$iothub/methods/{name}`

**Egenskaper**: ingen

**Nytto Last**: valfri byte-sekvens

#### <a name="success-response"></a>Lyckat svar

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| svars kod | u32 | ja |  |

**Nytto Last**: valfri byte-sekvens

#### <a name="alternative-responses"></a>Alternativa svar

| Status | Name | Beskrivning |
| :----- | :--- | :---------- |
| 06A0 |  Inte tillgänglig | Anger att klienten inte kan kontaktas via den här anslutningen. |

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Ta emot dubbla önskade tillstånds ändringar

Ta emot uppdateringar till den dubbla förvalda statusen

#### <a name="message"></a>Meddelande

**Ämnes namn:**`$iothub/twin/patch/desired`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| version | u64 | ja | Version av önskat tillstånd som matchar den här uppdateringen |

**Nytto Last**: TwinState

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Skicka telemetri

Publicera meddelande till telemetri Channel – EventHubs som standard eller någon annan slut punkt via konfigurering av routning.

#### <a name="message"></a>Meddelande

**Ämnes namn:**`$iothub/telemetry`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| Innehållstyp | sträng | nej | översätts till `content-type` system egenskap i publicerat meddelande |
| innehålls kodning | sträng | nej | översätts till `content-encoding` system egenskap i publicerat meddelande |
| meddelande-ID | sträng | nej | översätts till `message-id` system egenskap i publicerat meddelande |
| användar-id | sträng | nej | översätts till `user-id` system egenskap i publicerat meddelande |
| korrelations-id | sträng | nej | översätts till `correlation-id` system egenskap i publicerat meddelande |
| skapande tid | time | nej | översätts till `iothub-creation-time-utc` egenskap för skickat meddelande |

**Nytto Last**: valfri byte-sekvens

#### <a name="success-acknowledgment"></a>Lyckad bekräftelse

Meddelandet har publicerats till telemetri-kanalen

**Egenskaper**: ingen

**Nytto Last**: Tom

#### <a name="alternative-acknowledgments"></a>Alternativa bekräftelser

| Orsaks kod | Status | Name | Beskrivning |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Felaktig begäran | Åtgärds meddelandet har fel format och kan inte bearbetas. |
| 135 | 0101 | Inte auktoriserad | Klienten har inte behörighet att utföra åtgärden. |
| 131 | 0102 | Tillåts inte | Åtgärden är inte tillåten. |
| 131 | 0601 | Serverfel | internt Server fel |
| 151 | 0501 | Begränsas | begär ande frekvensen är för hög per SKU |
| 151 | 0502 | Kvoten överskreds | den dagliga kvoten per aktuell SKU har överskridits |
| 131 | 0602 | Tidsgräns | tids gränsen nåddes för åtgärden innan den kunde slutföras |
| 131 | 0603 | Servern är upptagen | servern är upptagen |

#### <a name="pseudo-code-sample"></a>Exempel på pseudo-kod

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Svar

### <a name="bad-request"></a>Felaktig begäran

Åtgärds meddelandet har fel format och kan inte bearbetas.

**Orsakskod:** `131`

**Status:**`0100`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="conflict"></a>Konflikt

Åtgärden är i konflikt med en annan pågående åtgärd.

**Orsakskod:** `131`

**Status:**`0103`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="not-allowed"></a>Tillåts inte

Åtgärden är inte tillåten.

**Orsakskod:** `131`

**Status:**`0102`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="not-authorized"></a>Inte auktoriserad

Klienten har inte behörighet att utföra åtgärden.

**Orsakskod:** `135`

**Status:**`0101`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |

**Nytto Last**: Tom

### <a name="not-found"></a>Hittades inte

den begärda resursen finns inte

**Orsakskod:** `131`

**Status:**`0504`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="not-modified"></a>Inte ändrat

Resursen ändrades inte baserat på det angivna villkoret.

**Orsakskod:** `0`

**Status:**`0001`

**Egenskaper**: ingen

**Nytto Last**: Tom

### <a name="precondition-failed"></a>Förhands villkor misslyckades

Villkoret uppfylldes inte på grund av att begäran avbröts

**Orsakskod:** `131`

**Status:**`0104`

**Egenskaper**: ingen

**Nytto Last**: Tom

### <a name="quota-exceeded"></a>Kvoten överskreds

den dagliga kvoten per aktuell SKU har överskridits

**Orsakskod:** `151`

**Status:**`0502`

**Egenskaper**: ingen

**Nytto Last**: Tom

### <a name="resource-exhausted"></a>Resurs uttömd

resursen har ingen kapacitet för att slutföra åtgärden

**Orsakskod:** `131`

**Status:**`0503`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="server-busy"></a>Servern är upptagen

servern är upptagen

**Orsakskod:** `131`

**Status:**`0603`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |

**Nytto Last**: Tom

### <a name="server-error"></a>Serverfel

internt Server fel

**Orsakskod:** `131`

**Status:**`0601`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |

**Nytto Last**: Tom

### <a name="target-failed"></a>Målet misslyckades

Målet svarade men svaret var ogiltigt eller felaktigt

**Orsakskod:** `131`

**Status:**`06A2`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="target-timeout"></a>Tids gräns för mål

tids gränsen nåddes i väntan på att målet skulle slutföra begäran

**Orsakskod:** `131`

**Status:**`06A1`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |
| orsak | sträng | nej | innehåller information om vad som specifikt är ogiltigt om meddelandet |

**Nytto Last**: Tom

### <a name="target-unavailable"></a>Målet är inte tillgängligt

Det går inte att nå målet för att slutföra begäran

**Orsakskod:** `131`

**Status:**`06A0`

**Egenskaper**: ingen

**Nytto Last**: Tom

### <a name="throttled"></a>Begränsas

begär ande frekvensen är för hög per SKU

**Orsakskod:** `151`

**Status:**`0501`

**Egenskaper**: ingen

**Nytto Last**: Tom

### <a name="timeout"></a>Tidsgräns

tids gränsen nåddes för åtgärden innan den kunde slutföras

**Orsakskod:** `131`

**Status:**`0602`

**Egenskaper**:

| Namn | Typ | Obligatorisk | Beskrivning |
| :--- | :--- | :------- | :---------- |
| spårnings-ID | sträng | nej | spårnings-ID för korrelation med ytterligare diagnostik för felet |

**Nytto Last**: Tom

