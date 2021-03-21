---
title: Språk stöd – API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Ta reda på vilka länder/regioner och språk som stöds av API för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 14dacc4351af444c75517df4d4e02ecca3bbf40a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350083"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Språk-och region stöd för API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API för bildsökning i Bing har stöd för fler än tre dussin länder/regioner, många med fler än ett språk. Om du anger ett land/en region med en fråga, är det främst att förfina Sök resultat baserat på intressen i landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/regioner eller språk.

Om du vill ange land/region och språk ställer du in `mkt` Frågeparametern (marknads) till en kod från tabellen **marknader** nedan. Marknaden anger både land/region och språk. Om användaren vill se visnings text på ett annat språk anger `setLang` du Frågeparametern till rätt språkkod.

Alternativt kan du ange land/region med `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` http-huvudet. Vilka språk som stöds varierar beroende på land/region; de anges för varje land/region i tabellen marknad.

> [!NOTE]
> API: et för Trends images stöder för närvarande endast följande marknader:
> - en-US (engelska, USA)
> - en-CA (engelska, Kanada)
> - en – AU (engelska, Australien)
> - zh-CN (kinesiska, Kina)

## <a name="countriesregions"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
|Argentina|AR|
|Australien|AU|
|Österrike|AT|
|Belgien|BE|
|Brasilien|BR|
|Kanada|CA|
|Chile|CL|
|Danmark|DK|
|Finland|FI|
|Frankrike|FR|
|Tyskland|DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|HK|
|Indien|IN|
|Indonesien|ID|
|Italien|IT|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexico|MX|
|Nederländerna|NL|
|Nya Zeeland|NZ|
|Norge|NO|
|Kina|CN|
|Polen|PL|
|Portugal|PT|
|Filippinerna|PH|
|Ryssland|RU|
|Saudiarabien|SA|
|Sydafrika|ZA|
|Spanien|ES|
|Sverige|SE|
|Schweiz|CH|
|Taiwan|TW|
|Turkiet|TR|
|Storbritannien|GB|
|USA|USA|


## <a name="markets"></a>Marknaden

|Land/region|Språk|Marknads kod|
|-------|--------|-----------|
|Argentina|Spanska|ES-AR|
|Australien|Engelska|en – AU|
|Österrike|Tyska|avinstallation|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Engelska|en-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|Traditionell kinesiska|zh-HK|
|Indien|Engelska|en-IN|
|Indonesien|Engelska|en-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Engelska|en-MY|
|Mexico|Spanska|ES – MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Engelska|en-NZ|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Engelska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Engelska|en-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|Frankrike-CH|
|Schweiz|Tyska|de-CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Engelska|en-GB|
|USA|Engelska|sv-SE|
|USA|Spanska|ES – USA|

## <a name="next-steps"></a>Nästa steg
Mer information om Nyhetssökning i Bing slut punkter finns i [nyheter bildsökning API v7 Reference](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).