---
title: Vad är Azure Communication Services?
description: Lär dig Azure Communication Services hjälper dig att utveckla omfattande användarupplevelser med realtidskommunikation.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588113"
---
# <a name="what-is-azure-communication-services"></a>Vad är Azure Communication Services?

Azure Communication Services kan du enkelt lägga till röst-, video- och telefonkommunikation i realtid i dina program. Communication Services-SDK:er kan du också lägga till SMS-funktioner i dina kommunikationslösningar. Azure Communication Services är identitetsoberoende och du har fullständig kontroll över hur slutanvändare identifieras och autentiseras. Du kan ansluta människor till kommunikationsdataplanet eller kommunikationstjänster (robotar).

Program omfattar:

- **Företag till konsument (B2C).** Ett företags anställda och tjänster kan interagera med konsumenter med hjälp av röst-, video- och RTF-chatt i en anpassad webbläsare eller ett mobilprogram. En organisation kan skicka och ta emot SMS eller använda ett interaktivt röstsvarssystem (IVR) med hjälp av ett telefonnummer som du får via Azure. [Integrering med Microsoft Teams gör det](./quickstarts/voice-video-calling/get-started-teams-interop.md) möjligt för användare att delta i Teams-möten som värdar för anställda. perfekt för fjärranslutna sjukvårds-, bank- och produktsupportscenarier där medarbetarna kanske redan är bekanta med Teams.
- **Konsument till konsument.** Skapa engagerande sociala utrymmen för interaktion mellan konsumenter med röst-, video- och RTF-chatt. Alla typer av användargränssnitt kan byggas på Azure Communication Services-SDK:er, men fullständiga programexempel och gränssnittstillgångar är tillgängliga för att hjälpa dig att komma igång snabbt.

Mer information finns i vår Microsoft [Mechanics-video eller](https://www.youtube.com/watch?v=apBX7ASurgM) de resurser som är länkade nedan.

## <a name="common-scenarios"></a>Vanliga scenarier

<br>

| Resurs                               |Beskrivning                           |
|---                                    |---                                   |
|**[Skapa en Communication Services-resurs](./quickstarts/create-communication-resource.md)**|Börja använda Azure Communication Services med hjälp av Azure Portal eller Communication Services SDK för att etablera din Communication Services resurs. När du har Communication Services resursanslutningssträng kan du etablera dina första användaråtkomsttoken.|
|**[Hämta ett telefonnummer](./quickstarts/telephony-sms/get-phone-number.md)**|Du kan använda Azure Communication Services för att etablera och släppa telefonnummer. Dessa telefonnummer kan användas för att initiera eller ta emot telefonsamtal och skapa SMS-lösningar.|
|**[Skicka ett SMS från din app](./quickstarts/telephony-sms/send.md)**|Den Azure Communication Services SMS SDK används för att skicka och ta emot SMS från tjänstprogram.|

När du har Communication Services en resurs kan du börja skapa klientscenarier, till exempel röst- och videosamtal eller textchatt:

| Resurs                               |Beskrivning                           |
|---                                    |---                                   |
|**[Skapa din första användaråtkomsttoken](./quickstarts/access-tokens.md)**|Användaråtkomsttoken används för att autentisera klienter mot Azure Communication Services resurs. Dessa token etableras och återutgås med hjälp av Communication Services SDK.|
|**[Kom igång med röst- och videosamtal](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services kan du lägga till röst- och videosamtal till webbläsaren eller interna appar med hjälp av Anropa SDK. |
|**[Anslut din samtalsapp till ett Teams-möte](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services kan användas för att skapa anpassade mötesupplevelser som interagerar med Microsoft Teams. Användare av dina Communication Services kan interagera med Teams-deltagare via röst, video, chatt och skärmdelning.|
|**[Kom igång med chatt](./quickstarts/chat/get-started.md)**|Den Azure Communication Services Chat SDK används för att lägga till innehållsrik textchatt i realtid i dina program.|

## <a name="samples"></a>Exempel

Följande exempel visar användningen av Azure Communication Services. Använd dessa exempel för att bootstrap-koda dina Communication Services lösningar.
<br>

| Exempelnamn                               | Description                           |
|---                                    |---                                   |
|**[Exemplet på gruppsamtal till Hero](./samples/calling-hero-sample.md)**| Ladda ned ett utformat programexempel för gruppsamtal för webbläsare, iOS- och Android-enheter. |
|**[Exempel på gruppchatt](./samples/chat-hero-sample.md)**| Ladda ned ett utformat programexempel för grupptextchatt för webbläsare. |


## <a name="platforms-and-sdk-libraries"></a>Plattformar och SDK-bibliotek

Läs mer om Azure Communication Services-SDK:er med resurserna nedan. REST-API:er är tillgängliga för de flesta funktioner om du vill skapa egna klienter eller på annat sätt komma åt tjänsten via Internet.

| Resurs                               | Beskrivning                           |
|---                                    |---                                   |
|**[SDK-bibliotek och REST API:er](./concepts/sdk-options.md)**|Azure Communication Services är konceptuellt ordnade i sex områden, där var och en representeras av en SDK. Du kan bestämma vilka SDK-bibliotek som ska användas baserat på dina kommunikationsbehov i realtid.|
|**[Översikt över anropande SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Granska översikten Communication Services ANROPA SDK.|
|**[Översikt över Chat SDK](./concepts/chat/sdk-features.md)**|Granska översikten Communication Services Chat SDK.|
|**[Översikt över SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Granska översikten Communication Services SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Andra Microsoft-Communication Services

Det finns två andra Microsoft-kommunikationsprodukter som du kan överväga att använda som inte är direkt kompatibla med Communication Services just nu:

 - [Microsoft Graph API:er för molnkommunikation](/graph/cloud-communications-concept-overview) gör det möjligt för organisationer att skapa kommunikationsupplevelser som är knutna till Azure Active Directory användare med Microsoft 365 licenser. Detta är perfekt för program som är kopplade Azure Active Directory eller där du vill utöka produktivitetsupplevelserna i Microsoft Teams. Det finns även API:er för att skapa program och anpassningar i [Teams-upplevelsen.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Azure PlayFab Party förenklar](/gaming/playfab/features/multiplayer/networking/) tillägg av chatt med låg latens och datakommunikation till spel. Även om du kan driva chatt- och nätverkssystem med Communication Services är PlayFab ett skräddarsytt alternativ och kostnadsfritt på Xbox.


## <a name="next-steps"></a>Nästa steg

 - [Skapa en Communication Services-resurs](./quickstarts/create-communication-resource.md)
