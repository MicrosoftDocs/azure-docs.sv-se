---
title: Identifiering av färg schema – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör att identifiera färg schema i bilder med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e7774d2cd100931f92ff80066ebea4463c6f65c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532660"
---
# <a name="detect-color-schemes-in-images"></a>Identifiera färg scheman i bilder

Visuellt innehåll analyserar färgerna i en bild för att tillhandahålla tre olika attribut: den dominerande förgrunds färgen, den dominerande bakgrunds färgen och uppsättningen av dominerande färger för bilden som helhet. Returnerade färger tillhör uppsättningen: svart, blå, brun, grå, grön, orange, rosa, lila, röd, blågrön, vit och gul. 

Visuellt innehåll också extrahera en accentfärg, som representerar den mest livfulla färgen i bilden, baserat på en kombination av dominerande färger och mättnad. Dekor färgen returneras som en hexadecimal HTML-färgkod. 

Visuellt innehåll returnerar också ett booleskt värde som anger om en bild är i svart vitt.

## <a name="color-scheme-detection-examples"></a>Exempel på identifiering av färg scheman

I följande exempel visas ett JSON-svar som returneras av Visuellt innehåll när du identifierar exempel bildens färg schema. I det här fallet är exempel bilden inte en svartvit bild, men de dominerande förgrunds-och bakgrunds färgerna är svarta och de dominerande färgerna för bilden som helhet är svarta och vita.

![Utomhus mountainbike vid solnedgång, med en persons Silhouette](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Dominerande färg exempel

I följande tabell visas de färger som returneras för förgrunds-, bakgrunds-och bildfiler för varje exempel bild.

| Bild | Dominerande färger |
|-------|-----------------|
|![En vit blomma med grön bakgrund](./Images/flower.png)| Förgrund: svart<br/>Bakgrund: vit<br/>Färger: svart, vit, grönt|
![Ett tåg som körs via en station](./Images/train_station.png) | Förgrund: svart<br/>Bakgrund: svart<br/>Färger: svart |

### <a name="accent-color-examples"></a>Dekor färgs exempel

 I följande tabell visas den returnerade tilläggs färgen, som ett hexadecimalt HTML-färg värde, för varje exempel bild.

| Bild | Accentfärg |
|-------|--------------|
|![En person som står på en Mountain rock vid solnedgång](./Images/mountain_vista.png) | #BB6D10 |
|![En vit blomma med grön bakgrund](./Images/flower.png) | #C6A205 |
|![Ett tåg som körs via en station](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exempel på svart & White Detection

I följande tabell visas Visuellt innehållens svarta och vita utvärdering i exempel bilderna.

| Bild | Svart & vitt? |
|-------|----------------|
|![En svart och vit bild av byggnader i Manhattan](./Images/bw_buildings.png) | true |
|![Ett blått hus och en främre meter](./Images/house_yard.png) | falskt |

## <a name="use-the-api"></a>Använda API:et

Funktionen för identifiering av färg scheman är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Color` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"color"` avsnittet.

* [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
