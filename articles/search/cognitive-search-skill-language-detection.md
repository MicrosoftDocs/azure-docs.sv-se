---
title: Kognitiv kompetens för språk identifiering
titleSuffix: Azure Cognitive Search
description: Utvärderar ostrukturerad text, och för varje post returneras en språk identifierare med en poäng som anger styrkan i analysen i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548061"
---
#   <a name="language-detection-cognitive-skill"></a>Kognitiv kompetens för språk identifiering

**Språkidentifiering** -kunskapen identifierar språket för inmatad text och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språk koden är kopplad till en poäng som anger styrkan i analysen. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](../cognitive-services/text-analytics/overview.md) i Cognitive Services.

Den här funktionen är särskilt användbar när du behöver ange språket för texten som inskickad till andra kunskaper (till exempel [Attitydanalys kompetens](cognitive-search-skill-sentiment.md) eller [text delnings kunskaper](cognitive-search-skill-textsplit.md)).

Språk identifieringen utnyttjar Bing: s naturliga språk bearbetnings bibliotek, vilket överskrider antalet [språk och regioner som stöds](../cognitive-services/text-analytics/language-support.md) för textanalys. Den exakta listan över språk publiceras inte, men innehåller alla talade språk, plus varianter, dialekter och vissa regionala och kulturella språk. Om du har innehåll som uttryckts på ett mindre vanligt språk kan du [prova språkidentifiering-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `(Unknown)` .

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. LanguageDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till språk identifierings kunskapen kan du använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Indata | Beskrivning |
|---------------------|-------------|
| `defaultCountryHint` | Valfritt En ISO 3166-1 alpha-2 2 bokstavs lands kod kan anges som en ledtråd till språk identifierings modellen om den inte kan disambiguate språket. Se [textanalys-dokumentationen](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) för det här avsnittet för mer information. Mer specifikt `defaultCountryHint` används parametern med dokument som inte anger `countryHint` indatatypen explicit.  |
| `modelVersion`   | Valfritt Den version av modellen som ska användas när Textanalyss tjänsten anropas. Den senast tillgängliga anges som standard när du inte anger den. Vi rekommenderar att du inte anger det här värdet om det inte är absolut nödvändigt. Mer information finns [i modell versioner i API för textanalys](../cognitive-services/text-analytics/concepts/model-versioning.md) . |

## <a name="skill-inputs"></a>Kompetens inmatningar

Parametrar är skiftlägeskänsliga.

| Indata     | Beskrivning |
|--------------------|-------------|
| `text` | Den text som ska analyseras.|
| `countryHint` | En ISO 3166-1 alpha-2 2 bokstavs lands kod som används som ett tips för språk identifierings modellen om den inte kan disambiguate språket. Se [textanalys-dokumentationen](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) för det här avsnittet för mer information. |

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata    | Beskrivning |
|--------------------|-------------|
| `languageCode` | ISO 6391-språkkoden för det språk som identifierades. Till exempel "en". |
| `languageName` | Språkets namn. Till exempel "engelska". |
| `score` | Ett värde mellan 0 och 1. Sannolikheten att språket identifieras korrekt. Poängen kan vara lägre än 1 om meningen har blandade språk.  |

##  <a name="sample-definition"></a>Exempel definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Exempelutdata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)