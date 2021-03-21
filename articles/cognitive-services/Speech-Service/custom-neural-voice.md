---
title: Anpassad röst översikt för neurala – tal tjänst
titleSuffix: Azure Cognitive Services
description: Anpassad neurala-röst är en text till tal-funktion som gör att du kan skapa en egen, anpassad syntetisk röst för dina program genom att tillhandahålla dina egna ljud data som ett exempel.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713107"
---
# <a name="what-is-custom-neural-voice"></a>Vad är anpassat neurala röst?

Anpassad neurala-röst är en [text till tal](./text-to-speech.md) -funktion som gör att du kan skapa en egen, anpassad syntetisk röst för dina program genom att tillhandahålla dina egna ljud data som ett exempel. Text till tal fungerar genom att konvertera text till syntetiskt tal med en maskin inlärnings modell som låter som en vald röst. Med [REST API](./rest-text-to-speech.md)kan du göra det möjligt för dina appar att prata med [förbyggda röster](./language-support.md#neural-voices) eller dina egna [anpassade röst](./how-to-custom-voice-prepare-data.md) modeller som utvecklats genom den anpassade röst funktionen för neurala. Den anpassade neurala-rösten baseras på neurala TTS-teknik som skapar en naturlig ljud röst som ofta är lätt att skilja jämfört med en mänsklig röst.
Den realistiska och naturliga ljud rösten i den anpassade neurala-rösten kan representera varumärken, personify datorer och tillåta användare att interagera med program på ett naturligt sätt.

> [!NOTE]
> Den anpassade röst funktionen för neurala kräver registrering och åtkomst till den är begränsad baserat på Microsofts villkor för berättigande och användning. Kunder som vill använda den här funktionen krävs för att registrera sina användnings fall genom [insugnings formuläret](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Grunderna för anpassad röst för neurala

Den underliggande neurala TTS-tekniken som används för anpassad neurala-röst består av tre huvud komponenter: text analys, neurala akustisk modell och neurala vocoder. Om du vill generera naturliga syntetiskt tal från text är texten först indata i text Analyzer, som ger utdata i form av fonem Sequence. En fonem är en grundläggande enhet av ljud som skiljer ett ord från ett annat på ett visst språk. En sekvens med fonem definierar uttal av de ord som finns i texten. 

Sedan går fonem-sekvensen till neurala-akustisk modell för att förutsäga akustiska funktioner som definierar tal signaler, till exempel Timbre, tal stil, hastighet, intonations och stress mönster. Slutligen konverterar neurala-vocoder akustiska funktioner till akustiska vågor så att syntetiskt tal skapas.

![Introduktions bild för anpassad neurala röst.](./media/custom-voice/cnv-intro.png)

Neurala TTS Voice-modeller tränas med djup neurala-nätverk baserat på inspelnings exemplen för mänskliga röster. I den här [bloggen](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)beskriver vi hur neurala TTS fungerar med neurala tal syntes modeller. Bloggen förklarar också hur en universell bas modell kan anpassas med mindre än 2 timmars tal data (eller mindre än 2 000 inspelade yttranden) från en riktad högtalare och lär dig mer om att tala i den aktuella rösten. Om du vill läsa om hur en neurala-vocoder är utbildad, se [blogg inlägget](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Med anpassnings funktionen för anpassad neurala röst kan du anpassa neurala TTS-motorn så att den passar dina användar scenarier bättre. Om du vill skapa en anpassad neurala röst använder du [tal Studio](https://speech.microsoft.com/customvoice) för att ladda upp det inspelade ljudet och motsvarande skript, träna modellen och distribuera rösten till en anpassad slut punkt. Beroende på användnings fallet kan anpassade neurala-röst användas för att konvertera text till tal i real tid (som används i en smart virtuell assistent) eller för att generera ljud innehåll offline (t. ex., som används som i ljud boken eller instruktioner i e-Learning-program) med den text som användaren har angett. Detta görs tillgängligt via [REST API](./rest-text-to-speech.md), [tal-SDK](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)eller en [webb Portal](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Villkor och definitioner

| **Period**      | **Definition**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Röst modell   | En text till tal-modell som kan efterlikna de unika Vocal egenskaperna för en riktad talare. En *röst modell* kallas även *röst teckensnitt* eller *syntetisk röst*. En röst modell är en uppsättning parametrar i binärt format som inte är läsliga och som inte innehåller ljud inspelningar. Den kan inte bakåtkompileras för att härleda eller konstruera ljudet för en mänsklig röst. |
| Röst personal  | Individer eller mål högtalare vars röster registreras och används för att skapa röst modeller som är avsedda att ljud som rösten personal.                                                                                                                                                                                                                                                   |
| Standard TTS  | Standard metoden, eller "traditionell", som utvärderar det talade språket till fonetiska kodfragment så att de kan Remixed och matchas med hjälp av klassisk programmering eller statistiska metoder.                                                                                                                                                                                                    |
| Neurala TTS    | Neurala TTS-syntetiserar tal med djup neurala nätverk som har "lärt sig" hur fonetik kombineras i naturliga mänsklig tal i stället för att använda procedurbaserade programmerings metoder eller statistiska metoder. Förutom inspelningar av en mål röst personal använder neurala TTS en käll bibliotek/bas modell som har skapats med röst inspelningar från många olika högtalare.          |
| Träningsdata | En anpassad neurala för röst träning som innehåller ljud inspelningar av röst-personal och tillhör ande text avskrifter.                                                                                                                                                                                                                                                               |
| Person       | En person som beskriver vem du vill att den här rösten ska vara. En lämplig persons design informerar all röst när den väljer en tillgänglig röst modell som redan har skapats eller från början genom att omvandla och registrera en ny röst personal.                                                                                                |
| Skript        | Ett skript är en textfil som innehåller yttranden som ska läsas av din röst personal. (Termen "*yttranden*" omfattar både fullständiga meningar och kortare fraser.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Ansvarsfull användning av AI

Information om hur du använder anpassade neurala Voice-ett ansvarsfullt sätt finns i [Oh-anteckningen](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Microsofts Oh-kommentarer är avsedda att hjälpa dig att förstå hur vår AI-teknik fungerar, vilka alternativ som system ägare kan göra för att påverka systemets prestanda och hur viktigt det är att tänka på hela systemet, inklusive teknik, människor och miljö.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Custom Voice](how-to-custom-voice.md)
* [Skapa och Använd en anpassad röst slut punkt](how-to-custom-voice-create-voice.md)