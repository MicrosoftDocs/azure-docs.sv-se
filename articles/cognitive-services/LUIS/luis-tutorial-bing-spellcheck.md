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
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731042"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Korrigera felstavade ord med Bing-resurs

V3-förutsägelse API har nu stöd för [Bing API för stavnings](/bing/search-apis/bing-spell-check/overview)kontroll. Lägg till stavnings kontroll i programmet genom att inkludera nyckeln till din Bing search-resurs i rubriken för dina begär Anden. Du kan använda en befintlig Bing-resurs om du redan äger en, eller [skapa en ny](https://portal.azure.com/#create/Microsoft.BingSearch) för att använda den här funktionen. 

Exempel på förutsägelse av utdata för en felstavad fråga:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Korrigeringar av stavning görs före LUIS User uttryck förutsägelse. Du kan se ändringar i den ursprungliga uttryck, inklusive stavning, i svaret.

## <a name="create-bing-search-resource"></a>Skapa Bing-sökning resurs

Följ dessa instruktioner om du vill skapa en Bing-sökning resurs i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **skapa en resurs** i det övre vänstra hörnet.

3. I rutan Sök anger `Bing Search V7` du och väljer tjänsten.

4. En informations panel visas till höger som innehåller information, inklusive juridiskt meddelande. Välj **skapa** för att starta processen för att skapa prenumerationer.

> [!div class="mx-imgBorder"]
> ![API för stavningskontroll i Bing v7-resurs](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. I nästa panel anger du dina tjänst inställningar. Vänta tills processen har skapats.

6. När resursen har skapats går du till bladet **nycklar och slut punkt** till vänster. 

7. Kopiera en av nycklarna som ska läggas till i rubriken för din förutsägelse förfrågan. Du behöver bara en av de två nycklarna.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Aktivera stavnings kontroll från användar gränssnittet 
Du kan aktivera stavnings kontroll för din exempel fråga med hjälp av [Luis-portalen](https://www.luis.ai). Välj **Hantera** överst på skärmen och **Azure-resurser** i det vänstra navigerings fältet. När du har kopplat en förutsägelse resurs till ditt program kan du välja **ändra** frågeparametrar längst ned på sidan och klistra in resurs nyckeln i fältet **Aktivera stavnings kontroll** .
    
   > [!div class="mx-imgBorder"]
   > ![Aktivera stavnings kontroll](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Lägger till nyckeln i slut punkts-URL: en
För varje fråga som du vill tillämpa stavnings korrigering på måste slut punkts frågan ha en resurs nyckel för Bing-stavnings kontroll som skickas i frågesträngparametern. Du kan ha en chattrobot som anropar LUIS eller så kan du anropa LUIS slut punkts-API: n direkt. Oavsett hur slut punkten anropas måste varje anrop innehålla den information som krävs i huvudets begäran om att stavnings korrigeringar ska fungera korrekt. Du måste ange värdet med **mkt-Bing-stavning-check-Key** till Key-värdet.

|Rubrik nyckel|Huvud värde|
|--|--|
|`mkt-bing-spell-check-key`|Nycklar hittades i bladet **nycklar och slut punkter** i din resurs|

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