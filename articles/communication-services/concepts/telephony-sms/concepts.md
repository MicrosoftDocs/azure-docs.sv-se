---
title: SMS-koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om SMS-koncept för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6f03195c801f241a79a6bf68ef9d398370b0d4c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391821"
---
# <a name="sms-concepts"></a>SMS-begrepp

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Med Azure Communication Services kan du skicka och ta emot SMS-textmeddelanden med hjälp av kommunikations tjänsterna SMS-klientcertifikat. Dessa klient bibliotek kan användas för att stödja kund tjänst scenarier, påminnelser för avtalade tider, tvåfaktorautentisering och andra real tids kommunikations behov. Med kommunikations tjänster i SMS kan du på ett tillförlitligt sätt skicka meddelanden samtidigt som du kan exponera leverans och svars takt insikter kring dina kampanjer.

Viktiga funktioner i Azure Communication Services SMS-klient bibliotek är:

-  **Enkel** installations miljö för att lägga till SMS-funktioner i dina program.
- **Hög hastighets** meddelande support över avgiftsfria nummer för A2P (program till person) i USA.
- **Dubbelriktade** konversationer som stöder scenarier som kund support, aviseringar och påminnelser om avtalade tider.
- **Tillförlitlig leverans** med rapporter om leverans i real tid för meddelanden som skickas från ditt program.
- **Analys** för att spåra användnings mönster och kund engagemang.
- Hanterings stöd för att automatiskt identifiera och respektera opt- **out** för avgiftsfritt nummer. Opt-timeout för amerikanska avgiftsfria nummer bestäms och framtvingas av amerikanska operatörer.
  - STOPPA – om en SMS-mottagare vill välja ut kan de skicka "stopp" till det avgiftsfria numret. Transport företaget skickar följande standard svar för stopp: *"nätverks meddelande: du svarade med ordet" Stop "som blockerar alla texter som skickas från det här numret. Skriv tillbaka "avstanna" för att ta emot meddelanden igen. "*
  - Starta/sluta stoppa – om mottagaren vill prenumerera på SMS igen från ett avgiftsfritt nummer, kan de skicka "START" eller "sluta stanna till det avgiftsfria numret. Transport företaget skickar följande standard svar för starta/sluta stoppa: *"nätverks meddelande: du har besvarat" avstanna "och kommer att börja ta emot meddelanden igen från det här numret."*
  - Azure Communication Services kommer att identifiera stopp meddelandet och blockera alla ytterligare meddelanden till mottagaren. Leverans rapporten visar att det inte gick att leverera med status meddelandet "avsändare för den aktuella mottagaren".
  - Meddelandena stoppa, stoppa och starta kommer att vidarebefordras tillbaka till dig. Azure Communication Services uppmuntrar dig att övervaka och implementera de här alternativen för att se till att inga fler meddelande försök görs till mottagare som har valt att skicka in din kommunikation.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med SMS- [klient biblioteket](../telephony-sms/sdk-features.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummer typer i Azure Communication Services](../telephony-sms/plan-solution.md)
