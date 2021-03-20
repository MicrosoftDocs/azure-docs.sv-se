---
title: Leverans och nya försök Azure Event Grid IoT Edge | Microsoft Docs
description: Leverans och försök igen i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171626"
---
# <a name="delivery-and-retry"></a>Leverans och nytt försök

Event Grid tillhandahåller varaktig leverans. Det försöker leverera varje meddelande minst en gång för varje matchande prenumeration direkt. Om en prenumerants slut punkt inte bekräftar mottagandet av en händelse eller om det uppstår ett fel, Event Grid försöker leverera igen baserat på ett fast **schema för omförsök** och **försök igen**.  Som standard skickar modulen Event Grid en händelse i taget till prenumeranten. Nytto lasten är dock en matris med en enda händelse. Du kan låta modulen leverera mer än en händelse i taget genom att aktivera batch-funktionen för utdata. Mer information om den här funktionen finns i [batching av utdata](delivery-output-batching.md).  

> [!IMPORTANT]
>Det finns inget beständigt stöd för händelse data. Det innebär att omdistribution eller omstart av Event Grid-modulen gör att du förlorar eventuella händelser som ännu inte har levererats.

## <a name="retry-schedule"></a>Schema för förnyat försök

Event Grid väntar upp till 60 sekunder innan ett meddelande har levererats. Om prenumerantens slut punkt inte ACK ger svar, kommer meddelandet att placeras i en av våra säkerhets kopierings köer för efterföljande återförsök.

Det finns två förkonfigurerade köer som avgör vilket schema som försök görs att försöka igen. De är:

| Schema | Beskrivning |
| ---------| ------------ |
| 1 minut | Meddelanden som visas här görs varje minut.
| 10 minuter | Meddelanden som visas här kommer att göras var tionde minut.

### <a name="how-it-works"></a>Så här fungerar det

1. Meddelandet tas emot i Event Grid-modulen. Försök görs att leverera det direkt.
1. Om leveransen Miss lyckas placeras meddelandet i kö i 1 minut och provas igen efter en minut.
1. Om leveransen fortsätter att fungera hamnar meddelandet i kö i 10 minuter och provas var 10: e minut.
1. Leveranser görs till dess att de slutförda eller återförsöks princip gränserna har nåtts.

## <a name="retry-policy-limits"></a>Princip begränsningar för återförsök

Det finns två konfigurationer som avgör princip för återförsök. De är:

* Maximalt antal försök
* TTL (Time-to-Live) för händelse

En händelse tas bort om någon av gränserna för principen för återförsök har nåtts. Schemat för förnyat försök beskrivs i avsnittet schema för förnyat försök. Konfigurationen av dessa gränser kan göras antingen för alla prenumeranter eller per prenumeration. I följande avsnitt beskrivs var och en ytterligare information.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurera standardvärden för alla prenumeranter

Det finns två egenskaper: `brokers__defaultMaxDeliveryAttempts` och `broker__defaultEventTimeToLiveInSeconds` som kan konfigureras som en del av event Grid-distributionen, som kontrollerar princip inställningarna för återförsök för alla prenumeranter.

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärde: 30.
| `broker__defaultEventTimeToLiveInSeconds` | TTL för händelse i sekunder efter vilken en händelse tas bort om den inte levereras. Standardvärde: **7200** sekunder

## <a name="configuring-defaults-per-subscriber"></a>Konfigurera standardinställningar per prenumerant

Du kan också ange en princip gräns för återförsök per prenumeration.
I vår [API-dokumentation](api.md) finns information om hur du konfigurerar standardinställningar per prenumerant. Standardvärden för prenumerations nivåer åsidosätter modulens nivå konfiguration.

## <a name="examples"></a>Exempel

I följande exempel konfigureras princip för återförsök i Event Grid-modulen med maxNumberOfAttempts = 3 och händelse-TTL på 30 minuter

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

I följande exempel konfigureras en Web Hook-prenumeration med maxNumberOfAttempts = 3 och händelse-TTL på 30 minuter

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
