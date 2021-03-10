---
title: Inlärnings kunskap för dokument extrahering
titleSuffix: Azure Cognitive Search
description: Extraherar innehåll från en fil inom en pipeline för anrikning. Den här kunskapen är för närvarande en offentlig för hands version.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 144e8058e640f98dc6b0ef60534405525532b00e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547874"
---
# <a name="document-extraction-cognitive-skill"></a>Inlärnings kunskap för dokument extrahering

**Dokument extraherings** kunskapen extraherar innehåll från en fil i pipelinen för anrikning. På så sätt kan du utnyttja det steg för dokument extrahering som normalt inträffar innan färdigheter-körningen med filer som kan genereras av andra färdigheter.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet dokument sprickor i indexering. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Indata | Tillåtna värden | Beskrivning |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ange som `default` dokument extrahering från filer som inte är rena text eller JSON. Ange till `text` för att förbättra prestandan på filer med oformaterad text. Ange till `json` om du vill extrahera strukturerat innehåll från JSON-filer. Om `parsingMode` inte uttryckligen definieras anges det som `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ange till `contentAndMetadata` om du vill extrahera alla metadata och text innehåll från varje fil. Ange till `allMetadata` om du bara vill extrahera [metadata-egenskaperna för innehålls typen](search-blob-metadata-properties.md) (till exempel metadata som är unika för just. png-filer). Om `dataToExtract` inte uttryckligen definieras anges det som `contentAndMetadata` . |
| `configuration` | Se nedan. | En ord lista med valfria parametrar som justerar hur dokument extraheringen utförs. I tabellen nedan finns beskrivningar av de konfigurations egenskaper som stöds. |

| Konfigurations parameter   | Tillåtna värden | Beskrivning |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ange till `none` om du vill ignorera inbäddade bilder eller bildfiler i data uppsättningen. Det här är standardinställningen. <br/>För [bild analys med kognitiva kunskaper](cognitive-search-concept-image-scenarios.md)kan du ange att `generateNormalizedImages` kunskapen ska skapa en matris med normaliserade avbildningar som en del av dokument-sprickor. Den här åtgärden kräver att `parsingMode` har värdet `default` och `dataToExtract` är inställt på `contentAndMetadata` . En normaliserad bild syftar på ytterligare bearbetning som resulterar i enhetlig bild utmatning, storleks ändring och rotation för att främja konsekvent åter givning när du inkluderar bilder i visuella Sök resultat (till exempel samma storlek som fotografier i en diagram kontroll som visas i [JFK-demonstrationen](https://github.com/Microsoft/AzureSearch_JFK_Files)). Den här informationen genereras för varje avbildning när du använder det här alternativet.  <br/>Om du väljer till kommer `generateNormalizedImagePerPage` PDF-filer att behandlas på ett annat sätt i stället för att extrahera inbäddade bilder. varje sida återges som en bild och normaliseras därefter.  Filtyper som inte är PDF-filer kommer att behandlas på samma sätt som om `generateNormalizedImages` har angetts.
| `normalizedImageMaxWidth` | Ett heltal mellan 50-10000 | Den maximala bredden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2 000. | 
| `normalizedImageMaxHeight` | Ett heltal mellan 50-10000 | Den maximala höjden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2 000. |

> [!NOTE]
> Standardvärdet på 2000 bild punkter för de normaliserade bildernas maximala bredd och höjd baseras på de maximala storlekar som stöds av [OCR-kompetensen](cognitive-search-skill-ocr.md) och [bild analysens färdighet](cognitive-search-skill-image-analysis.md). [OCR-kunskaper](cognitive-search-skill-ocr.md) stöder maximal bredd och höjd på 4200 för andra språk än engelska och 10000 för engelska.  Om du ökar Max gränsen kan bearbetningen av större avbildningar gå sönder beroende på din färdigheter-definition och dokumentets språk. 
## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn     | Beskrivning |
|--------------------|-------------|
| `file_data` | Filen som innehållet ska extraheras från. |

Inmatade file_data måste vara ett objekt som definieras enligt följande:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Detta fil referens objekt kan genereras av tre sätt:

 - Ställer in `allowSkillsetToReadFileData` parametern på index-definitionen på "true".  Då skapas en sökväg `/document/file_data` som representerar de ursprungliga fil data som hämtas från BLOB-datakällan. Den här parametern gäller endast för data i Blob Storage.

 - Ange `imageAction` ett annat värde än för parametern i Indexer-definitionen `none` .  Detta skapar en matris med bilder som följer den konvention som krävs för inmatade kunskaper om den har skickats separat (dvs. `/document/normalized_images/*` ).

 - En anpassad kunskap returnerar ett JSON-objekt som definieras exakt som ovan.  `$type`Parametern måste anges till exakt `file` och `data` parametern måste vara bas 64-kodade byte mat ris data för fil innehållet.

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata    | Beskrivning |
|--------------|-------------|
| `content` | Dokumentets text innehåll. |
| `normalized_images`   | Om `imageAction` är inställt på ett värde annat `none` kommer fältet ny *normalized_images* innehålla en matris med bilder. I [dokumentationen för avbildnings extrahering](cognitive-search-concept-image-scenarios.md) finns mer information om utdataformatet för varje bild. |

##  <a name="sample-definition"></a>Exempel definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
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
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Exempelutdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här bearbetar och extraherar du information från bilder i kognitiva Sök scenarier](cognitive-search-concept-image-scenarios.md)