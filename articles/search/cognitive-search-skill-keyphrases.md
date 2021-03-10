---
title: Extrahering av diskussionsämne kognitiva kunskaper
titleSuffix: Azure Cognitive Search
description: Utvärderar ostrukturerad text och returnerar en lista med nyckel fraser i en pipeline för AI-anrikning i Azure Kognitiv sökning för varje post.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547840"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Extrahering av diskussionsämne kognitiva kunskaper

Den **extrahering av diskussionsämnea** kunskapen utvärderar ostrukturerad text och returnerar en lista över viktiga fraser för varje post. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](../cognitive-services/text-analytics/overview.md) i Cognitive Services.

Den här funktionen är användbar om du snabbt behöver identifiera de viktigaste pratar punkterna i posten. Till exempel, den inmatade texten "livsmedlet var Delicious och det var en fantastiskt anställd", returnerar tjänsten "mat" och "trevlig personal".

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Indata | Beskrivning |
|---------------------|-------------|
| `defaultLanguageCode` | Valfritt Språk koden som ska användas för dokument som inte uttryckligen anger språk.  Om standard språk koden inte anges används engelska (en) som standard språk kod. <br/> Se en [fullständig lista över språk som stöds](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | Valfritt Det maximala antalet nyckel fraser som ska skapas. |
| `modelVersion`   | Valfritt Den version av modellen som ska användas när Textanalyss tjänsten anropas. Den senast tillgängliga anges som standard när du inte anger den. Vi rekommenderar att du inte anger det här värdet om det inte är absolut nödvändigt. Mer information finns [i modell versioner i API för textanalys](../cognitive-services/text-analytics/concepts/model-versioning.md) . |

## <a name="skill-inputs"></a>Kompetens inmatningar

| Indata  | Beskrivning |
|--------------------|-------------|
| `text` | Den text som ska analyseras.|
| `languageCode`    |  En sträng som anger språket för posterna. Om den här parametern inte anges används standard språk koden för att analysera posterna. <br/>Se en [fullständig lista över språk som stöds](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Utdata     | Beskrivning |
|--------------------|-------------|
| `keyPhrases` | En lista med nyckel fraser extraherade från inmatad text. Viktiga fraser returneras i prioritetsordning. |


##  <a name="sample-definition"></a>Exempel definition

Överväg en SQL-post med följande fält:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Sedan kan din kunskaps definition se ut så här:

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Exempelutdata

I exemplet ovan skrivs resultatet av din färdighet till en ny nod i det fördjupade trädet "Document/myKeyPhrases" eftersom det är det `targetName` som vi har angett. Om du inte anger någon `targetName` , blir det "dokument/diskussions fraser".

#### <a name="documentmykeyphrases"></a>Document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Du kan använda "Document/myKeyPhrases" som indata till andra kunskaper eller som en källa till en [mappning](cognitive-search-output-field-mapping.md)av ett fält för utdata.

## <a name="warnings"></a>Varningar
Om du anger en språk kod som inte stöds genereras en varning och viktiga fraser extraheras inte.
Om texten är tom skapas en varning.
Om texten är större än 50 000 tecken kommer endast de första 50 000 tecknen att analyseras och en varning utfärdas.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Definiera mappningar för utdata-fält](cognitive-search-output-field-mapping.md)