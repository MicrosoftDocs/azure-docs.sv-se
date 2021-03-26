---
title: Vad är Azure Communication Services?
description: Lär dig hur Azure Communication Services hjälper dig att utveckla omfattande användar upplevelser med real tids kommunikation.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fe580848e3bbea701718463123a6bbcd4848a9f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564610"
---
# <a name="what-is-azure-communication-services"></a>Vad är Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

> [!IMPORTANT]
> Program som du skapar med Azure Communication Services kan prata med Microsoft Teams. Mer information finns i vår [Teams interop](./quickstarts/voice-video-calling/get-started-teams-interop.md) -dokumentation.


Med Azure Communication Services kan du enkelt lägga till funktioner för multimedia, video och telefoni för kommunikation i real tid till dina program. I SDK: erna för kommunikations tjänster kan du också lägga till chatt-och SMS-funktioner i kommunikations lösningarna.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Du kan använda kommunikations tjänster för röst-, video-, text-och data kommunikation i olika scenarier:

- Webb läsar-till-webbläsare, webb läsar-till-app och kommunikation mellan appar
- Användare som interagerar med robotar eller andra tjänster
- Användare och robotar som interagerar över det offentliga växlade telefoni nätverket

Blandade scenarier stöds. Ett kommunikations tjänst program kan till exempel ha användare som talar från webbläsare och traditionella telefoni enheter samtidigt. Kommunikations tjänsterna kan också kombineras med Azure Bot Service för att bygga bot-drivna interaktiva Voice Response-system (IVR).

## <a name="common-scenarios"></a>Vanliga scenarier

Följande resurser är en bra plats för att komma igång med Azure Communication Services. 
<br>

| Resurs                               |Beskrivning                           |
|---                                    |---                                   |
|**[Skapa en Communication Services-resurs](./quickstarts/create-communication-resource.md)**|Du kan börja använda Azure Communication Services med hjälp av klient biblioteket för Azure Portal eller kommunikations tjänster för att etablera din första kommunikations tjänst resurs. När du har resurs anslutnings strängen för kommunikations tjänster kan du etablera dina första åtkomsttoken för användare.|
|**[Hämta ett telefonnummer](./quickstarts/telephony-sms/get-phone-number.md)**|Du kan använda Azure Communication Services för att etablera och frigöra telefonnummer. Dessa telefonnummer kan användas för att initiera utgående samtal och bygga SMS-kommunikations lösningar.|
|**[Skicka ett SMS från din app](./quickstarts/telephony-sms/send.md)**|Med klient biblioteket för Azure Communication Services SMS kan du skicka och ta emot SMS-meddelanden från .NET-och JavaScript-program.|

När du har skapat en kommunikations tjänst resurs kan du börja skapa klient scenarier, till exempel röst-och video samtal eller chatt för text.

| Resurs                               |Beskrivning                           |
|---                                    |---                                   |
|**[Skapa din första åtkomsttoken för användare](./quickstarts/access-tokens.md)**|Token för användar åtkomst används för att autentisera dina tjänster mot Azure Communication Services-resursen. Dessa tokens tillhandahålls och återutfärdas med klient biblioteket för kommunikations tjänster.|
|**[Kom igång med röst-och video samtal](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Med Azure Communication Services kan du lägga till röst-och video samtal till dina appar med hjälp av det anropande klient biblioteket. Det här biblioteket drivs av WebRTC och gör att du kan upprätta peer-to-peer, multimedia, real tids kommunikation i dina program.|
|**[Anslut din samtalsapp till ett Teams-möte](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services kan användas för att bygga anpassade Mötes upplevelser som interagerar med Microsoft Teams. Användare av kommunikations tjänst lösningen (er) kan interagera med team deltagare via röst-, video-, Chat-och skärm delning.|
|**[Kom igång med chatt](./quickstarts/chat/get-started.md)**|Klient biblioteket för Azure Communication Services Chat kan användas för att integrera chatt i real tid i dina program.|

## <a name="samples"></a>Exempel

I följande exempel demonstreras slut punkt till slut punkt för Azure Communication Services SDK: er. Du kan använda dessa exempel för att starta dina egna kommunikations tjänst lösningar.
<br>

| Exempel namn                               | Description                           |
|---                                    |---                                   |
|**[Gruppen som anropar ett hjälte exempel](./samples/calling-hero-sample.md)**|Se hur kommunikations tjänstens SDK: er kan användas för att bygga en grupp som anropar upplevelse.|
|**[Hjälte-exemplet för grupp Chat](./samples/chat-hero-sample.md)**|Se hur kommunikations tjänstens SDK: er kan användas för att bygga en grupps Chat-upplevelse.|


## <a name="platforms-and-sdks"></a>Plattformar och SDK: er

Följande resurser hjälper dig att lära dig mer om SDK: er för Azure Communication Services:

| Resurs                               | Beskrivning                           |
|---                                    |---                                   |
|**[Klientbibliotek och REST-API:er](./concepts/sdk-options.md)**|Azure Communication Services-funktionerna är konceptuellt ordnade i sex områden som representeras av en SDK. Du kan bestämma vilka SDK: er som ska användas utifrån dina kommunikations behov i real tid.|
|**[Översikt över samtals-SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Läs översikt över kommunikations tjänsterna som anropar SDK.|
|**[Översikt över chatt SDK](./concepts/chat/sdk-features.md)**|Läs översikten över kommunikations tjänsterna i Chat SDK.|
|**[Översikt över SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Läs översikten över kommunikations tjänsterna för SMS SDK.|

## <a name="compare-azure-communication-services"></a>Jämför Azure Communication Services

Det finns två andra Microsoft-kommunikationsprodukter som du kan överväga att inte direkt samverka med kommunikations tjänster för tillfället:

 - [Microsoft Graph API: er för moln kommunikation](/graph/cloud-communications-concept-overview) gör det möjligt för organisationer att bygga kommunikations upplevelser som är kopplade till Azure Active Directory användare med Microsoft 365 licenser. Detta är perfekt för program som är knutna till Azure Active Directory eller där du vill utöka produktivitets upplevelser i Microsoft Teams. Det finns också API: er för att bygga program och anpassningar i [Teams upplevelsen.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Azure PlayFab-parten](/gaming/playfab/features/multiplayer/networking/) fören klar att lägga till samtal med låg latens och data kommunikation i spel. Även om du kan spela spel och nätverks system med kommunikations tjänster, är PlayFab ett skräddarsyt alternativ och kostnads fritt på Xbox.


## <a name="next-steps"></a>Nästa steg

 - [Skapa en Communication Services-resurs](./quickstarts/create-communication-resource.md)
