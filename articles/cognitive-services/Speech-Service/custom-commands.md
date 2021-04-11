---
title: Anpassade kommandon – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för anpassade kommandon, en lösning för att skapa röst program.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: e4ae8a09a554afe232091b68888af17dfa096771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102426477"
---
# <a name="what-is-custom-commands"></a>Vad är anpassade kommandon?

Program som [röst assistenter](voice-assistants.md) lyssnar på användare och vidtar en åtgärd som svar, vilket ofta talar tillbaka. De använder [tal-till-text](speech-to-text.md) för att skriva över användarens tal och sedan vidta åtgärder för textens naturliga språk förståelse. Den här åtgärden innehåller ofta talade utdata från assistenten som genererades med [text till tal](text-to-speech.md). Enheter ansluter till assistenter med tal SDK: s `DialogServiceConnector` objekt.

Med **anpassade kommandon** kan du enkelt bygga avancerade röst kommando program som är optimerade för röst-och första interaktions upplevelser. Det ger en enhetlig redigerings upplevelse, en automatisk värd modell och relativt lägre komplexitet, vilket hjälper dig att fokusera på att skapa den bästa lösningen för röst kommando scenarier.

Anpassade kommandon lämpar sig bäst för slut för ande av aktiviteter eller kommando-och kontroll scenarier, särskilt väl matchade för Sakernas Internet-enheter (IoT), omgivande och omdirigerings enheter. I exemplen ingår lösningar för service, detaljist-och bil industrier, så att du kan bygga de bästa lokala, röst kontrollerade upplevelserna för dina gäster, hantera inventering i butiken och styra funktioner i bilen i farten.

> [!TIP]
> Visa våra exempel demonstrationer på vår landnings sida på [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Om du är intresse rad av att skapa komplexa konversationer, rekommenderar vi att du testar bot-ramverket med hjälp av den [virtuella assistent lösningen](/azure/bot-service/bot-builder-enterprise-template-overview). Du kan lägga till röst till all bot Framework-robot med direkt linje tal.

Bra kandidater för anpassade kommandon har en fast ord lista med väldefinierade uppsättningar variabler. Till exempel är start automatiserings uppgifter, som att styra en termostat, idealiska.

   ![Exempel på scenarier för slut för ande av uppgift](media/voice-assistants/task-completion-examples.png "exempel på slutförd uppgift")

## <a name="getting-started-with-custom-commands"></a>Komma igång med anpassade kommandon

Vårt mål med anpassade kommandon är att minska din kognitiva belastning för att lära dig om alla olika tekniker och fokusera på att skapa din app för röst kommandon. Första steget för att använda anpassade kommandon för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">skapa en Azure tal-resurs </a>. Du kan redigera appen anpassade kommandon i tal Studio och publicera den, och sedan kan ett program på enheten kommunicera med det med hjälp av tal-SDK.

#### <a name="authoring-flow-for-custom-commands"></a>Redigerings flöde för anpassade kommandon
   ![Redigerings flöde för anpassade kommandon](media/voice-assistants/custom-commands-flow.png "De anpassade kommandona som redigerar flödet")

Följ snabb starten för att få din första anpassade kommando-app att köra kod på mindre än 10 minuter.

* [Skapa en röstassistent med Anpassade kommandon](quickstart-custom-commands-application.md)

När du är klar med snabb starten kan du utforska våra instruktions guider för detaljerade steg för att utforma, utveckla, felsöka, distribuera och integrera ett program för anpassade kommandon.

## <a name="building-voice-assistants-with-custom-commands"></a>Skapa röst assistenter med anpassade kommandon
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Visa våra röst assistenter lagrings platsen på GitHub for samples](https://aka.ms/speech/cc-samples)
* [Gå till tal Studio för att testa anpassade kommandon](https://speech.microsoft.com/customcommands)
* [Hämta tal-SDK](speech-sdk.md)