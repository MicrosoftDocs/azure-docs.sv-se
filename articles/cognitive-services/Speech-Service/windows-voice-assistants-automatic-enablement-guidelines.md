---
title: Sekretess rikt linjer för röst assistenter i Windows
titleSuffix: Azure Cognitive Services
description: Anvisningarna för att aktivera röst aktivering för en röst agent som standard.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90987402"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Sekretess rikt linjer för röst assistenter i Windows

Det är viktigt att användarna får tydlig information om hur deras röst data samlas in och används och är viktiga att de får kontroll över om och hur den här samlingen sker. De här kärn ansiktena för sekretess – *avslöjande* och *medgivande* – är särskilt viktiga för röst assistenter som är integrerade i Windows och som alltid lyssnar på användningen.

Utvecklare som skapar röst assistenter i Windows måste ta med tydliga användar gränssnitts element i sina program som återspeglar lyssnar funktioner i assistenten.

> [!NOTE]
> Underlåtenhet att lämna ut korrekt information och medgivande för ett assistent program, inklusive efter program uppdateringar, kan leda till att assistenten blir otillgänglig för röst aktivering tills integritets problem har lösts.

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimi krav för funktions inkludering

Windows-användare kan se och kontrol lera tillgängligheten för sina assistent program i **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Skärm bild som visar alternativ för att kontrol lera tillgänglighet av Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "En sekretess inställnings post för Windows Voice Activation för ett assistent program")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Kontakta Microsoft på winvoiceassistants@microsoft.com för att komma igång om du vill bli berättigad till att inkludera i den här listan. Som standard måste användarna uttryckligen aktivera röst aktivering för en ny assistent i **`Settings > Privacy > Voice Activation`** , vilket ett program kan protokoll länkar till med `ms-settings:privacy-voiceactivation` . Ett tillåtet program kommer att visas i listan när det har körts och använt `Windows.ApplicationModel.ConversationalAgent` API: erna. Inställningarna för röst aktivering kan ändras när programmet har fått ett mikrofon godkännande från användaren.

Eftersom inställningarna för Windows-sekretess innehåller information om hur röst aktivering fungerar och har standard gränssnittet för att kontrol lera behörighet, är information och godkännande båda uppfyllda. Assistenten finns kvar i den här listan över tillåtna så länge det inte är:

* Vilseleda eller misinform användaren om röst aktivering eller hantering av röst data från assistenten
* Otillbörligt stör en annan assistent
* Bryt alla andra relevanta Microsoft-principer

Om något av ovanstående är identifierat kan Microsoft ta bort en assistent från listan över tillåtna tills problem har lösts.

> [!NOTE]
> I samtliga fall kräver röst aktiverings behörigheten mikrofon behörighet. Om ett assistent program inte har åtkomst till mikrofonen är det inte berättigat till röst aktivering och kommer att visas i sekretess inställningarna för röst aktivering i inaktiverat tillstånd.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Ytterligare krav för att inkludera i mikrofonen

Assistent författare som vill göra det enklare och smidigare för användarna att välja röst aktivering kan göra detta genom att uppfylla ytterligare krav på korrekt uppfyllande av information och medgivande utan en extra resa till inställnings sidan. När den har godkänts blir röst aktiveringen tillgänglig omedelbart när en användare ger till gång till ett mikrofon program. För att kunna göra detta måste ett assistent program göra följande **innan** du uppmanas att ange ett mikrofon godkännande (till exempel genom att använda `AppCapability.RequestAccessAsync` API: et):

1. Ge tydliga och framträdande uppgifter om användaren att programmet vill lyssna på användarens röst för ett nyckelord, *även om programmet inte körs* och vill att användarens samtycke
1. Ta med relevant information om principer för data användning och sekretess, till exempel en länk till en officiell sekretess policy
1. Undvik alla direktiv eller inledande orda lydelse (till exempel "Klicka på Ja i följande prompt") i upplevelse flödet som avbildar ljud tagnings beteendet

Om ett program uppnår alla ovanstående, är det möjligt att aktivera röst aktiverings funktioner tillsammans med mikrofon. Kontakta winvoiceassistants@microsoft.com för att få mer information och granska en första användnings upplevelse.

> [!NOTE]
> Röst aktivering över lås är inte kvalificerad för automatisk aktivering med mikrofon åtkomst och kommer fortfarande att kräva att en användare besöker sidan för röst aktiverings sekretess för att aktivera åtkomst över lås till en assistent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om metod tips för röst assistenter i Windows](windows-voice-assistants-best-practices.md)