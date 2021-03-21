---
title: Extrahera text från bilder
titleSuffix: Azure Cognitive Search
description: Behandla och extrahera text och annan information från bilder i Azure Kognitiv sökning pipelines.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e77bbd6e82d0d4a48b72e13e60b60608f2d7674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419599"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Så här bearbetar och extraherar du information från bilder i AI-anriknings scenarier

Azure Kognitiv sökning har flera funktioner för att arbeta med bilder och bildfiler. Under dokument sprickor kan du använda parametern *imageAction* för att extrahera text från foton eller bilder som innehåller alfanumerisk text, till exempel ordet "Stop" i ett stopp tecken. Andra scenarier är att generera en text representation av en bild, till exempel "Dandelion" för ett foto av en Dandelion eller färgen "gul". Du kan också extrahera metadata om avbildningen, till exempel dess storlek.

Den här artikeln beskriver bild bearbetning i mer detalj och ger vägledning för att arbeta med bilder i en pipeline för AI-anrikning.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Hämta normaliserade avbildningar

Som en del av dokument sprickor finns det en ny uppsättning konfigurations parametrar för indexerare för att hantera bildfiler eller avbildningar som är inbäddade i filer. De här parametrarna används för att normalisera bilder för vidare bearbetning i efterföljande bearbetning. Normalisera bilder gör dem mer enhetliga. Stora bilder ändrar storlek till maximal höjd och bredd så att de kan använda dem. För bilder som tillhandahåller metadata i orienteringen justeras bild rotation för vertikal inläsning. Metadata-justeringar samlas in i en komplex typ som skapas för varje bild. 

Du kan inte inaktivera avbildnings-normalisering. De färdigheter som itereras över bilder förväntar sig normaliserade avbildningar. Om du aktiverar avbildnings-normalisering på en indexerare krävs det att en färdigheter är kopplad till den indexeraren.

| Konfigurations parameter | Beskrivning |
|--------------------|-------------|
| imageAction   | Ange till "ingen" om ingen åtgärd ska vidtas när inbäddade bilder eller bildfiler påträffas. <br/>Ange till "generateNormalizedImages" om du vill generera en matris med normaliserade avbildningar som en del av dokument sprickor.<br/>Ange till "generateNormalizedImagePerPage" om du vill generera en matris med normaliserade avbildningar där varje sida visas till en utgående bild för PDF-filer i data källan.  Funktionen är samma som "generateNormalizedImages" för filtyper som inte är PDF-filer.<br/>För alla alternativ som inte är "ingen" visas bilderna i fältet *normalized_images* . <br/>Standardvärdet är "ingen". Den här konfigurationen är bara relevant för BLOB-datakällor, när "dataToExtract" är inställt på "contentAndMetadata". <br/>Högst 1000 avbildningar kommer att extraheras från ett givet dokument. Om det finns fler än 1000 avbildningar i ett dokument kommer den första 1000 att extraheras och en varning genereras. |
|  normalizedImageMaxWidth | Den maximala bredden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2 000. Det högsta tillåtna värdet är 10000. | 
|  normalizedImageMaxHeight | Den maximala höjden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2 000. Det högsta tillåtna värdet är 10000.|

> [!NOTE]
> Om du anger egenskapen *imageAction* till något annat än "ingen" kan du inte ange egenskapen *parsingMode* till något annat än "default".  Du kan bara ange en av dessa två egenskaper till ett värde som inte är standardvärdet i din indexerare-konfiguration.

Ange parametern **parsingMode** till `json` (för att indexera varje blob som ett enskilt dokument) eller `jsonArray` (om Blobbarna innehåller JSON-matriser och du behöver varje element i en matris för att behandlas som ett separat dokument).

Standardvärdet på 2000 bild punkter för de normaliserade bildernas maximala bredd och höjd baseras på de maximala storlekar som stöds av [OCR-kompetensen](cognitive-search-skill-ocr.md) och [bild analysens färdighet](cognitive-search-skill-image-analysis.md). [OCR-kunskaper](cognitive-search-skill-ocr.md) stöder maximal bredd och höjd på 4200 för andra språk än engelska och 10000 för engelska.  Om du ökar Max gränsen kan bearbetningen av större avbildningar gå sönder beroende på din färdigheter-definition och dokumentets språk. 

Du anger imageAction i [Indexer-definitionen](/rest/api/searchservice/create-indexer) enligt följande:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

När *imageAction* är inställt på ett annat värde än "ingen", kommer fältet ny *normalized_images* innehålla en matris med bilder. Varje bild är en komplex typ som har följande medlemmar:

| Bild medlem       | Beskrivning                             |
|--------------------|-----------------------------------------|
| data               | BASE64-kodad sträng för den normaliserade bilden i JPEG-format.   |
| bredd              | Den normaliserade bildens bredd i bild punkter. |
| mankhöjd             | Den normaliserade bildens höjd i bild punkter. |
| originalWidth      | Bildens ursprungliga bredd före normalisering. |
| originalHeight      | Bildens ursprungliga höjd innan normalisering. |
| rotationFromOriginal |  Räknaren medsols rotation i grader som uppstod när den normaliserade bilden skulle skapas. Ett värde mellan 0 och 360 grader. Det här steget läser metadata från den avbildning som genereras av en kamera eller skanner. Vanligt vis en multipel av 90 grader. |
| contentOffset | Den teckenuppsättning i innehålls fältet där avbildningen extraherades. Det här fältet gäller endast för filer med inbäddade bilder. |
| pageNumber | Om avbildningen extraherades eller renderades från en PDF innehåller det här fältet sid numret i PDF-filen som extraherades eller renderades från från 1.  Om avbildningen inte är från en PDF-fil kommer det här fältet att vara 0.  |

 Exempel värde för *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Bild-relaterade kunskaper

Det finns två inbyggda kognitiva färdigheter som tar bilder som indata: [OCR](cognitive-search-skill-ocr.md) -och [bild analys](cognitive-search-skill-image-analysis.md). 

Dessa kunskaper fungerar för närvarande bara med bilder som genereras från dokument sprickors steget. Därför är det enda som stöds `"/document/normalized_images"` .

### <a name="image-analysis-skill"></a>Bild analys kunskaper

I [bild analysens kunskap](cognitive-search-skill-image-analysis.md) extraheras en omfattande uppsättning visuella funktioner baserat på avbildningens innehåll. Du kan till exempel generera en under text från en bild, generera taggar eller identifiera kändisar och landmärken.

### <a name="ocr-skill"></a>OCR-kunskaper

[OCR-kompetensen](cognitive-search-skill-ocr.md) extraherar text från bildfiler som JPGs, PNGs och bitmappar. Den kan extrahera text samt layoutinformation. I layoutinformation visas avgränsnings rutor för var och en av de angivna strängarna.

## <a name="embedded-image-scenario"></a>Scenario för inbäddad bild

Ett vanligt scenario är att skapa en enskild sträng som innehåller allt fil innehåll, både text-och bilds Origin text, genom att utföra följande steg:  

1. [Extrahera normalized_images](#get-normalized-images)
1. Kör OCR-kompetensen med `"/document/normalized_images"` som indatamängd
1. Sammanfoga text representationen av dessa bilder med den obehandlade text som extraherats från filen. Du kan använda [text sammanfognings](cognitive-search-skill-textmerger.md) kunskapen för att konsolidera båda text segmenten i en enda stor sträng.

I följande exempel färdigheter skapas ett *merged_text* -fält som innehåller text innehållet i ditt dokument. Den innehåller också OCRed-texten från var och en av de inbäddade bilderna. 

#### <a name="request-body-syntax"></a>Syntax för begär ande text
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
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

Nu när du har ett merged_text fält kan du mappa det som ett sökbart fält i din index definition. Alla filernas innehåll, inklusive texten i bilderna, är sökbara.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisera avgränsnings rutor för extraherad text

Ett annat vanligt scenario är visualisering av information om Sök resultatens layout. Du kanske till exempel vill markera var en del av texten påträffades i en bild som en del av dina Sök resultat.

Eftersom OCR-steget utförs på de normaliserade bilderna, är det normaliserade bild utrymmet. När du visar den normaliserade bilden är förekomsten av koordinater vanligt vis inte ett problem, men i vissa fall kanske du vill visa den ursprungliga bilden. I det här fallet konverterar du varje koordinat punkter i layouten till det ursprungliga bild koordinat systemet. 

Om du behöver transformera normaliserade koordinater till det ursprungliga koordinat utrymmet som hjälp kan du använda följande algoritm:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```
## <a name="passing-images-to-custom-skills"></a>Skicka bilder till anpassade kunskaper

För scenarier där du behöver en anpassad färdighet för att arbeta med bilder kan du skicka bilder till den anpassade kunskapen och låta dem returnera text eller bilder. Bild bearbetningen [python](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) demonstrerar arbets flödet. Följande färdigheter är från exemplet.

Följande färdigheter tar den normaliserade avbildningen (hämtas under dokument sprickor) och matar ut segment av bilden.

#### <a name="sample-skillset"></a>Exempel på färdigheter
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

#### <a name="custom-skill"></a>Anpassad kompetens

Den anpassade själva kompetensen är extern i färdigheter. I det här fallet är det python-kod som först loopar i gruppen med begär ande poster i det anpassade färdighets formatet och konverterar den base64-kodade strängen till en bild.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```
Om du vill returnera en bild returnerar du en Base64-kodad sträng i ett JSON-objekt med `$type` egenskapen `file` .

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Se även
+ [Skapa indexerare (REST)](/rest/api/searchservice/create-indexer)
+ [Bild analys kunskaper](cognitive-search-skill-image-analysis.md)
+ [OCR-kunskaper](cognitive-search-skill-ocr.md)
+ [Text sammanfognings kunskaper](cognitive-search-skill-textmerger.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här mappar du omfattande fält](cognitive-search-output-field-mapping.md)
+ [Så här skickar du avbildningar till anpassade kunskaper](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
