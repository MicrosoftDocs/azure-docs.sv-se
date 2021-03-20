---
title: Bild moderator – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd Content Moderator dator redigering och gransknings verktyget för mänskligt-in-loop till måttliga bilder för vuxna och vågat innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85801003"
---
# <a name="learn-image-moderation-concepts"></a>Lär dig om bild moderator koncept

Använd Content Moderator datorns verktyg för redigering av bilder och [gransknings verktyg](Review-Tool-User-Guide/human-in-the-loop.md) till måttliga bilder för vuxna och vågat innehåll. Skanna bilder för text innehåll och extrahera texten och identifiera ansikten. Du kan matcha bilder mot anpassade listor och vidta ytterligare åtgärder.

## <a name="evaluating-for-adult-and-racy-content"></a>Utvärdera för vuxna och vågat innehåll

**Utläsningen** returnerar en förtroende poäng mellan 0 och 1. Den returnerar även booleska data som är lika med sant eller falskt. Dessa värden förutsäger om avbildningen innehåller potentiellt vuxen eller vågat innehåll. När du anropar API: et med din avbildning (fil eller URL) innehåller det returnerade svaret följande information:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` representerar den potentiella förekomsten av bilder som kan uppfattas som sexuellt explicita eller ämnade för vuxna.
> - `isImageRacyClassified` representerar den potentiella förekomsten av bilder som kan uppfattas som sexuellt laddade eller ämnade för vuxna.
> - Poängen är mellan 0 och 1. Ju högre poäng, desto högre blir modellen för att förutsäga att kategorin kan vara tillämplig. Den här förhands granskningen använder en statistisk modell i stället för att manuellt koda resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig efter dina behov.
> - De booleska värdena är antingen sant eller falskt beroende på de interna Poäng tröskelvärdena. Kunderna bör bedöma om de ska använda det här värdet eller bestämma anpassade tröskelvärden baserat på deras innehålls principer.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Identifiera text med optisk tecken igenkänning (OCR)

**OCR-åtgärden (optisk tecken läsning)** förutsäger förekomsten av text innehåll i en bild och extraherar den för text redigering, bland annat. Du kan ange språket. Om du inte anger något språk används engelska som standard.

Svaret innehåller följande information:
- Den ursprungliga texten.
- De identifierade text elementen med sina konfidens resultat.

Exempel på extrahering:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Identifiera ansikten

Att identifiera ansikten hjälper till att identifiera personliga data, till exempel ansikten i bilderna. Du identifierar potentiella ansikten och antalet potentiella ansikten i varje bild.

Ett svar innehåller följande information:

- Antal ansikten
- Lista över platser för ansikten som identifieras

Exempel på extrahering:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Skapa och hantera anpassade listor

I många online-communities kan stötande objekt delas flera gånger under följande dagar, veckor och månader efter att användare överför bilder eller annan typ av innehåll. Kostnaderna för att söka igenom och filtrera ut samma bild eller till och med något ändrat versioner av avbildningen från flera platser kan vara dyra och fel känsliga.

I stället för att använda samma avbildning flera gånger, lägger du till de stötande bilderna i din anpassade lista över blockerat innehåll. På så sätt jämför ditt innehålls redigerings system inkommande avbildningar mot dina anpassade listor och stoppar all ytterligare bearbetning.

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

Content Moderator innehåller en fullständig [hanterings-API för bild listor](try-image-list-api.md) med åtgärder för att hantera listor med anpassade avbildningar. Börja med [avbildningen listar API-konsolen](try-image-list-api.md) och Använd REST API kod exempel. Ta också en titt på [avbildnings listan .net snabb start](image-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="matching-against-your-custom-lists"></a>Matchning mot dina anpassade listor

Matchnings åtgärden tillåter fuzzy matchning av inkommande bilder mot någon av dina anpassade listor, som skapas och hanteras med hjälp av list åtgärderna.

Om en matchning hittas returnerar åtgärden identifieraren och kontrollanten för den matchade bilden. Svaret innehåller följande information:

- Matcha Poäng (mellan 0 och 1)
- Matchad bild
- Bildtaggar (tilldelas under föregående moderator)
- Bild etiketter

Exempel på extrahering:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Granskningsverktyg

Om du vill ha fler nyanserade-fall använder du [gransknings verktyget](Review-Tool-User-Guide/human-in-the-loop.md) Content moderator och dess API för att Visa kontroll resultaten och innehållet i granskningen för dina mänskliga moderatorer. De granskar de tilldelade taggarna och bekräftar sina slutgiltiga beslut.

![Bildgranskning för mänskliga moderatorer](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Nästa steg

Testa API- [konsolen för bild moderator](try-image-api.md) och Använd REST API kod exempel. Se även [granskningar, arbets flöden och jobb](./review-api.md) för att lära dig hur du ställer in mänsklig granskning.
