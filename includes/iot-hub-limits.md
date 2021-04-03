---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "82204467"
---
I följande tabell visas de gränser som är associerade med de olika tjänst nivåerna S1, S2, S3 och F1. Information om kostnaden för varje *enhet* i varje nivå finns i [Azure IoT Hub prissättning](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resurs | S1 Standard | S2 Standard | S3 Standard | F1 Kostnadsfri |
| --- | --- | --- | --- | --- |
| Meddelanden per dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximalt antal enheter |200 |200 |10 |1 |

> [!NOTE]
> Kontakta Microsoft Support om du förväntar dig att använda fler än 200 enheter med en S1-eller S2-nivå hubb eller 10 enheter med ett nav på S3-nivå.
> 
> 

I följande tabell visas de begränsningar som gäller för IoT Hub resurser.

| Resurs | Gräns |
| --- | --- |
| Maximalt antal betalda IoT-hubbar per Azure-prenumeration |50 |
| Maximalt antal kostnadsfria IoT-hubbar per Azure-prenumeration |1 |
| Maximalt antal tecken i ett enhets-ID | 128 |
| Maximalt antal enhetsidentiteter<br/> som returneras i ett enskilt anrop |1 000 |
| Maximal kvarhållning av IoT Hub-meddelanden för enhet-till-moln-meddelanden |7 dagar |
| Maximal storlek för enhet-till-moln-meddelande |256 kB |
| Maximal storlek för enhet-till-moln-batch |AMQP och HTTP: 256 KB för hela batchen <br/>MQTT: 256 KB för varje meddelande |
| Maximalt antal meddelanden för enhet-till-moln-batch |500 |
| Maximal storlek för moln-till-enhet-meddelande |64 kB |
| Maximalt TTL-värde för moln-till-enhet-meddelanden |2 dagar |
| Maximalt antal leveranser för moln-till-enhet- <br/> meddelanden |100 |
| Högsta antal köer för moln till enhet per enhet |50 |
| Maximalt antal leveranser för feedbackmeddelanden <br/> som svar på ett moln-till-enhet-meddelande |100 |
| Maximalt TTL-värde för meddelanden som <br/> svar på ett moln-till-enhet-meddelande |2 dagar |
| [Maximal storlek för enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB för Taggar-avsnittet och 32 KB för önskade och rapporterade egenskaper-avsnitt |
| Maximal längd på enhetens dubbla sträng nyckel | 1 kB |
| Maximal längd på enhetens dubbla sträng värde | 4 kB |
| [Maximalt djup för objekt i enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximal storlek på nyttolast för direkt metod | 128 kB |
| Maximal kvarhållning för jobbhistorik | 30 dagar |
| Maximalt antal samtidiga jobb | 10 (för S3), 5 (för S2), 1 (för S1) |
| Maximalt antal ytterligare slutpunkter | 10 (för S1, S2 och S3) |
| Maximalt antal regler för meddelandedirigering | 100 (för S1, S2 och S3) |
| Maximalt antal anslutna enhets strömmar som har anslutits | 50 (endast för S1, S2, S3 och F1) |
| Maximal data överföring för enhets ström | 300 MB per dag (endast för S1, S2, S3 och F1) |

> [!NOTE]
> Kontakta Microsoft Support om du behöver fler än 50 betalda IoT-hubbar i en Azure-prenumeration.

> [!NOTE]
> För närvarande är det totala antalet enheter plus moduler som kan registreras för en enda IoT Hub som är ett tak på 1 000 000. Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du vill öka den här gränsen.

IoT Hub begränsnings begär anden när följande kvoter överskrids.

| Begränsning | Värde per hubb |
| --- | --- |
| Identitetsregisteråtgärder <br/> (skapa, Hämta, Visa, uppdatera och ta bort), <br/> enskilda eller massimport/massexport |83.33 per sekund per enhet (5000 per minut/enhet) (för S3). <br/> 1.67 per sekund/enhet (100 per minut/enhet) (för S1 och S2). |
| Enhetsanslutningar |6000/SEK/enhet (för S3), 120/SEK/enhet (för S2), 12/SEK per enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar enhet-till-moln |6000/SEK/enhet (för S3), 120/SEK/enhet (för S2), 12/SEK per enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar moln-till-enhet | 83.33 per sekund per enhet (5000/min/enhet) (för S3), 1.67/SEK/enhet (100 per minut/enhet) (för S1 och S2). |
| Mottagningar moln-till-enhet |833.33 per sekund per enhet (50000/min/enhet) (för S3), 16.67/SEK/enhet (1000 per minut/enhet) (för S1 och S2). |
| Filöverföringsåtgärder |83,33 fil överförings initieringar/SEK per enhet (5000/min/enhet) (för S3), 1,67 fil överförings initieringar/SEK/enhet (100 per minut/enhet) (för S1 och S2). <br/> 10 000 SAS-URI: er kan ligga ut för ett Azure Storage-konto i taget.<br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Direkta metoder | 24 MB/SEK per enhet (för S3), 480 KB/SEK/enhet (för S2), 160 KB/SEK/enhet (för S1).<br/> Baserat på 8 KB begränsnings skalans begränsnings storlek. |
| Läsoperationer för enhetstvilling | 500 per sekund per enhet (för S3): högst 100 per sekund eller 10 per sekund per enhet (för S2), 100/SEK (för S1) |
| Uppdateringar för enhetstvilling | 250 per sekund per enhet (för S3), högst 50 per sekund eller 5/SEK per enhet (för S2), 50/SEK (för S1) |
| Jobbåtgärder <br/> (skapa, uppdatera, lista och ta bort) | 83.33 per sekund per enhet (5000/min/enhet) (för S3), 1.67/SEK/enhet (100 per minut/enhet) (för S2), 1.67/SEK/enhet (100 per minut/enhet) (för S1). |
| Jobb per enhetsåtgärd, dataflöde | 50 per sekund per enhet (för S3): högst 10 per sekund eller 1 per sekund per enhet (för S2), 10 per sekund (för S1). |
| Initierings takt för enhets strömmen | 5 nya strömmar/SEK (endast för S1, S2, S3 och F1). |
