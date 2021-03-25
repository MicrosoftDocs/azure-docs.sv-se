---
title: SMS SDK-översikt för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över SMS SDK och dess erbjudanden.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25a5befab6ef141b059caaac7b1784d47f2f93cf
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108119"
---
# <a name="sms-sdk-overview"></a>Översikt över SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services SMS SDK: er kan användas för att lägga till SMS-meddelanden till dina program.

## <a name="sms-sdk-capabilities"></a>SMS SDK-funktioner

I följande lista presenteras de funktioner som för närvarande är tillgängliga i SDK: er.

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
