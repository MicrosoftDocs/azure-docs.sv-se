---
title: Översikt över SMS-klient bibliotek för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över klient biblioteket för SMS och dess erbjudanden.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2448840fcbbea686972f8f948d428bda7b3c2bf2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391753"
---
# <a name="sms-client-library-overview"></a>Översikt över klientbiblioteket för SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services SMS-klient bibliotek kan användas för att lägga till SMS-meddelanden i dina program.

## <a name="sms-client-library-capabilities"></a>Funktioner för SMS-klient bibliotek

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i våra klient bibliotek.

| Grupp med funktioner | Funktion                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Kärn funktioner | Skicka och ta emot SMS-meddelanden </br> *Unicode-emojis stöds*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ta emot leverans rapporter för skickade meddelanden                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alla teckenuppsättningar (språk-/Unicode-stöd)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Stöd för långa meddelanden (upp till 2048 tecken)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatisk sammanfogning av långa meddelanden                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Händelser            | Använd Event Grid för att konfigurera Webhooks för att ta emot inkommande meddelanden och leverans rapporter | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonnummer      | Toll-Free nummer                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regler        | Opt-Out hantering                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Övervakning        | Övervaka användning för skickade och mottagna meddelanden                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-anrop      | Lägg till PSTN-anrop till det SMS-aktiverade avgiftsfria numret                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med allmänna [SMS-koncept](../telephony-sms/concepts.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummer typer i Azure Communication Services](../telephony-sms/plan-solution.md)
