---
title: Kunskap om OCR-kognitivhet
titleSuffix: Azure Cognitive Search
description: Extrahera text från bildfiler med optisk tecken läsning (OCR) i en pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950813"
---
# <a name="ocr-cognitive-skill"></a>Kunskap om OCR-kognitivhet

Den **optiska tecken läsningen (OCR)** känner igen utskrift och handskriven text i bildfiler. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [visuellt innehåll](../cognitive-services/computer-vision/overview.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) i Cognitive Services. **OCR** -kunskapen mappar till följande funktioner:

+ För engelska, spanska, tyska, franska, italienska, portugisiska och nederländska används det nya ["Read"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) -API: et.
+ För alla andra språk används [OCR](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) -API: et.

**OCR** -kompetensen extraherar text från bildfiler. Fil format som stöds är:

+ . -
+ . JPEG
+ . KÄLLFIL
+ . BITMAPPSBILD
+ . OBJEKT
+ . TIFF

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `detectOrientation`   | Aktiverar automatisk identifiering av bild orientering. <br/> Giltiga värden: TRUE/FALSE.|
| `defaultLanguageCode` | <p>   Språk koden för inmatad text. Språk som stöds: <br/> zh-hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (tjeckiska) <br/>da (danska) <br/>nl (nederländska) <br/>en (engelska) <br/>Fi (finska)  <br/>Frankrike (franska) <br/>  de (tyska) <br/>El (grekiska) <br/> hu (ungerska) <br/> IT (italienska) <br/>  Ja (japansk) <br/> Ko (koreanska) <br/> NB (norska) <br/>   pl (polska) <br/> PT (portugisiska) <br/>  ru (ryska) <br/>  ES (spanska) <br/>  sa (Svenska) <br/>  TR (turkiska) <br/> ar (arabiska) <br/> ro (rumänska) <br/> sr-cyrl (SerbianCyrillic) <br/> SR-latn (SerbianLatin) <br/>  SK (slovakiska) <br/>  Unk (okänd) <br/><br/> Om språk koden är ospecificerad eller null kommer språket att ställas in på engelska. Om språket uttryckligen anges till "Unk" identifieras språket automatiskt. </p> |
| `lineEnding` | Det värde som ska användas mellan varje identifierad rad. Möjliga värden: "Space", "CarriageReturn", "rad matning".  Standardvärdet är "blank steg". |

Tidigare fanns det en parameter med namnet "textExtractionAlgorithm" för att ange om kunskapen ska extrahera "tryckt" eller "handskriven" text.  Den här parametern är föråldrad och behövs inte längre eftersom den senaste Read API-algoritmen kan extrahera båda typer av text samtidigt.  Om din kunskaps definition redan innehåller den här parametern behöver du inte ta bort den, men den kommer inte längre att användas och båda typerna av text kommer att extraheras, oavsett vad den är inställd på.

## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| `image`         | Komplex typ. För närvarande fungerar det bara med fältet "/Document/normalized_images" som skapas av Azure Blob-indexeraren när ```imageAction``` har angetts till ett annat värde än ```none``` . Se [exemplet](#sample-output) för mer information.|


## <a name="skill-outputs"></a>Kunskaps utmatningar
| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| `text`            | Oformaterad text extraherad från avbildningen.   |
| `layoutText`    | Komplex typ som beskriver den extraherade texten och den plats där texten hittades.|


## <a name="sample-definition"></a>Exempel definition

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Exempel text och layoutText utdata

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exempel: sammanfoga text som extraherats från inbäddade bilder med dokumentets innehåll.

Ett vanligt användnings fall för text fusion är möjligheten att slå samman text representationen av bilder (text från en OCR-färdighet eller bild text) i innehålls fältet i ett dokument.

I följande exempel skapar färdigheter ett *merged_text* -fält. Det här fältet innehåller text innehållet i ditt dokument och OCRed-texten från var och en av de inbäddade bilderna i dokumentet.

#### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Exemplet ovan färdigheter förutsätter att det finns ett normaliserat-avbildnings fält. Om du vill generera det här fältet anger du *imageAction* -konfigurationen i din indexerare-definition till *generateNormalizedImages* som visas nedan:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Se även
+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [TextMerger-kompetens](cognitive-search-skill-textmerger.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](/rest/api/searchservice/create-indexer)