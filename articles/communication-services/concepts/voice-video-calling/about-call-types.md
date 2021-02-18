---
title: Röst-och video koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om samtals typer för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 98c1ce9367e9bf2aa1a381a772b3cbc73f96b519
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650957"
---
# <a name="voice-and-video-concepts"></a>Begrepp inom röst- och videosamtal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Du kan använda Azure Communication Services för att skapa och ta emot en till en eller grupp röst-och video samtal. Dina anrop kan göras till andra Internet-anslutna enheter och till vanliga telefoner. Du kan använda kommunikations tjänsterna Java Script, Android eller iOS klient bibliotek för att bygga program som gör att användarna kan prata med varandra i privata konversationer eller gruppera diskussioner. Azure Communication Services stöder anrop till och från tjänster eller robotar.

## <a name="call-types-in-azure-communication-services"></a>Samtals typer i Azure Communication Services

Det finns flera typer av anrop som du kan göra i Azure Communication Services. Den typ av samtal som du fastställer för ditt signal schema, medie trafikflöden och pris sättnings modellen.

### <a name="voice-over-ip-voip"></a>Voice över IP (VoIP) 

När en användare av programmet anropar en annan användare av ditt program via en Internet-eller data anslutning görs anropet via Voice över IP (VoIP). I det här fallet, både signalering och medie flödet över Internet.

### <a name="public-switched-telephone-network-pstn"></a>Delat telefonnät verk (PSTN)

När dina användare interagerar med ett traditionellt telefonnummer, under lättas samtal av ett PSTN-röst samtal (Public Switched Phone Network). Om du vill göra och ta emot PSTN-samtal måste du lägga till telefoni funktioner i Azure Communication Services-resursen. I detta fall använder signalering och media en kombination av IP-baserade och PSTN-baserade tekniker för att ansluta dina användare.

### <a name="one-to-one-call"></a>Ett-till-ett-anrop

Ett ett-till-ett-anrop i Azure Communication Services sker när en av dina användare ansluter till en annan användare med hjälp av ett av våra klient bibliotek. Anropet kan vara antingen VoIP eller PSTN.

### <a name="group-call"></a>Grupp anrop

Ett grupp anrop i Azure Communication Services sker när tre eller fler deltagare ansluter till varandra. Alla kombinationer av VoIP-och PSTN-anslutna användare kan finnas i ett grupp anrop. Ett ett-till-ett-anrop kan konverteras till ett grupp anrop genom att lägga till fler deltagare till anropet. En av dessa deltagare kan vara en bot.

### <a name="supported-video-standards"></a>Video standarder som stöds
Vi stöder H. 264 (MPEG-4) 

### <a name="video-quality"></a>Videokvalitet 
Vi har stöd för upp till full HD 1080p på interna (iOS, Android) SDK: er. För Web (JS) SDK stöder vi standard HD 720p. Kvaliteten beror på den tillgängliga bandbredden.  

### <a name="rooms-concept"></a>Konceptet rum
Rum är en uppsättning API: er och SDK: er som gör att du enkelt kan lägga till ljud-, video-, skärm delnings-, PSTN-och SMS-interaktioner till din webbplats eller det interna
Under förhands granskningen kan du använda grupp-ID: t för att ansluta till samma konversation. Du kan skapa så många grupp-ID: n som du behöver och avgränsa användarna med "rum". Om du flyttar framåt introduceras fler kontroller runt "rum" 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [samtals flöden](../call-flows.md)
- [Telefonnummer typer](../telephony-sms/plan-solution.md)
- Lär dig mer om funktioner för att [anropa klient bibliotek](../voice-video-calling/calling-sdk-features.md)
