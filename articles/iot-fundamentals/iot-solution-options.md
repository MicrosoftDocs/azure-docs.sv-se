---
title: Lösnings alternativ för Azure Sakernas Internet (IoT)
description: Vägledning om hur du väljer mellan en plattforms tjänst eller en hanterad app Platform-metod för att skapa en IoT-lösning. Platform service-metoden använder tjänster som IoT Hub och digital, dubbla som bygg stenar. Den hanterade appens plattforms metod använder IoT Central för att snabbt komma igång.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 7ff8193e87d138878d95e783d144d445436e7a08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452999"
---
# <a name="what-is-the-right-iot-solution-for-your-business"></a>Vad är rätt IoT-lösning för ditt företag?

För att bygga en IoT-lösning för din verksamhet väljer du vanligt vis antingen *plattforms tjänsterna* eller den *hanterade appens plattforms* metod.

Plattforms tjänster tillhandahåller Bygg stenar för anpassade och flexibla IoT-program. Du har fler alternativ att välja och kod när du ansluter enheter, och du kan mata in, lagra och analysera dina data. Azure IoT Platform-tjänster omfattar de produkter som Azure IoT Hub och digitala Azure-datorer.

Med en hanterad app-plattform kan du komma igång med att skapa appar snabbare än plattforms tjänster genom att minska antalet beslut som behövs för att uppnå resultaten. Den hanterade App-plattformen tar hand om de flesta delar av lösningen, så att du kan fokusera på att lägga till bransch kunskap och skala och ansluta enheter. Azure IoT Central är en hanterad app-plattform.

Om du vill välja mellan dessa två metoder bör du tänka på följande:

- Hur du vill hantera din lösning.
- Vilken anpassnings nivå och kontroll du vill ha över din lösning.
- Vilken pris struktur du vill ha.

## <a name="management"></a>Hantering

Var vill du spendera din system hanterings tid och dina resurser? 

- Välj plattforms tjänstens metod för att få fullständig kontroll över de underliggande tjänsterna i din lösning. Till exempel vill du:

    - Hantera skalning och säkra tjänster så att de passar dina behov.
    - Använd interna eller partner kunskaper för att publicera enheter och tillhandahålla tjänster.

- Välj den hanterade appens plattforms metod för att dra nytta av en plattform som hanterar skalbarhet, säkerhet och hantering av dina IoT-program och-enheter.

## <a name="control"></a>Kontroll

Vilka element i din lösning vill du anpassa?

- Välj plattforms tjänst metoden för total anpassning och kontroll över lösnings arkitekturen.

- Välj den hanterade appens plattforms metod för anpassning, instrument paneler, användar roller, enheter och telemetri. Du vill dock inte hantera den underliggande IoT System Management-omkostnaderna.

## <a name="pricing"></a>Priser

Vilken pris struktur passar bäst för dina behov?

- Välj plattforms tjänstens metod för att finjustera tjänster och kontrol lera mina totala kostnader.

- Välj den hanterade appens plattforms metod för en enkel, förutsägbar pris struktur.

## <a name="summary"></a>Sammanfattning

Plattforms tjänstens metod är lämplig för ett företag med moln lösning och expert kunskaper som vill:

- Finjustera tjänsterna i lösningen.
- Ha en hög kontroll över tjänsterna i lösningen.
- Anpassa lösningen helt.

Den hanterade appens plattforms metod är lämplig för ett företag som:

- Vill inte tilldela omfattande resurser till system design, utveckling och hantering.
- Vill ha en förutsägbar pris struktur.
- Vill ha vissa anpassnings funktioner.

## <a name="next-steps"></a>Nästa steg

En mer omfattande förklaring av de olika tjänsterna och plattformarna och hur de används finns i [Azure IoT-tjänster och-tekniker](iot-services-and-technologies.md).

Mer information om de viktigaste attributen för lyckad IoT-lösning finns i [8 attributen för lyckad IoT-lösning](https://aka.ms/8attributes) White Paper.

En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Referensarkitektur för Microsoft Azure IoT).