---
title: Förstå priser för Azure IoT Hub | Microsoft Docs
description: Guide för utvecklare – information om hur mätning och prissättning fungerar med IoT Hub inklusive bearbetade exempel.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81729148"
---
# <a name="azure-iot-hub-pricing-information"></a>Pris information om Azure IoT Hub

[Azure IoT Hub-prissättning](https://azure.microsoft.com/pricing/details/iot-hub) tillhandahåller allmän information om olika SKU: er och priser för IoT Hub. Den här artikeln innehåller ytterligare information om hur de olika IoT Hub funktionerna mäts som meddelanden genom IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Avgifter per åtgärd

| Åtgärd | Fakturerings information | 
| --------- | ------------------- |
| Identitetsregisteråtgärder <br/> (skapa, Hämta, Visa, uppdatera, ta bort) | Debiteras inte. |
| Meddelanden från enheten till molnet | Meddelanden som skickas debiteras i 4 KB-segment vid ingångar i IoT Hub. Ett meddelande på 6 KB debiteras till exempel 2 meddelanden. |
| Meddelanden från moln till enhet | Meddelanden som har skickats debiteras i segment på 4 KB, till exempel ett meddelande på 6 KB debiteras 2 meddelanden. |
| Fil överföringar | Fil överföring till Azure Storage mäts inte av IoT Hub. Fil överförings-och slut för ande meddelanden debiteras som meddelande mätare i steg om 4 KB. Att överföra till exempel en 10 MB-fil debiteras som två meddelanden utöver Azure Storage kostnaden. |
| Direkta metoder | Lyckade metod begär Anden debiteras i segment med fyra KB och svaren debiteras i segment på 4 KB som ytterligare meddelanden. Begär anden till frånkopplade enheter debiteras som meddelanden i 4 KB-segment. Till exempel, en metod med en text i 4 KB som resulterar i ett svar utan bröd text från enheten debiteras som två meddelanden. En metod med en 6 KB-text som resulterar i ett svar på 1 KB från enheten debiteras som två meddelanden för begäran plus ett annat meddelande för svaret. |
| Enhet och modul, dubbla läspaket | Dubbla läsningar från enheten eller modulen och från lösningens Server del debiteras som meddelanden i segment om 512 byte. Till exempel debiteras en 6 KB-dubbel som 12 meddelanden. |
| Enhet och modul, dubbla uppdateringar (Taggar och egenskaper) | Dubbla uppdateringar från enheten eller modulen och från Server delen av lösningen debiteras som meddelanden i segment om 512 byte. Till exempel debiteras en 6 KB-dubbel som 12 meddelanden. |
| Enhet och modul, dubbla frågor | Frågor debiteras som meddelanden beroende på resultat storleken i 512 byte-segment. |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | Debiteras inte. |
| Jobb per enhet-åtgärder | Jobb åtgärder (till exempel dubbla uppdateringar och metoder) debiteras som normala. Ett jobb som resulterade i 1000-metod anrop med 1 KB-begäranden och tomma bröd texts svar debiteras till exempel 1000 meddelanden. |
| Keep-Alive-meddelanden | När du använder AMQP-eller MQTT-protokoll debiteras inte meddelanden som utbyts för att upprätta anslutningen och meddelanden som utbyts i förhandlingen. |

> [!NOTE]
> Alla storlekar beräknas om nytto lastens storlek i byte (protokoll ram ignoreras). För meddelanden som har egenskaper och brödtext, beräknas storleken på ett protokoll oberoende sätt. Mer information finns i [IoT Hub meddelande format](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exempel #1

En enhet skickar ett meddelande på 1 1 KB-enhet till molnet per minut till IoT Hub, som sedan läses av Azure Stream Analytics. Lösningens Server del anropar en metod (med en 512 byte-nytto Last) på enheten var 10: e minut för att utlösa en speciell åtgärd. Enheten svarar på-metoden med ett resultat på 200 byte.

Enheten förbrukar:

* Ett meddelande * 60 minuter * 24 timmar = 1440 meddelanden per dag för meddelanden från enhet till moln.
* Två begäran plus svar * 6 gånger per timme * 24 timmar = 288 meddelanden för metoderna.

Den här beräkningen ger totalt 1728 meddelanden per dag.

## <a name="example-2"></a>Exempel #2

En enhet skickar 1 100 KB enhet-till-moln-meddelande varje timme. Den uppdaterar också sin enhet med 1 KB-nyttolaster var fjärde timme. Lösningens Server del, en gång per dag, läser den 14 KB enheten och uppdaterar den med 512 byte-nyttolaster för att ändra konfigurationer.

Enheten förbrukar:

* 25 (100 KB/4 KB) meddelanden * 24 timmar för meddelanden från enhet till moln.
* Två meddelanden (1 KB/0,5 KB) * sex gånger per dag för enhetens dubbla uppdateringar.

Den här beräkningen ger totalt 612 meddelanden per dag.

Lösningens Server del använder 28 meddelanden (14 KB/0,5 KB) för att läsa enheten, och ett meddelande för att uppdatera det, för totalt 29 meddelanden.

Totalt är enheten och lösningens Server del förbrukar 641 meddelanden per dag.
