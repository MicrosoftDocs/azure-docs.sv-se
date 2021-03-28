---
title: SMS SDK-översikt för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över SMS SDK och dess erbjudanden.
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2e9f782f0b8fa0be77e70ccdae8849caa4abb81e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643816"
---
# <a name="sms-sdk-overview"></a>Översikt över SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services SMS SDK: er kan användas för att lägga till SMS-meddelanden till dina program.

## <a name="sms-sdk-capabilities"></a>SMS SDK-funktioner

I följande lista presenteras de funktioner som för närvarande är tillgängliga i SDK: er.

| Grupp med funktioner | Funktion                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Kärn funktioner | Skicka och ta emot SMS-meddelanden                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Aktivera leverans rapporter för skickade meddelanden                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alla teckenuppsättningar (språk-/Unicode-stöd)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Stöd för långa meddelanden (upp till 2048 byte)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatisk sammanfogning av långa meddelanden                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Skicka meddelanden till flera mottagare i taget                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Stöd för idempotens                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Anpassade taggar för meddelanden.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Händelser            | Använd Event Grid för att konfigurera Webhooks för att ta emot inkommande meddelanden och leverans rapporter | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonnummer      | Toll-Free nummer                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-anrop      | Lägg till PSTN-anrop till det SMS-aktiverade avgiftsfria numret                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med allmänna [SMS-koncept](../telephony-sms/concepts.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummer typer i Azure Communication Services](../telephony-sms/plan-solution.md)
