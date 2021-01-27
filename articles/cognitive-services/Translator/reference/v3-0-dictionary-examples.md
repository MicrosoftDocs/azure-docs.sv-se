---
title: Exempel metod för Translator-ordlista
titleSuffix: Azure Cognitive Services
description: Exempel metoden Translator-ordlista innehåller exempel som visar hur termer i ord listan används i kontexten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895517"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3,0: exempel på ord lista

Innehåller exempel som visar hur termer i ord listan används i kontexten. Den här åtgärden används i tandem med [ord](./v3-0-dictionary-lookup.md)lists ökning.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

| Frågeparameter | Description |
| --------- | ----------- |
| api-version <img width=200/> | **Obligatorisk parameter**.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0` . |
| Från | **Obligatorisk parameter**.<br/>Anger språket för inmatad text. Käll språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary` omfånget. |
| på | **Obligatorisk parameter**.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary` omfånget.  | 

Begärandehuvuden innehåller:

| Sidhuvuden  | Description |
| ------ | ----------- |
| Authentication-huvud (er) <img width=200/>  | **Begär ande huvud för begäran**.<br/>Se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Begär ande huvud för begäran**.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json` . |
| Innehålls längd   | **Begär ande huvud för begäran**.<br/>Längden på begär ande texten. |
| X-ClientTraceId   | **Valfritt**.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId` . |

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med följande egenskaper:

  * `Text`: En sträng som anger termen som ska sökas. Detta bör vara värdet för ett `normalizedText` fält från back-translations för en föregående [ord söknings](./v3-0-dictionary-lookup.md) förfrågan. Det kan också vara värdet för `normalizedSource` fältet.

  * `Translation`: En sträng som anger den översatta text som tidigare returnerades av [ord listans Sök](./v3-0-dictionary-lookup.md) åtgärd. Detta ska vara värdet från `normalizedTarget` fältet i `translations` listan över Sök svaret för [ord listan](./v3-0-dictionary-lookup.md) . Tjänsten kommer att returnera exempel för det specifika käll målets Word-par.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 10 element.
* Text värden för ett mat ris element får inte överstiga 100 tecken inklusive blank steg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger en normaliserad form av käll termen. I allmänhet ska detta vara identiskt med värdet för `Text` fältet i det matchande List indexet i bröd texten i begäran.
    
  * `normalizedTarget`: En sträng som ger normaliserad form av mål termen. I allmänhet ska detta vara identiskt med värdet för `Translation` fältet i det matchande List indexet i bröd texten i begäran.
  
  * `examples`: En lista med exempel på paret (käll termen, mål termen). Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: Strängen som ska sammanfogas _innan_ värdet för `sourceTerm` utgör ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: En sträng som motsvarar den faktiska termen som har sökts upp. Strängen läggs till med `sourcePrefix` och används `sourceSuffix` för att skapa hela exemplet. Värdet är avgränsat så att det kan markeras i ett användar gränssnitt, t. ex. med fetstil.

    * `sourceSuffix`: Strängen som ska sammanfogas _efter_ värdet för `sourceTerm` att skapa ett fullständigt exempel. Lägg inte till ett blank stegs tecken eftersom det redan finns där. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: En sträng som liknar `sourcePrefix` men för målet.

    * `targetTerm`: En sträng som liknar `sourceTerm` men för målet.

    * `targetSuffix`: En sträng som liknar `sourceSuffix` men för målet.

    > [!NOTE]
    > Om det inte finns några exempel i ord listan är svaret 200 (OK), men `examples` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du söker efter exempel för paret som består av den engelska `fly` och dess Spanska Översättning `volar` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Svars texten (förkortat för tydlighetens skull) är:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```