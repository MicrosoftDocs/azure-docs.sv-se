---
title: Tal igenkännings tjänst för direkt linje
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för röst assistenter med hjälp av direkt linje tal med Speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611801"
---
# <a name="what-is-direct-line-speech"></a>Vad är Direct Line Speech?

**Direkt linje tal** är en robust och heltäckande lösning för att skapa en flexibel, utöknings bar röst assistent. Den drivs av bot-ramverket och dess direkta linje tal kanal, som är optimerad för röst-in, röst samtals interaktion med robotar.

[Röst assistenter](voice-assistants.md) lyssnar på användare och utför en åtgärd som svar, och talar ofta tillbaka. De använder [tal-till-text](speech-to-text.md) för att skriva över användarens tal och sedan vidta åtgärder för textens naturliga språk förståelse. Den här åtgärden innehåller ofta talade utdata från assistenten som genererades med [text till tal](text-to-speech.md).

Direkt linje tal ger högsta möjliga anpassnings-och riktigt ambitiös för röst assistenter. Den är utformad för konversations scenarier som är öppna, avslutade, naturliga eller hybrider av de två med uppgift att slutföra eller använda kommando-och kontroll. Den här hög flexibiliteten ger en större komplexitet och scenarier som är begränsade till väldefinierade uppgifter med hjälp av naturligt språk kan vilja överväga [anpassade kommandon](custom-commands.md) för en strömlinjeformad lösning.

## <a name="getting-started-with-direct-line-speech"></a>Komma igång med direkt linje tal

De första stegen för att skapa en röst assistent med direkt linje tal är att [Hämta en tal prenumerations nyckel](overview.md#try-the-speech-service-for-free), skapa en ny robot som är associerad med den prenumerationen och ansluta bot till den direkta linjens tal kanal.

   ![Konceptuellt diagram över det direkta linje flödet för dirigering av tal Orchestration-tjänsten](media/voice-assistants/overview-directlinespeech.png "Tal kanal flödet")

En fullständig steg-för-steg-guide om hur du skapar en enkel röst assistent med direkt linje tal finns i [själv studie kursen om hur du aktiverar roboten med tal-SDK och kanal för direkt linje tal](tutorial-voice-enable-your-bot-speech-sdk.md).

Vi erbjuder också snabb starter som är utformade för att du ska kunna köra kod och lära sig API: erna snabbt. Den här tabellen innehåller en lista med snabb starter för röst assistenter ordnade efter språk och plattform.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Bläddra](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Bläddra](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Bläddra](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Exempelkod

Exempel kod för att skapa en röst assistent finns på GitHub. Dessa exempel avser klient programmet för att ansluta till din assistent i flera populära programmeringsspråk.

* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Självstudie: röst aktivera din assistent med talet SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Anpassning

Röst assistenter som skapats med hjälp av tal tjänsten kan använda det fullständiga urvalet av anpassnings alternativ för [tal-till-text](speech-to-text.md), [text till tal](text-to-speech.md)och [anpassad val av nyckelord](./custom-keyword-basics.md).

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](./language-support.md)).

Direkt linje tal och dess tillhör ande funktioner för röst assistenter är ett idealiskt tillägg till den [virtuella assistentens lösning och företags mal len](/azure/bot-service/bot-builder-enterprise-template-overview). Även om det direkta linje talet kan fungera med alla kompatibla bot, ger dessa resurser en åter användnings bar bas linje för konversationer med hög kvalitet samt vanliga support kunskaper och modeller för att komma igång snabbt.

## <a name="reference-docs"></a>Referens dokument

* [Speech SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Hämta tal-SDK](speech-sdk.md)
* [Skapa och distribuera en grundläggande robot](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Hämta lösningen för virtuella assistenter och företags mallar](https://github.com/Microsoft/AI)
