---
title: Anpassade nyckelord – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för anpassade nyckelord med hjälp av Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91356612"
---
# <a name="what-is-a-keyword"></a>Vad är ett nyckelord?

Ett nyckelord är ett ord eller en kort fras som gör att produkten röst aktive rad. Till exempel är "Hej Cortana" ett nyckelord för Cortana-assistenten. Röst aktivering gör att användarna kan börja interagera med produkten helt utan kostnad genom att bara prata med nyckelordet. När din produkt kontinuerligt lyssnar efter nyckelordet bearbetas alla ljud lokalt på användarens enhet tills en identifiering görs för att säkerställa att data förblir så privat som möjligt.

## <a name="core-features-of-custom-keyword"></a>Huvud funktioner i anpassat nyckelord

Med anpassade nyckelords anpassnings-, prestanda-och integrerings funktioner kan du skräddarsy röst aktivering så att den passar din produkts vision och användarnas behov.

| Funktion | Beskrivning |
|----------|----------|
| Anpassning av nyckelord | Som ett tillägg till ditt varumärke stärker ett nyckelord det aktie som du har skapat med kunderna. Med den anpassade nyckelords portalen i tal Studio kan du ange ett ord eller en kort fras som bäst motsvarar ditt varumärke. Du kan anpassa ditt nyckelord ytterligare genom att välja rätt uttal, som kommer att användas av nyckelords modellen som genereras.
| Nyckelords verifiering | När det finns hög exakthet i nyckelordet som identifieras lokalt skickas ljud till molnet för att ytterligare verifiera att en användare har sagt detta nyckelord. Nyckelords verifiering ger ett extra säkerhets lager genom att minska effekten av felaktig lokal identifiering och skydda användar sekretessen.
| Röst assistent & Speech SDK integration | Nyckelord som genereras från det anpassade nyckelordet i tal Studio kan enkelt integreras i din enhet eller i ditt program via tal-SDK: n. Du behöver bara peka SDK: t till den nyckelords modell som tillhandahålls av tal Studio och din produkt kommer att vara röst aktive rad med nyckelords verifiering. Du kan slutföra din produkts röst upplevelse genom att skapa en egen [röst assistent](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Kom igång med anpassade nyckelord

* Se [grunderna för anpassade nyckelord](custom-keyword-basics.md) för grundläggande användning och design mönster.
* Så här [aktiverar du din produkt med tal-SDK: n med C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Välj ett effektivt nyckelord

Att skapa ett effektivt nyckelord är viktigt för att se till att enheten kommer att reagera konsekvent och korrekt. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke. Överväg följande rikt linjer när du väljer ett nyckelord:

> [!div class="checklist"]
> * Ditt nyckelord bör vara ett engelskt ord eller en fras.
> * Det bör inte ta mer än två sekunder att säga.
> * Ord av 4 till 7 stavelser fungerar bäst. Till exempel "Hej, dator" är ett utmärkt nyckelord. Bara "Hej" är ett dåligt.
> * Nyckelord bör följa vanliga intals regler för engelska uttal.
> * Ett unikt eller till och med ett upprättat ord som följer vanliga regler för engelska uttal kan minska antalet falska positiva identifieringar. Till exempel kan "computerama" vara ett utmärkt nyckelord.
> * Välj inget vanligt ord. Till exempel är "äta" och "go" ord som människor säger ofta i vanlig konversation. De kan vara falska utlösare för enheten.
> * Undvik att använda nyckelord som kan ha alternativa uttal. Användarna måste känna till "Right"-uttal för att få enheten att svara. "509" kan till exempel uttalas "5 0 9", "5 0 9" eller "509". "R.E.I." kan uttalas "r-e-i" eller "Ray". "Live" kan uttalas "/līv/" eller "/liv/".
> * Använd inte specialtecken, symboler eller siffror. Till exempel "go #" och "20 + katter" kan vara problematiska nyckelord. Men "gå skarpa" eller "tjugo plus katter" kan fungera. Du kan fortfarande använda symbolerna i din anpassning och använda marknadsföring och dokumentation för att förstärka rätt uttal.

> [!NOTE]
> Om du väljer ett tilldelat ord som nyckelord måste du vara säker på att du äger detta varumärke eller att du har behörighet från varumärkes ägaren för att använda ordet. Microsoft ansvarar inte för några juridiska problem som kan uppstå från ditt val av nyckelord.

## <a name="see-samples-on-github"></a>Se exempel på GitHub

* [Identifiera nyckelord med talet SDK, på Universell Windows-plattform med C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Identifiera nyckelord med talet SDK, på Android med Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Hämta tal-SDK](speech-sdk.md)
* [Lär dig mer om röst assistenter](voice-assistants.md)
