---
title: SMS-koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om SMS-koncept för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e879b7938eb778d4cdbbef4a970325501a6124db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932156"
---
# <a name="sms-concepts"></a>SMS-begrepp

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Med Azure Communication Services kan du skicka och ta emot SMS-textmeddelanden med hjälp av kommunikations tjänsterna SMS SDK: er. Dessa SDK: er kan användas för att stödja kund tjänst scenarier, påminnelser för avtalade tider, tvåfaktorautentisering och andra behov av kommunikation i real tid. Med kommunikations tjänster i SMS kan du på ett tillförlitligt sätt skicka meddelanden samtidigt som du exponerar leverans-och svars mått.

Viktiga funktioner i Azure Communication Services SMS SDK: er är:

-  **Enkel** installations miljö för att lägga till SMS-funktioner i dina program.
- **Hög hastighets** meddelande support över avgiftsfria nummer för A2P (program till person) i USA.
- **Mass meddelanden** som stöds för att aktivera sändning av meddelanden till flera mottagare i taget.
- **Dubbelriktade** konversationer som stöder scenarier som kund support, aviseringar och påminnelser om avtalade tider.
- **Tillförlitlig leverans** med rapporter om leverans i real tid för meddelanden som skickas från ditt program.
- **Analys** för att spåra dina SMS-användnings mönster.
- Hanterings stöd för att automatiskt identifiera och respektera opt- **out** för avgiftsfritt nummer. Opt-timeout för amerikanska avgiftsfria nummer bestäms och framtvingas av amerikanska operatörer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med [SMS SDK](../telephony-sms/sdk-features.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummer typer i Azure Communication Services](../telephony-sms/plan-solution.md)
