---
title: Språk stöd – QnA Maker
titleSuffix: Azure Cognitive Services
description: En lista över kultur, naturliga språk som stöds av QnA Maker för din kunskaps bas. Blanda inte språk i samma kunskaps bas.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605067"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Språk stöd för en QnA Maker resurs-och kunskaps baser

I den här artikeln beskrivs alternativ för språk support för QnA Maker resurser och kunskaps baser. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Språk för tjänsten väljs när du skapar den första kunskaps basen i resursen. Alla ytterligare kunskaps baser i resursen måste vara på samma språk. 

Språket bestämmer relevansen hos resultaten QnA Maker ger svar på användar frågor. QnA Maker resursen och alla kunskaps baser i resursen, stöder ett enda språk. Det enda språket är nödvändigt för att ge bästa svars resultat för en fråga.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I QnA Maker hanterat kan du välja att göra språk inställningarna på den enskilda kunskaps bas nivån. Den här inställningen kan endast aktive ras med tjänstens kunskaps bas. När den har angetts kan inte språk inställningarna ändras för tjänsten. 

Om du väljer språk inställningar så att de är kunskapsbaserade, kan du skapa kunskaps baser om olika språk i själva tjänsten. 

---

## <a name="single-language-per-resource"></a>Enskilt språk per resurs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Tänk också på följande:

* En QnA Maker tjänst, och alla dess kunskaps baser, stöder bara ett språk.
* Språket anges explicit när den första kunskaps basen i tjänsten skapas.
* Språket bestäms av de filer och URL: er som läggs till när kunskaps basen skapas.
* Det går inte att ändra språket för andra kunskaps banker i tjänsten.
* Språket används av Kognitiv sökning tjänsten (Ranging #1) och QnA Maker tjänsten (rangation #2) för att generera det bästa svaret på en fråga.

# <a name="qnamaker-managed-preview"></a>[QnAMaker-hanterad (för hands version)](#tab/v2)
![Språk inställning i QnA Maker hanterad](../media/language-support/language-setting-managed.png)

Om du **inte markerar kryss rutan för att aktivera språk inställningen per kunskaps bas** bör du tänka på följande: 
* En QnA Maker tjänst och alla dess kunskaps Bases har stöd för ett språk.
* Språket anges explicit när den första kunskaps basen i tjänsten skapas
* Språket bestäms av de filer och URL: er som läggs till när kunskaps basen skapas
* Det går inte att ändra språket för andra kunskaps banker i tjänsten
* Språket används av Kognitiv sökning tjänsten (Ranging #1) och QnA Maker tjänsten (Ranging #2) för att generera det bästa svaret på en fråga

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Stöd för flera språk i en QnA Maker resurs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)
Den här funktionen stöds inte i vår aktuella allmänt tillgängliga (GA) stabila version. Kolla QnA Maker Managed för att testa den här funktionen. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)
* När du skapar den första kunskaps basen i din tjänst kan du välja att aktivera språk inställningen per kunskaps bas. Markera kryss rutan för att skapa kunskaps baser som tillhör olika språk i en tjänst.
* Det går inte att ändra språk inställnings alternativet för tjänsten när den första kunskaps basen har skapats.
* Om du aktiverar språk inställningar som är specifika för varje kunskaps bas måste du i stället för att ha ett test index för tjänsten ha ett test index per kunskaps bas. 

![Språk inställning i QnA Maker hanterad](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Stöd för flera språk i en kunskaps bas

Om du behöver stöd för ett kunskaps bas system, som innehåller flera språk, kan du:

* Använd [tjänsten Translator](../../translator/translator-info-overview.md) för att översätta en fråga till ett språk innan du skickar frågan till din kunskaps bas. På så sätt kan du fokusera på kvaliteten på ett enda språk och kvaliteten på de alternativa frågorna och svaren.
* Skapa en QnA Maker resurs och en kunskaps bas i resursen för varje språk. På så sätt kan du hantera separata alternativa frågor och svars text som är mer nyanserade för varje språk. Detta ger dig mycket mer flexibilitet men kräver en mycket högre underhålls kostnad när frågorna eller Svaren ändras på alla språk.


## <a name="languages-supported"></a>Språk som stöds

Följande lista innehåller de språk som stöds för en QnA Maker-resurs. 

| Språk |
|--|
| Arabiska |
| Armeniska |
| Bangla |
| Baskiska |
| Bulgariska |
| Katalanska |
| Chinese_Simplified |
| Chinese_Traditional |
| Kroatiska |
| Tjeckiska |
| Danska |
| Nederländska |
| Engelska |
| Estniska |
| Finska |
| Franska |
| Galiciska |
| Tyska |
| Grekiska |
| Gujarati |
| Hebreiska |
| Hindi |
| Ungerska |
| Isländska |
| Indonesiska |
| Iriska |
| Italienska |
| Japanska |
| Kannada |
| Koreanska |
| Lettiska |
| Litauiska |
| Malayalam |
| Malajiska |
| Norska |
| Polska |
| Portugisiska |
| Punjabi |
| Rumänska |
| Ryska |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slovakiska |
| Slovenska |
| Spanska |
| Svenska |
| Tamilska |
| Telugu |
| Thailändska |
| Turkiska |
| Ukrainska |
| Urdu |
| Vietnamesiska |

## <a name="query-matching-and-relevance"></a>Fråga matchning och relevans
QnA Maker är beroende av [Azure kognitiv sökning språk analys](/rest/api/searchservice/language-support) verktyg för att ge resultat.

Även om Azure Kognitiv sökning-funktionerna är på parivärde för språk som stöds, har QnA Maker ytterligare rangordning som ligger ovanför Azure Search-resultaten. I den här ranknings modellen använder vi vissa särskilda semantiska och Word-baserade funktioner på följande språk.

|Språk med ytterligare rangordning|
|--|
|Kinesiska|
|Tjeckiska|
|Nederländska|
|Engelska|
|Franska|
|Tyska|
|Ungerska|
|Italienska|
|Japanska|
|Koreanska|
|Polska|
|Portugisiska|
|Spanska|
|Svenska|

Den här ytterligare rangordningen är en intern bearbetning av QnA Makers rang.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Val av språk](../index.yml)