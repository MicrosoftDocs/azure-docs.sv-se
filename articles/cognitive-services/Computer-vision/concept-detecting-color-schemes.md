---
title: Identifiering av färgschema – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av färgschemat i bilder med hjälp av Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b113c424851065e244c3943bf4a4816bae9bba8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778921"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färgscheman i bilder

Visuellt innehåll analyserar färgerna i en bild för att ge tre olika attribut: den dominerande förgrundsfärgen, den dominerande bakgrundsfärgen och uppsättningen dominanta färger för bilden som helhet. Returnerade färger tillhör uppsättningen: svart, blå, brown, grå, grön, orange, rosa, lila, röd, teal, vit och gul. 

Visuellt innehåll extraherar också en accentfärg, som representerar den mest färgande färgen i bilden, baserat på en kombination av dominerande färger och mättnad. Accentfärgen returneras som en hexadecimal HTML-färgkod. 

Visuellt innehåll returnerar också ett booleskt värde som anger om en bild är i svart och vitt.

## <a name="color-scheme-detection-examples"></a>Exempel på identifiering av färgschema

I följande exempel visas JSON-svaret som returneras Visuellt innehåll när du identifierar färgschemat för exempelbilden. I det här fallet är exempelbilden inte en svart och vit bild, men de dominerande förgrunds- och bakgrundsfärgerna är svarta och de dominerande färgerna för bilden som helhet är svarta och vita.

![Utomhus mountain at sunset, with a persons's](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Dominanta färgexempel

I följande tabell visas de returnerade förgrunds-, bakgrunds- och bildfärgerna för varje exempelbild.

| Bild | Dominanta färger |
|-------|-----------------|
|![En vit blomma med en grön bakgrund](./Images/flower.png)| Förgrund: Svart<br/>Bakgrund: Vit<br/>Färger: svart, vit, grön|
![Ett träna som körs genom en station](./Images/train_station.png) | Förgrund: Svart<br/>Bakgrund: Svart<br/>Färger: Svart |

### <a name="accent-color-examples"></a>Accentfärgexempel

 I följande tabell visas den returnerade accentfärgen som ett hexadecimalt HTML-färgvärde för varje exempelbild.

| Bild | Accentfärg |
|-------|--------------|
|![En person som står på en bergsten vid sunset](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blomma med grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett träna som körs genom en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exempel på & vit identifiering

I följande tabell visas Visuellt innehåll utvärdering av svart och vit i exempelbilderna.

| Bild | Svart & vit? |
|-------|----------------|
|![En svart och vit bild av byggnader i Gör](./Images/bw_buildings.png) | true |
|![Ett blått hus och fronten](./Images/house_yard.png) | falskt |

## <a name="use-the-api"></a>Använda API:et

Funktionen för identifiering av färgscheman är en del av [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa det här API:et via ett inbyggt SDK eller via REST-anrop. Inkludera `Color` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"color"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
