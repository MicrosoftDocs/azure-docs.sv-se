---
title: Korrigera felstavade ord – LUIS
titleSuffix: Azure Cognitive Services
description: Korrigera felstavade ord i yttranden genom att lägga till API för stavningskontroll i Bing v7 till LUIS slut punkts frågor.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179647"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Korrigera felstavade ord med Bing-sökning resurs

Du kan integrera din LUIS-app med [Bing-sökning](https://ms.portal.azure.com/#create/Microsoft.BingSearch) för att korrigera felstavade ord i yttranden innan Luis förutsäger poängen och enheterna i uttryck.

## <a name="create-endpoint-key"></a>Skapa slut punkts nyckel

Följ dessa instruktioner om du vill skapa en Bing-sökning resurs i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **skapa en resurs** i det övre vänstra hörnet.

3. I rutan Sök anger `Bing Search V7` du och väljer tjänsten.

4. En informations panel visas till höger som innehåller information, inklusive juridiskt meddelande. Välj **skapa** för att starta processen för att skapa prenumerationer.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="API för stavningskontroll i Bing v7-resurs":::

5. I nästa panel anger du dina tjänst inställningar. Vänta tills processen har skapats.

6. När resursen har skapats går du till bladet **nycklar och slut punkt** till vänster. 

7. Kopiera en av nycklarna som ska läggas till i rubriken för din förutsägelse förfrågan. Du behöver bara en av de två nycklarna.

8. Lägg till nyckeln `mkt-bing-spell-check-key` i i huvudet för förutsägelse begär Ande.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Lägger till nyckeln i slut punkts-URL: en
För varje fråga som du vill tillämpa stavnings korrigering på måste slut punkts frågan ha en resurs nyckel för Bing-stavnings kontroll som skickas i frågesträngparametern. Du kan ha en chattrobot som anropar LUIS eller så kan du anropa LUIS slut punkts-API: n direkt. Oavsett hur slut punkten anropas måste varje anrop innehålla den information som krävs i huvudets begäran om att stavnings korrigeringar ska fungera korrekt. Du måste ange värdet med **mkt-Bing-stavning-check-Key** till Key-värdet.


## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade uttryck till LUIS
1. Lägg till en felstavad uttryck i förutsägelse frågan som du kommer att skicka som "hur långt är mountainn?". I engelska `mountain` är det med en `n` korrekt stavning.

2. LUIS svarar med ett JSON-resultat för `How far is the mountain?` . Om API för stavningskontroll i Bing v7 identifierar ett stavfel `query` innehåller fältet i Luis-appens JSON-svar den ursprungliga frågan och `alteredQuery` fältet innehåller den korrigerade frågan som skickats till Luis.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorera stavfel

Om du inte vill använda tjänsten Bing-sökning-API-v7 måste du lägga till korrekt och felaktig stavning.

Två lösningar:

* Label-exempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och skrivfel. Det här alternativet kräver mer märknings arbete än att använda en stavnings kontroll.
* Skapa en fras lista med alla varianter av ordet. Med den här lösningen behöver du inte märka ord variationerna i exemplet yttranden.


> [!div class="nextstepaction"]
> [Läs mer om exempel yttranden](./luis-how-to-add-entities.md)
