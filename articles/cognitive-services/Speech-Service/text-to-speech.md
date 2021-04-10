---
title: Översikt över text till tal – tal tjänst
titleSuffix: Azure Cognitive Services
description: Funktionen för text till tal i tal-tjänsten gör det möjligt för dina program, verktyg eller enheter att konvertera text till naturligt mänskligt syntetiskt tal. Den här artikeln är en översikt över fördelarna och funktionerna i tjänsten text till tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: text till tal
ms.openlocfilehash: 45977faec9ab54fed0c1e7054c567b4d18b11f50
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645024"
---
# <a name="what-is-text-to-speech"></a>Vad är text-till-tal?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

I den här översikten får du lära dig om fördelarna och funktionerna i tjänsten text till tal, som gör att dina program, verktyg eller enheter kan konvertera text till Human-liknande syntetiskt tal. Välj mellan standard-och neurala röster eller skapa en anpassad röst som är unik för din produkt eller ditt varumärke. 75 + standard röster är tillgängliga på fler än 45 språk och nationella inställningar och 5 neurala-röster är tillgängliga på ett valt antal språk och nationella inställningar. En fullständig lista över röster, språk och nationella inställningar som stöds finns i [språk som stöds](language-support.md#text-to-speech).

> [!NOTE]
> Taligenkänning i Bing togs ur bruk den 15 oktober 2019. Om dina program, verktyg eller produkter använder Taligenkänning i Bing-API: er eller Custom Speech har vi skapat guider som hjälper dig att migrera till tal tjänsten.
> - [Migrera från Taligenkänning i Bing till tal tjänsten](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Kärn funktioner

* Tal syntes – Använd [tal-SDK](./get-started-text-to-speech.md) eller [REST API](rest-text-to-speech.md) för att konvertera text till tal med hjälp av standard, neurala eller anpassade röster.

* Asynkron syntes av långt ljud – Använd den [långa ljud-API: n](long-audio-api.md) för att syntetisera text till tal-filer som är längre än 10 minuter (till exempel ljud böcker eller föreläsningar). Till skillnad från sammanfattningen som utfördes med hjälp av tal-SDK eller tal-till-text-REST API, returneras inte svar i real tid. Förväntat är att förfrågningar skickas asynkront, svaren avsökers för och att det syntetiserade ljudet hämtas när det görs tillgängligt från tjänsten. Endast anpassade neurala-röster stöds.

* Standard röster – skapade med statistisk parameter syntes och/eller kombinations sammanfattnings tekniker. Dessa röster är mycket anpassningsbara och har en naturlig naturlig effekt. Du kan enkelt göra det möjligt för dina program att prata på fler än 45 språk, med ett brett utbud av röst alternativ. Dessa röster ger hög uttal av precision, inklusive stöd för förkortningar, akronym-expansion, datum/tid-tolkningar, polytelefoner med mera. En fullständig lista över standard röster finns i [språk som stöds](language-support.md#text-to-speech).

* Neurala-röster – djup neurala nätverk används för att undvika gränserna för traditionella tal syntes med avseende på stress och intonation i talade språk. Prosody förutsägelse och röst syntes utförs samtidigt, vilket resulterar i mer Fluida och naturliga ljud. Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när du interagerar med AI-system. En fullständig lista över neurala-röster finns i [språk som stöds](language-support.md#text-to-speech).

* Ändra tal format med SSML-SSML (Speech syntes Markup Language) är ett XML-baserat kodspråk som används för att anpassa tal till text-utdata. Med SSML kan du justera bredden, lägga till pauser, förbättra uttal, påskynda eller sakta ned tal frekvens, öka eller minska volymen och attributa flera röster till ett enda dokument. Se [instruktionen så att du kan](speech-synthesis-markup.md) justera tal formatet.

* Visemes – [Visemes](how-to-speech-synthesis-viseme.md) är nyckeln i observerat tal, inklusive positionen för Lip, Jaw och tunga när man skapar en viss fonem. Visemes har en stark korrelation med röster och fonem. Med viseme-händelser i tal-SDK kan du skapa ansikts Animations data som kan användas för att animera ansikten i Lip-läsning av kommunikation, utbildning, underhållning och kund tjänst.

> [!NOTE]
> Viseme fungerar bara för `en-US-AriaNeural` Voice för tillfället.

## <a name="get-started"></a>Kom igång

Se [snabb start](get-started-text-to-speech.md) för att komma igång med text till tal. Tjänsten för text till tal är tillgänglig via [talet SDK](speech-sdk.md), [REST API](rest-text-to-speech.md)och [tal-CLI](spx-overview.md)

## <a name="sample-code"></a>Exempelkod

Exempel kod för text till tal finns på GitHub. De här exemplen tar upp text till tal-konvertering i de flesta populära programmeringsspråk.

- [Text till tal-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Anpassning

Förutom standard-och neurala-röster kan du skapa och finjustera anpassade röster som är unika för din produkt eller ditt varumärke. Allt det krävs för att komma igång är en fåtal av ljudfiler och tillhör ande avskrifter. Mer information finns i [Kom igång med anpassad röst](how-to-custom-voice.md)

## <a name="pricing-note"></a>Pris notering

När du använder text till tal-tjänsten debiteras du för varje tecken som konverteras till tal, inklusive interpunktion. SSML-dokumentet är inte fakturerbart, valfria element som används för att justera hur texten konverteras till tal, t. ex. fonem och färgdjup, räknas som fakturerbara tecken. Här är en lista över vad som är fakturerbara:

- Text som skickas till text till tal-tjänsten i SSML-delen av begäran
- Alla markeringar i textfältet för begär ande texten i SSML-format, förutom for- `<speak>` och- `<voice>` taggar
- Bokstäver, interpunktion, blank steg, tabbar, markeringar och alla blank stegs tecken
- Varje kod punkt som definierats i Unicode

Detaljerad information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Varje kinesiskt, japanskt och Koreanskt språk tecken räknas som två tecken för fakturering.

## <a name="reference-docs"></a>Referens dokument

- [Speech SDK](speech-sdk.md)
- [REST API: text till tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

- [Skaffa en prenumeration på kostnads fri röst tjänst](overview.md#try-the-speech-service-for-free)
- [Hämta tal-SDK](speech-sdk.md)
