---
title: Data extrahering – LUIS
description: Extrahera data från uttryck text med avsikter och entiteter. Lär dig vilken typ av data som kan extraheras från Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026001"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahera data från uttryck text med avsikter och entiteter
LUIS ger dig möjlighet att hämta information från en användares naturliga språk yttranden. Informationen extraheras på ett sätt som kan användas av ett program, program eller en chatt-robot för att vidta åtgärder. I följande avsnitt lär du dig vilka data som returneras från avsikter och entiteter med exempel på JSON.

De hårda data som ska extraheras är maskin inlärnings data eftersom det inte är en exakt text matchning. Data extrahering av [enheter](luis-concept-entity-types.md) för maskin inlärning måste vara en del av [redigerings cykeln](luis-concept-app-iteration.md) tills du är säker på att du får de data du förväntar dig.

## <a name="data-location-and-key-usage"></a>Data plats och nyckel användning
LUIS extraherar data från användarens uttryck vid den publicerade [slut punkten](luis-glossary.md#endpoint). **Https-begäran** (post eller get) innehåller uttryck samt vissa valfria konfigurationer som till exempel mellanlagrings-eller produktions miljöer.

**V2-begäran om slut punkts förutsägelse**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 förutsägelse slut punkts förfrågan**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

`appID`Är tillgänglig på sidan **Inställningar** i Luis-appen samt en del av URL: en (efter `/apps/` ) när du redigerar den Luis-appen. `subscription-key`Är den slut punkts nyckel som används för att skicka frågor till din app. Även om du kan använda din kostnads fria redigerings-/start nyckel när du lär dig LUIS, är det viktigt att ändra slut punkts nyckeln till en nyckel som stöder din [förväntade Luis-användning](luis-limits.md#key-limits). `timezoneOffset`Enheten är minuter.

**Https-svaret** innehåller all information om Luis och entiteten som kan fastställas baserat på den aktuella publicerade modellen för antingen mellanlagrings-eller produktions slut punkten. Slut punkts-URL: en finns på [Luis](luis-reference-regions.md) -webbplatsen i avsnittet **Hantera** på sidan **nycklar och slut punkter** .

## <a name="data-from-intents"></a>Data från avsikter
Primära data är det översta namnet på bedömnings **avsikten**. Slut punktens svar är:

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * *

|Data objekt|Datatyp|Dataplats|Värde|
|--|--|--|--|
|Avsikt|Sträng|topScoringIntent. avsikt|"GetStoreInfo"|

Om din chattrobot-eller LUIS-anropande app fattar ett beslut baserat på fler än ett avsikts poäng, returnerar du alla Intents-resultat.


#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

Ange parametern QueryString, `verbose=true` . Slut punktens svar är:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Ange parametern QueryString, `show-all-intents=true` . Slut punktens svar är:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * *

Avsikterna är sorterade från högsta till lägsta poäng.

|Data objekt|Datatyp|Dataplats|Värde|Poäng|
|--|--|--|--|:--|
|Avsikt|Sträng|avsikter [0]. avsikt|"GetStoreInfo"|0,984749258|
|Avsikt|Sträng|avsikter [1]. avsikt|Alternativet|0,0168218873|

Om du lägger till fördefinierade domäner, anger namn på avsikt domänen, t. ex. `Utilties` eller och `Communication` avsikten:

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * *

|Domain|Data objekt|Datatyp|Dataplats|Värde|
|--|--|--|--|--|
|Verktyg|Avsikt|Sträng|avsikter [0]. avsikt|"<b>Verktyg</b>. ShowNext"|
|Kommunikation|Avsikt|Sträng|avsikter [1]. avsikt|<b>Kommunikation</b>. StartOver"|
||Avsikt|Sträng|avsikter [2]. avsikt|Alternativet|


## <a name="data-from-entities"></a>Data från entiteter
De flesta chatt-robotar och program behöver mer än namnet på avsikten. Detta ytterligare, valfria data kommer från entiteter som identifierats i uttryck. Varje typ av entitet returnerar annan information om matchningen.

Ett enstaka ord eller en fras i en uttryck kan matcha mer än en entitet. I så fall returneras varje matchande entitet med poängen.

Alla entiteter returneras i matrisen **entiteter** för svaret från slut punkten

## <a name="tokenized-entity-returned"></a>En token enhet returnerades

Granska [token-stödet](luis-language-support.md#tokenization) i Luis.


## <a name="prebuilt-entity-data"></a>Färdiga enhets data
[Förbyggda](luis-concept-entity-types.md) entiteter identifieras baserat på en reguljär uttrycks matchning med hjälp av [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Förbyggda entiteter returneras i entiteten entiteter och använder det typnamn som föregås av `builtin::` .

## <a name="list-entity-data"></a>Lista entitets data

[Lista entiteter](reference-entity-list.md) representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för List-entiteter. Använd **rekommendations** funktionen om du vill se förslag på nya ord baserade på den aktuella listan. Om det finns mer än en List-entitet med samma värde returneras varje entitet i slut punkts frågan.

## <a name="regular-expression-entity-data"></a>Enhets data för reguljära uttryck

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) extraherar en entitet baserat på ett reguljärt uttryck som du anger.

## <a name="extracting-names"></a>Extraherar namn
Det är svårt att hämta namn från en uttryck eftersom ett namn kan vara praktiskt taget valfri kombination av bokstäver och ord. Beroende på vilken typ av namn du extraherar har du flera alternativ. Följande förslag är inte regler, men fler rikt linjer.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Lägg till färdiga PersonName-och GeographyV2-entiteter

[PersonName](luis-reference-prebuilt-person.md) -och [GeographyV2](luis-reference-prebuilt-geographyV2.md) -entiteter är tillgängliga i vissa [språk kulturer](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Namn på personer

Personens namn kan ha lite format beroende på språk och kultur. Använd antingen en fördefinierad **[personName](luis-reference-prebuilt-person.md)** -entitet eller en **[enkel entitet](luis-concept-entity-types.md)** med roller för för-och efter namn.

Om du använder den enkla entiteten ska du se till att ge exempel som använder det första och sista namnet i olika delar av uttryck, i yttranden med olika längd och yttranden i alla avsikter, inklusive ingen avsikt. [Granska](./luis-how-to-review-endpoint-utterances.md) slut punkts yttranden regelbundet för att märka namn som inte har förutsägts korrekt.

### <a name="names-of-places"></a>Namn på platser

Plats namn anges och är kända som städer, regioner, stater, provinser och länder/regioner. Använd den fördefinierade entiteten **[geographyV2](luis-reference-prebuilt-geographyv2.md)** för att extrahera plats information.

### <a name="new-and-emerging-names"></a>Nya och framväxande namn

Vissa appar måste kunna hitta nya och nya namn, till exempel produkter eller företag. Dessa typer av namn är den svåraste typen av data extrahering. Börja med en **[enkel entitet](luis-concept-entity-types.md#simple-entity)** och Lägg till en [fras lista](luis-concept-feature.md). [Granska](./luis-how-to-review-endpoint-utterances.md) slut punkts yttranden regelbundet för att märka namn som inte har förutsägts korrekt.

## <a name="patternany-entity-data"></a>Mönster. alla entitets data

[Mönster.](reference-entity-pattern-any.md) det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar. Entiteten som används i mönstret måste hittas för att mönstret ska kunna användas.

## <a name="sentiment-analysis"></a>Attitydanalys
Om sentiment-analysen konfigureras vid [publiceringen](luis-how-to-publish-app.md#sentiment-analysis)innehåller Luis JSON-svaret sentiment analys. Läs mer om sentiment-analys i [textanalys](../text-analytics/index.yml) -dokumentationen.

## <a name="key-phrase-extraction-entity-data"></a>Nyckel fras extrahering av enhets data
[Extraherings enheten för nyckel fraser](luis-reference-prebuilt-keyphrase.md) returnerar nyckel fraser i uttryck, som tillhandahålls av [textanalys](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter

LUIS returnerar alla entiteter som identifierats i uttryck. Därför kan din Chat-robot behöva fatta beslut baserat på resultaten.

## <a name="data-matching-multiple-list-entities"></a>Data som matchar flera List enheter

Om ett ord eller en fras matchar mer än en List-entitet returnerar slut punkts frågan varje lista entitet.

För frågan `when is the best time to go to red rock?` och appen har ordet `red` i fler än en lista, känner Luis igen alla entiteter och returnerar en matris med entiteter som en del av JSON-slutpunktens svar.

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.
