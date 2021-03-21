---
title: Översikt över tal till text – tal tjänst
titleSuffix: Azure Cognitive Services
description: Tal-till-text-programvara möjliggör real tids avskrift av ljud strömmar till text. Dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för den här text ingången. Den här artikeln är en översikt över fördelarna och funktionerna i tjänsten tal-till-text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: tal till text, tal till text program
ms.openlocfilehash: 5e593202a8cd68dfc4a92f991d573b4b06051b36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434327"
---
# <a name="what-is-speech-to-text"></a>Vad är tal-till-text?

I den här översikten får du lära dig om fördelarna och funktionerna i tjänsten tal-till-text.
Tal-till-text, även känt som tal igenkänning, aktiverar real tids avskrift av ljud strömmar till text. Dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder på den här texten som kommando inmatade. Den här tjänsten drivs av samma igenkännings teknik som Microsoft använder för Cortana och Office-produkter. Det fungerar sömlöst med <a href="./speech-translation.md" target="_blank">översättnings </a> -och <a href="./text-to-speech.md" target="_blank">text till tal </a> -tjänstens erbjudanden. En fullständig lista över tillgängliga tal-till-text-språk finns i [språk som stöds](language-support.md#speech-to-text).

Tjänsten tal-till-text används som standard för att använda den universella språk modellen. Den här modellen tränade med Microsoft-ägda data och distribueras i molnet. Det är optimalt för konversations-och dikterings scenarier. När du använder tal-till-text för igenkänning och avskrift i en unik miljö kan du skapa och träna anpassade modeller för akustisk, språk och uttal. Anpassning är användbart för att adressera omgivande brus eller branschspecifika vokabulär.

Med ytterligare referens text som indata [kan du använda funktionen tal](rest-speech-to-text.md#pronunciation-assessment-parameters) -till-text för att utvärdera tal uttal och ge talare feedback om precisionen och Fluency av talade ljud. Med uttals-utvärdering kan språklärare öva, få snabb feedback och förbättra sina uttal så att de kan tala och presentera med förtroende. Lärare kan använda funktionen för att utvärdera uttal av flera högtalare i real tid. Funktionen stöder för närvarande amerikansk engelska och korrelerar mycket med tal utvärderingar som utförs av experter.

> [!NOTE]
> Taligenkänning i Bing togs ur bruk den 15 oktober 2019. Om dina program, verktyg eller produkter använder Taligenkänning i Bing API: er har vi skapat guider som hjälper dig att migrera till tal tjänsten.
> - [Migrera från Taligenkänning i Bing till tal tjänsten](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Kom igång

Se [snabb start](get-started-speech-to-text.md) för att komma igång med tal till text. Tjänsten är tillgänglig via [talet SDK](speech-sdk.md), [REST API](rest-speech-to-text.md#pronunciation-assessment-parameters)och [tal-CLI](spx-overview.md).

## <a name="sample-code"></a>Exempelkod

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller.

- [Tal till text-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exempel på uttal av uttal (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Anpassning

Förutom standard modellen för tal tjänster kan du skapa anpassade modeller. Anpassningen hjälper till att undvika hinder för tal igenkänning, till exempel talad stil, vokabulär och bakgrunds brus, se [Custom Speech](./custom-speech-overview.md). Anpassnings alternativ varierar beroende på språk och nationella inställningar, se [språk som stöds](./language-support.md) för att kontrol lera stödet.

## <a name="batch-transcription"></a>Batch-transkription

Batch-avskrift är en uppsättning REST API åtgärder som gör det möjligt att skriva av en stor mängd ljud i lagring. Du kan peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och asynkront ta emot transkriptionsresultat. Mer information om hur du använder batch-avskrifts-API: et finns i [instruktionen How-to](batch-transcription.md) .

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Nästa steg

- [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
- [Hämta tal-SDK](speech-sdk.md)