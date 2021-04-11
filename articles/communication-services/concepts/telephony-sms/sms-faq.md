---
title: VANLIGA FRÅGOR OCH SVAR OM SMS
titleSuffix: An Azure Communication Services concept document
description: VANLIGA FRÅGOR OCH SVAR OM SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646319"
---
# <a name="sms-faq"></a>VANLIGA FRÅGOR OCH SVAR OM SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Kan en kund använda Azure Communication Services i nödfalls syfte?

Azure Communication Services stöder inte text-till-911-funktioner i USA, men det är möjligt att du har skyldighet att göra detta enligt reglerna i Federal Communications provision (FCC).  Du bör kontrol lera om FCC: s text-till-911-regler gäller för din tjänst eller ditt program. I den utsträckning som du omfattas av dessa regler ansvarar du för att skicka e-postmeddelanden med 911 till nöd samtals Center som begär dem. Du är kostnads fri att ta reda på din egen text-till-911-leverans modell, men en metod som godkänns av FCC innebär att automatiskt starta den interna uppringningen på användarens mobila enhet för att leverera 911-texter via den underliggande mobila operatören.

## <a name="are-there-any-limits-on-sending-messages"></a>Finns det några begränsningar för att skicka meddelanden?

För att säkerställa att vi fortsätter att erbjuda hög kvalitet på tjänsten som är konsekvent med vår service avtal tillämpar Azure Communication Services hastighets begränsningar (olika för varje primitiv). Utvecklare som anropar våra API: er utanför gränsen får ett svar på 429 HTTP status kod. Om ditt företag har krav som överstiger hastighets gränserna kan du skicka ett e-postmeddelande till oss phone@microsoft.com .

Hastighets begränsningar för SMS:

|Åtgärd|Omfång|Tidsram (er)| Gräns (begär #) | Meddelande enheter per minut|
|---------|-----|-------------|-------------------|-------------------------|
|Skicka meddelande|Per nummer|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Hur hanterar Azure Communication Services opt-timeout för avgiftsfria nummer?

Opt-timeout för amerikanska avgiftsfria nummer bestäms och framtvingas av amerikanska operatörer.
- STOPPA – om en SMS-mottagare vill välja ut kan de skicka "stopp" till det avgiftsfria numret. Transport företaget skickar följande standard svar för stopp: "nätverks meddelande: du svarade med ordet" Stop "som blockerar alla texter som skickas från det här numret. Skriv tillbaka "avstanna" för att ta emot meddelanden igen. "
- Starta/sluta stoppa – om mottagaren vill prenumerera på SMS igen från ett avgiftsfritt nummer, kan de skicka "START" eller "sluta stanna till det avgiftsfria numret. Transport företaget skickar följande standard svar för starta/sluta stoppa: "nätverks meddelande: du har besvarat" avstanna "och kommer att börja ta emot meddelanden igen från det här numret."
- Azure Communication Services kommer att identifiera stopp meddelandet och blockera alla ytterligare meddelanden till mottagaren. Leverans rapporten visar att det inte gick att leverera med status meddelandet "avsändare för den aktuella mottagaren".
- Meddelandena stoppa, stoppa och starta kommer att vidarebefordras tillbaka till dig. Azure Communication Services uppmuntrar dig att övervaka och implementera de här alternativen för att se till att inga fler meddelande försök görs till mottagare som har valt att skicka in din kommunikation.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Hur kan jag ta emot meddelanden med Azure Communication Services?

Azure Communication Services-kunder kan använda Azure Event Grid för att ta emot inkommande meddelanden. Följ den här [snabb](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) starten för att konfigurera händelse rutnätet för att ta emot meddelanden.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Kan jag skicka/ta emot långa meddelanden (>2048-tecken)?

Azure Communication Services stöder sändning och mottagning av långa meddelanden via SMS. Vissa trådlösa transport företag eller enheter kan dock agera annorlunda när de tar emot långa meddelanden.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Hur skickas meddelanden till Landline-nummer som behandlas?

I USA söker Azure Communication Services inte efter Landline-nummer och försöker skicka det till transport företag för leverans. Kunderna debiteras för meddelanden som skickas till Landline-nummer. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Kan jag skicka meddelanden till flera mottagare?


Ja, du kan göra en begäran med flera mottagare. Följ den här [snabb](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) starten för att skicka meddelanden till flera mottagare.
