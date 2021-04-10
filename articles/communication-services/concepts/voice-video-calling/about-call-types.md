---
title: Röst-och video koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om samtals typer för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8a25f69019e194650bb6aa2f5b8ae19dd37fbc48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729175"
---
# <a name="voice-and-video-concepts"></a>Begrepp inom röst- och videosamtal

Du kan använda Azure Communication Services för att skapa och ta emot en till en eller grupp röst-och video samtal. Dina anrop kan göras till andra Internet-anslutna enheter och till vanliga telefoner. Du kan använda kommunikations tjänsterna Java Script, Android eller iOS SDK: er för att bygga program som gör att användarna kan prata med varandra i privata konversationer eller gruppera diskussioner. Azure Communication Services stöder anrop till och från tjänster eller robotar.

## <a name="call-types-in-azure-communication-services"></a>Samtals typer i Azure Communication Services

Det finns flera typer av anrop som du kan göra i Azure Communication Services. Den typ av samtal som du fastställer för ditt signal schema, medie trafikflöden och pris sättnings modellen.

### <a name="voice-over-ip-voip"></a>Voice över IP (VoIP)

När en användare av programmet anropar en annan användare av ditt program via en Internet-eller data anslutning görs anropet via Voice över IP (VoIP). I det här fallet, både signalering och medie flödet över Internet.

### <a name="public-switched-telephone-network-pstn"></a>Delat telefonnät verk (PSTN)

När dina användare interagerar med ett traditionellt telefonnummer, under lättas samtal av ett PSTN-röst samtal (Public Switched Phone Network). Om du vill göra och ta emot PSTN-samtal måste du lägga till telefoni funktioner i Azure Communication Services-resursen. I detta fall använder signalering och media en kombination av IP-baserade och PSTN-baserade tekniker för att ansluta dina användare.

### <a name="one-to-one-call"></a>Ett-till-ett-anrop

Ett ett-till-ett-anrop i Azure Communication Services sker när en av dina användare ansluter till en annan användare med hjälp av någon av våra SDK: er. Anropet kan vara antingen VoIP eller PSTN.

### <a name="group-call"></a>Grupp anrop

Ett grupp anrop i Azure Communication Services sker när tre eller fler deltagare ansluter till varandra. Alla kombinationer av VoIP-och PSTN-anslutna användare kan finnas i ett grupp anrop. Ett ett-till-ett-anrop kan konverteras till ett grupp anrop genom att lägga till fler deltagare till anropet. En av dessa deltagare kan vara en bot.

### <a name="supported-video-standards"></a>Video standarder som stöds
Vi stöder H. 264 (MPEG-4).

### <a name="video-quality"></a>Videokvalitet 
Vi har stöd för upp till full HD 1080p på interna (iOS, Android) SDK: er. För Web (JS) SDK stöder vi standard HD 720p. Kvaliteten beror på den tillgängliga bandbredden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [samtals flöden](../call-flows.md)
- [Telefonnummer typer](../telephony-sms/plan-solution.md)
- Lär dig mer om de [anropande SDK-funktionerna](../voice-video-calling/calling-sdk-features.md)
