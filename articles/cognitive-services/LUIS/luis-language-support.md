---
title: Språk stöd – LUIS
titleSuffix: Azure Cognitive Services
description: Det finns flera olika funktioner i tjänsten LUIS. Vilka funktioner som stöds varierar mellan olika språk. Kontrollera att de funktioner som du är intresserad av stöds i målspråkkulturen. En LUIS-app är landsspecifika och kan inte ändras när den har angetts.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: b60a777960f3c6f8a2415ca551217d35b7f3497d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588030"
---
# <a name="language-and-region-support-for-luis"></a>Språk-och region stöd för LUIS

Det finns flera olika funktioner i tjänsten LUIS. Vilka funktioner som stöds varierar mellan olika språk. Kontrollera att de funktioner som du är intresserad av stöds i målspråkkulturen. En LUIS-app är landsspecifika och kan inte ändras när den har angetts.

## <a name="multi-language-luis-apps"></a>LUIS-appar med flera språk

Om du behöver ett LUIS-klient program med flera språk, till exempel en chattrobot, har du några alternativ. Om LUIS har stöd för alla språk utvecklar du en LUIS-app för varje språk. Varje LUIS-app har ett unikt app-ID och slut punkts logg. Om du behöver ange språk förståelse för ett språk LUIS inte stöder kan du använda [Translator-tjänsten](../Translator/translator-info-overview.md) för att översätta uttryck till ett språk som stöds, skicka uttryck till Luis-slutpunkten och ta emot de resulterande poängen.

## <a name="languages-supported"></a>Språk som stöds

LUIS förstår yttranden på följande språk:

| Språk |Nationell inställning  |  Fördefinierad domän | Fördefinierad entitet | Rekommendationer för fras lista | **[Text analys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment och<br>Reserverade|
|--|--|:--:|:--:|:--:|:--:|
| Engelska (USA) |`en-US` | ✔ | ✔  |✔|✔|
| Arabiska (förhands granskning-modern standard arabisk) |`ar-AR`|-|-|-|-|
| *[Kinesiska](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederländska |`nl-NL` |✔|-|-|✔|
| Franska (Frankrike) |`fr-FR` |✔| ✔ |✔ |✔|
| Franska (Kanada) |`fr-CA` |-|-|-|✔|
| Tyska |`de-DE` |✔| ✔ |✔ |✔|
| Gujarati | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Italienska |`it-IT` |✔| ✔ |✔|✔|
| *[Japanska](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Endast nyckel fras|
| Koreanska |`ko-KR` |✔|-|-|Endast nyckel fras|
| Marathi | `mr-IN`|-|-|-|-|
| Portugisiska (Brasilien) |`pt-BR` |✔| ✔ |✔ |alla under kulturer|
| Spanska (Spanien) |`es-ES` |✔| ✔ |✔|✔|
| Spanska (Mexiko)|`es-MX` |-|-|✔|✔|
| Tamilska | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turkiska | `tr-TR` |✔|✔|-|Endast sentiment|




Språk stöd varierar för [färdiga entiteter](luis-reference-prebuilt-entities.md) och [fördefinierade domäner](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>* Stöd anteckningar för japanska

 - Eftersom LUIS inte tillhandahåller syntaktisk analys och inte kommer att förstå skillnaden mellan Keigo och informell japanska, måste du inkludera de olika nivåerna av formaliteter som inlärnings exempel för dina program.
     - でございます är inte samma som です.
     - です är inte samma som だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Speech API språk som stöds
Se språk som [stöds](../speech-service/speech-to-text.md) av tal i tal för tallägen.

### <a name="bing-spell-check-supported-languages"></a>Stavningskontroll i Bing språk som stöds
Se Stavningskontroll i Bing [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) för en lista över språk och status som stöds.

## <a name="rare-or-foreign-words-in-an-application"></a>Ovanliga eller främmande ord i ett program
I `en-us` kulturen lär sig Luis att skilja ut de flesta engelska ord, inklusive slang. I `zh-cn` kulturen lär sig Luis att skilja på de flesta kinesiska tecknen. Om du använder ett sällsynt ord i `en-us` eller en bokstav i `zh-cn` , och du ser att Luis inte verkar särskilja ordet eller specialtecknet, kan du lägga till ordet eller symbolen i en [fras List funktion](luis-how-to-add-features.md). Till exempel ord utanför programmets kultur – det vill säga främmande ord – ska läggas till i en fras List funktion.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybrid språk
Hybrid språk kombinerar ord från två kulturer, till exempel engelska och kinesiska. Dessa språk stöds inte i LUIS eftersom en app baseras på en enda kultur.

## <a name="tokenization"></a>Tokenisering
För att utföra maskin inlärning bryter LUIS en uttryck till [token](luis-glossary.md#token) baserat på kulturen.

|Språk|  varje mellanslag eller specialtecken | Character-nivå|sammansatta ord
|--|:--:|:--:|:--:|
|Arabiska|✔|||
|Kinesiska||✔||
|Nederländska|✔||✔|
|Engelska (en-us)|✔ |||
|Franska (fr-FR)|✔|||
|Franska (fr-CA)|✔|||
|Tyska|✔||✔|
|Gujarati|✔|||
|Hindi|✔|||
|Italienska|✔|||
|Japanska|||✔
|Koreanska||✔||
|Marathi|✔|||
|Portugisiska (Brasilien)|✔|||
|Spanska (es-ES)|✔|||
|Spanska (ES-MX)|✔|||
|Tamilska|✔|||
|Telugu|✔|||
|Turkiska|✔|||


### <a name="custom-tokenizer-versions"></a>Anpassade tokenizer-versioner

Följande kulturer har anpassade tokenizer-versioner:

|Culture (Kultur)|Version|Syfte|
|--|--|--|
|Tyska<br>`de-de`|1.0.0|Tokenizes ord genom att dela dem med hjälp av en Machine Learning-baserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare anger `Ich fahre einen krankenwagen` som en uttryck, aktive ras den `Ich fahre einen kranken wagen` . Tillåta markering av `kranken` och `wagen` oberoende som olika entiteter.|
|Tyska<br>`de-de`|1.0.2|Tokenizes ord genom att dela dem på blank steg.<br> Om en användare anges `Ich fahre einen krankenwagen` som en uttryck, förblir den en enda token. `krankenwagen`Markeras därför som en enskild entitet. |
|Nederländska<br>`nl-nl`|1.0.0|Tokenizes ord genom att dela dem med hjälp av en Machine Learning-baserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare anger `Ik ga naar de kleuterschool` som en uttryck, aktive ras den `Ik ga naar de kleuter school` . Tillåta markering av `kleuter` och `school` oberoende som olika entiteter.|
|Nederländska<br>`nl-nl`|1.0.1|Tokenizes ord genom att dela dem på blank steg.<br> Om en användare anges `Ik ga naar de kleuterschool` som en uttryck, förblir den en enda token. `kleuterschool`Markeras därför som en enskild entitet. |


### <a name="migrating-between-tokenizer-versions"></a>Migrera mellan tokenizer-versioner
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokenisering sker på App-nivå. Det finns inget stöd för tokenisering på versions nivå.

[Importera filen som en ny app](luis-how-to-start-new-app.md)i stället för en version. Den här åtgärden innebär att den nya appen har ett annat app-ID men använder den tokenizer-version som anges i filen.
