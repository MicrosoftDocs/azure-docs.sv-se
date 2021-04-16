---
title: Extrahering av data – LUIS
description: Extrahera data från yttranden med avsikter och entiteter. Lär dig vilken typ av data som kan extraheras från Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500149"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahera data från yttranden med avsikter och entiteter
LUIS ger dig möjlighet att hämta information från en användares yttranden på naturligt språk. Informationen extraheras på ett sätt som gör att den kan användas av ett program, ett program eller en chattrobot för att vidta åtgärder. I följande avsnitt lär du dig vilka data som returneras från avsikter och entiteter med exempel på JSON.

De svåraste data att extrahera är maskininlärningsdata eftersom de inte är en exakt textmatchning. Extrahering av maskininlärningsentiteter [](luis-concept-app-iteration.md) måste ingå i redigeringscykeln tills du är säker på att du får de data du förväntar dig. [](luis-concept-entity-types.md)

## <a name="data-location-and-key-usage"></a>Dataplats och nyckelanvändning
LUIS extraherar data från användarens uttryck på den publicerade [slutpunkten](luis-glossary.md#endpoint). **HTTPS-begäran** (POST eller GET) innehåller yttrandena samt vissa valfria konfigurationer, till exempel mellanlagrings- eller produktionsmiljöer.

**Begäran om slutpunkt för förutsägelse av V2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Begäran om förutsägelseslutpunkt för V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

finns på sidan Inställningar för din LUIS-app samt som en del av `appID` URL:en (efter ) när du redigerar  `/apps/` LUIS-appen. `subscription-key`är slutpunktsnyckeln som används för att köra frågor mot din app. Även om du kan använda din kostnadsfria redigerings-/startnyckel när du lär dig LUIS, är det viktigt att ändra slutpunktsnyckeln till en nyckel som stöder din [förväntade LUIS-användning.](luis-limits.md#key-limits) Enheten `timezoneOffset` är minuter.

**HTTPS-svaret innehåller** all avsikts- och entitetsinformation som LUIS kan fastställa baserat på den aktuella publicerade modellen för mellanlagrings- eller produktionsslutpunkten. Slutpunktens URL finns på [LUIS-webbplatsen](luis-reference-regions.md) i **avsnittet Hantera** på sidan **Nycklar och slutpunkter.**

## <a name="data-from-intents"></a>Data från avsikter
Primära data är avsiktsnamnet med **högst poäng.** Slutpunktssvaret är:

#### <a name="v2-prediction-endpoint-response"></a>[Svar från V2-förutsägelseslutpunkt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Svar från V3-förutsägelseslutpunkt](#tab/V3)

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

Läs mer om [V3-slutpunkten för förutsägelse](luis-migration-api-v3.md).

* * *

|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|
|Avsikt|Sträng|topScoringIntent.intent|"GetStoreInfo"|

Om din chattrobot eller LUIS-anropande app fattar ett beslut baserat på fler än en avsiktspoäng returnerar du alla avsikters poäng.


#### <a name="v2-prediction-endpoint-response"></a>[Svar från V2-förutsägelseslutpunkt](#tab/V2)

Ange frågesträngsparametern `verbose=true` . Slutpunktssvaret är:

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

#### <a name="v3-prediction-endpoint-response"></a>[Svar från V3-förutsägelseslutpunkt](#tab/V3)

Ange frågesträngsparametern `show-all-intents=true` . Slutpunktssvaret är:

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

Läs mer om [V3-slutpunkten för förutsägelse](luis-migration-api-v3.md).

* * *

Avsikterna sorteras från högsta till lägsta poäng.

|Dataobjekt|Datatyp|Dataplats|Värde|Poäng|
|--|--|--|--|:--|
|Avsikt|Sträng|intents[0].intent|"GetStoreInfo"|0.984749258|
|Avsikt|Sträng|intents[1].intent|"Ingen"|0.0168218873|

Om du lägger till fördefinierade domäner anger avsiktsnamnet domänen, till `Utilties` `Communication` exempel eller samt avsikten:

#### <a name="v2-prediction-endpoint-response"></a>[Svar från V2-förutsägelseslutpunkt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Svar från V3-förutsägelseslutpunkt](#tab/V3)

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

Läs mer om [V3-slutpunkten för förutsägelse.](luis-migration-api-v3.md)

* * *

|Domain|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|--|
|Verktyg|Avsikt|Sträng|intents[0].intent|"<b>Verktyg</b>. ShowNext"|
|Kommunikation|Avsikt|Sträng|intents[1].intent|<b>Kommunikation</b>. StartOver"|
||Avsikt|Sträng|intents[2].intent|"Ingen"|


## <a name="data-from-entities"></a>Data från entiteter
De flesta chattrobotar och program behöver mer än avsiktsnamnet. Dessa ytterligare valfria data kommer från entiteter som identifierats i yttrandena. Varje typ av entitet returnerar olika information om matchningen.

Ett enstaka ord eller en fras i ett uttryck kan matcha mer än en entitet. I så fall returneras varje matchande entitet med dess poäng.

Alla entiteter  returneras i entitetsmatrisen för svaret från slutpunkten

## <a name="tokenized-entity-returned"></a>Tokeniserad entitet returneras

Granska [tokenstöd i](luis-language-support.md#tokenization) LUIS.


## <a name="prebuilt-entity-data"></a>Fördefinierade entitetsdata
[Fördefinierade entiteter](luis-concept-entity-types.md) identifieras baserat på en matchning av reguljära uttryck med hjälp av projektet [Recognizers-Text med öppen](https://github.com/Microsoft/Recognizers-Text) källkod. Fördefinierade entiteter returneras i entitetsmatrisen och använder typnamnet med prefixet `builtin::` .

## <a name="list-entity-data"></a>Lista entitetsdata

[Listentiteter](reference-entity-list.md) representerar en fast, stängd uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listentiteter. Använd funktionen **Rekommendera** för att se förslag på nya ord baserat på den aktuella listan. Om det finns fler än en listentitet med samma värde returneras varje entitet i slutpunktsfrågan.

## <a name="regular-expression-entity-data"></a>Entitetsdata för reguljära uttryck

En [entitet för reguljära](reference-entity-regular-expression.md) uttryck extraherar en entitet baserat på ett reguljärt uttryck som du anger.

## <a name="extracting-names"></a>Extrahera namn
Det är svårt att hämta namn från ett uttryck eftersom ett namn kan vara nästan vilken kombination av bokstäver och ord som helst. Beroende på vilken typ av namn du extraherar har du flera alternativ. Följande förslag är inte regler utan fler riktlinjer.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Lägga till fördefinierade PersonName- och GeographyV2-entiteter

[Entiteterna PersonName](luis-reference-prebuilt-person.md) [och GeographyV2](luis-reference-prebuilt-geographyV2.md) är tillgängliga i vissa [språkkulturer.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Namn på personer

Personers namn kan ha lite format beroende på språk och kultur. Använd antingen en förbyggd **[personName-entitet](luis-reference-prebuilt-person.md)** eller **[en enkel entitet](luis-concept-entity-types.md)** med roller med för- och efternamn.

Om du använder den enkla entiteten ska du ge exempel som använder för- och efternamn i olika delar av yttrandena, i yttranden med olika längd och yttranden för alla avsikter, inklusive avsikten None (Ingen). [Granska](./luis-how-to-review-endpoint-utterances.md) slutpunktsyttranden regelbundet för att märka alla namn som inte förutsades korrekt.

### <a name="names-of-places"></a>Namn på platser

Platsnamn anges och kallas till exempel städer, regioner, delstater, provinser och länder/regioner. Använd den fördefinierade **[entitetsgeographyV2 för att](luis-reference-prebuilt-geographyv2.md)** extrahera platsinformation.

### <a name="new-and-emerging-names"></a>Nya och nya namn

Vissa appar måste kunna hitta nya och nya namn, till exempel produkter eller företag. Dessa typer av namn är den svåraste typen av extrahering av data. Börja med en enkel **[entitet](luis-concept-entity-types.md)** och lägg till [en fraslista](luis-concept-feature.md). [Granska](./luis-how-to-review-endpoint-utterances.md) slutpunktsyttranden regelbundet för att märka alla namn som inte förutsades korrekt.

## <a name="patternany-entity-data"></a>Pattern.any-entitetsdata

[Pattern.any](reference-entity-pattern-any.md) är en platshållare med variabel längd som endast används i mönstrens mutterance för att markera var entiteten börjar och slutar. Entiteten som används i mönstret måste hittas för att mönstret ska tillämpas.

## <a name="sentiment-analysis"></a>Attitydanalys
Om attitydanalys har konfigurerats när du [publicerar](luis-how-to-publish-app.md#sentiment-analysis)innehåller LUIS json-svaret attitydanalys. Läs mer om attitydanalys i Textanalys dokumentationen. [](../text-analytics/index.yml)

## <a name="key-phrase-extraction-entity-data"></a>Entitetsdata för extrahering av nyckelfraser
[Entiteten extrahering](luis-reference-prebuilt-keyphrase.md) av nyckelfraser returnerar nyckelfraser i yttrandena, som tillhandahålls [av Textanalys](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter

LUIS returnerar alla entiteter som identifierats i yttrandena. Därför kan chattroboten behöva fatta ett beslut baserat på resultatet.

## <a name="data-matching-multiple-list-entities"></a>Data som matchar flera listentiteter

Om ett ord eller en fras matchar mer än en listentitet returnerar slutpunktsfrågan varje List-entitet.

För frågan , och appen har ordet i fler än en lista, identifierar LUIS alla entiteter och returnerar en matris med entiteter som en del av `when is the best time to go to red rock?` `red` JSON-slutpunktssvaret.

## <a name="next-steps"></a>Nästa steg

Mer [information om hur](luis-how-to-add-entities.md) du lägger till entiteter i LUIS-appen finns i Lägga till entiteter.
