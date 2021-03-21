---
title: Språk stöd – API för visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för visuell sökning i Bing. API för visuell sökning i Bing har stöd för fler än tre dussin länder/regioner, många med fler än ett språk.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 6d93b6d024fc966f3b260602f4798a2d77669a35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96501166"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Språk-och region stöd för API för visuell sökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API för visuell sökning i Bing stöder fler än tre dussin länder/regioner, många med fler än ett språk. Varje begäran bör innehålla användarens land/region och valfritt språk. Att känna till användarens marknad hjälper Bing att returnera lämpliga resultat. Om du inte anger något land/region och språk, gör Bing det bästa arbetet med att fastställa användarens land/region och språk. Eftersom resultaten kan innehålla länkar till Bing, kan det vara en prioriterad lokaliserad Bing-användare om användaren klickar på Bing-länkarna om du vet att landet/regionen och språket har en prioriterad lokaliserad Bing-upplevelse

Om du vill ange land/region och språk ställer du in `mkt` Frågeparametern (marknads) till en kod från tabellen **marknader** nedan. Marknaden anger både land/region och språk. Om användaren vill se visnings text på ett annat språk anger `setLang` du Frågeparametern till rätt språkkod.

Alternativt kan du ange land/region med `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` http-huvudet. Vilka språk som stöds varierar beroende på land/region; de anges för varje land i tabellen marknad.



> [!NOTE]
> Följande marknads begränsningar gäller:
>
> - Bild igenkännings anteckningar finns bara på engelska.
> - Recept, shopping och sidor – inklusive insikter finns bara på en-amerikanska marknaden.


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