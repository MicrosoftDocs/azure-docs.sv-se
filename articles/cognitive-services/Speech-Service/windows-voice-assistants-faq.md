---
title: Röst assistenter i Windows – vanliga frågor och svar
titleSuffix: Azure Cognitive Services
description: Vanliga frågor som ofta kommer upp under utveckling av Windows Voice agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84457022"
---
# <a name="samples-and-faqs"></a>Exempel och vanliga frågor och svar

## <a name="the-uwp-voice-assistant-sample"></a>Exemplet på UWP röst assistent

Exemplet på UWP röst assistent är en röst assistent i Windows som hanterar

- demonstrera användningen av Windows ConversationalAgent-API: er
- Visa metod tips för en röst agent i Windows
- tillhandahålla en anpassningsbar app och återanvändbara komponenter för ditt MVP-agentprogram
- Visa hur direkt linje tal, tillsammans med bot Framework eller anpassade kommandon, kan användas tillsammans med Windows ConversationalAgent-API: er för en komplett röst agent upplevelse

Dokumentationen som medföljer exempel appen går igenom kod Sök vägen för röst aktivering och agent hantering, från förutsättningarna för start vid rätt rensning.

> [!div class="nextstepaction"]
> [Besök GitHub-lagrings platsen för UWP-exemplet](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Hur gör jag för att kontaktar du Microsoft för resurser som begränsade åtkomst funktioner för token och nyckelords modell filer?

Kontakta winvoiceassistants@microsoft.com för att begära dessa resurser.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Min app visas i ett litet fönster när jag aktiverar det med rösten. Hur kan jag gå över från vyn komprimera till ett fullständigt programfönster?

När ditt program först aktive ras av Voice startas det i en komprimerad vy. I [design vägledningen för för hands versionen av röst aktivering](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) hittar du information om olika vyer och över gångar mellan dem för röst assistenter i Windows.

Använd appView-API: et för att göra över gången från komprimerad vy till fullständig app-vy `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Varför är röst assistents funktioner i Windows bara aktiverade för UWP-program?

Med tanke på de sekretess risker som är associerade med funktioner som röst aktivering, är funktionerna i UWP-plattformen nödvändiga för att röst assistents funktionerna i Windows ska vara tillräckligt säkra.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>Exemplet på UWP röst assistent använder direkt linje tal. Måste jag använda direkt linje tal för min röst assistent i Windows?

UWP-exempelprogrammet utvecklades med hjälp av direkt linje tal och Speech Services SDK som en demonstration av hur du använder en dialog tjänst med funktioner för Windows konversations agent. Du kan dock använda valfri tjänst för kontroll av lokala och moln nyckelord, röst-till-text-konvertering, robot dialog och text till tal-konvertering.