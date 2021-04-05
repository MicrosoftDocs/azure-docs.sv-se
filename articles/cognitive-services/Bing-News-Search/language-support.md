---
title: Språk stöd – API för nyhetssökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341605"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Språk-och region stöd för API för nyhetssökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API för nyhetssökning i Bing stöder flera länder/regioner, många med fler än ett språk. Om du anger ett land/en region med en fråga, är det främst att förfina Sök resultat baserat på intressen i landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/region eller språk.

Du kan ange ett land/en region med hjälp av `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` http-huvudet. Vilka språk som stöds varierar beroende på regioner/region; de anges för varje land/region i tabellen marknad.

Alternativt kan du ange marknaden med hjälp av `mkt` Frågeparametern och en kod från tabellen **marknader** . Att ange en marknad samtidigt anger ett land/en region och ett önskat språk. `setLang`Frågeparametern kan anges till en språkkod i det här fallet, vanligt vis är det samma språk som anges av `mkt` om inte användaren föredrar att se Bing på ett annat språk.

## <a name="supported-markets-for-news-search-endpoint"></a>Marknader som stöds för nyhets sökningens slut punkt

I `/news/search` följande tabell visas de marknads kods värden som du kan använda för att ange Frågeparametern för slut punkten `mkt` . Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i Frågeparametern `cc` , se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Österrike|Tyska|avinstallation|
|Schweiz|Tyska|de-CH|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|en – AU|
|Kanada|Engelska|en-CA|
|Storbritannien|Engelska|en-GB|
|Indonesien|Engelska|en-ID|
|Irland|Engelska|en-IE|
|Indien|Engelska|en-IN|
|Malaysia|Engelska|en-MY|
|Nya Zeeland|Engelska|en-NZ|
|Filippinerna|Engelska|en-PH|
|Singapore|Engelska|en-SG|
|USA|Engelska|sv-SE|
|Engelska|allmänt|en-WW|
|Engelska|allmänt|en-XA|
|Sydafrika|Engelska|en-ZA|
|Argentina|Spanska|ES-AR|
|Chile|Spanska|ES-CL|
|Spanien|Spanska|es-ES|
|Mexico|Spanska|ES – MX|
|USA|Spanska|ES – USA|
|Spanska|allmänt|ES – XL|
|Finland|Finska|fi-FI|  
|Frankrike|Franska|fr-vara|
|Kanada|Franska|fr-CA|
|Belgien|Nederländska|NL-vara|
|Schweiz|Franska|Frankrike-CH|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|it-IT|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|Traditionell kinesiska|zh-HK|  
|Taiwan|Traditionell kinesiska|zh-TW|
|Japan|Japanska|ja-JP|  
|Korea|Koreanska|ko-KR|  
|Nederländerna|Nederländska|nl-NL|  
|Folkrepubliken Kina|Kinesiska|zh-CN|  
|Brasilien|Portugisiska|pt-BR|
|Ryssland|Ryska|ru-RU|  
|Sverige|Svenska|sv-SE|  
|Turkiet|Turkiska|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Marknader som stöds för diskussions grupps slut punkt
I `/news` följande tabell visas de marknads kods värden som du kan använda för att ange Frågeparametern för slut punkten `mkt` . Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i Frågeparametern `cc` , se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|en – AU|
|Storbritannien|Engelska|en-GB|
|USA|Engelska|sv-SE|
|Engelska|allmänt|en-WW|
|Chile|Spanska|ES-CL|
|Mexico|Spanska|ES – MX|
|USA|Spanska|ES – USA|
|Finland|Finska|fi-FI|  
|Kanada|Franska|fr-CA|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|it-IT|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marknader som stöds för nyhets Trends slut punkt
I `/news/trendingtopics` följande tabell visas de marknads kods värden som du kan använda för att ange Frågeparametern för slut punkten `mkt` . Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i Frågeparametern `cc` , se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|en – AU|
|Storbritannien|Engelska|en-GB|
|USA|Engelska|sv-SE|
|Kanada|Engelska|en-CA|
|Indien|Engelska|en-IN|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Lands koder  

Följande är de lands-/regions koder som du kan ange i `cc` Frågeparametern. Listan kan komma att ändras.  

|Land/region|Landskod|  
|---------------------|------------------|  
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
|Folkrepubliken Kina|CN|  
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

## <a name="next-steps"></a>Nästa steg
Mer information om Nyhetssökning i Bing slut punkter finns i [NYHETSSÖKNING API v7-referens](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).