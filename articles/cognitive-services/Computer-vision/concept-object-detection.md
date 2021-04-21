---
title: Objektidentifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör objektidentifieringsfunktionen i Visuellt innehåll API – användning och gränser.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a705a4134ec22d1cb14406cab4491f2af9177b48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768005"
---
# <a name="detect-common-objects-in-images"></a>Identifiera vanliga objekt i bilder

Objektidentifiering liknar [taggning, men](concept-tagging-images.md)API:et returnerar koordinaterna för markeringsrutan (i bildpunkter) för varje objekt som hittas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen för att bearbeta relationerna mellan objekten i en bild. Du kan också avgöra om det finns flera instanser av samma tagg i en avbildning.

Identifierings-API:et tillämpar taggar baserat på de objekt eller levande objekt som identifieras i bilden. Det finns för närvarande ingen formell relation mellan taggnings taxonomin och objektidentifierings taxonomin. På konceptuell nivå hittar API:et Identifiera endast objekt och levande saker, medan Tagg-API:et även kan innehålla sammanhangsberoende termer som "inomhus", som inte kan lokaliseras med avgränsade rutor.

## <a name="object-detection-example"></a>Exempel på objektidentifiering

Följande JSON-svar visar vad Visuellt innehåll returnerar när objekt identifieras i exempelbilden.

![En kvinna som använder en Microsoft Surface-enhet i ett köket](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Begränsningar

Det är viktigt att notera begränsningarna för objektidentifiering så att du kan undvika eller minimera effekterna av falska negativa identifieringar (missade objekt) och begränsad information.

* Objekt identifieras vanligtvis inte om de är små (mindre än 5 % av bilden).
* Objekt identifieras vanligtvis inte om de är placerade nära varandra (till exempel en stack med tallrikar).
* Objekten särskiljs inte efter varumärkes- eller produktnamn (till exempel olika typer av soda på en butikshyllor). Du kan dock hämta varumärkesinformation från en bild med hjälp av [funktionen Varumärkesidentifiering.](concept-brand-detection.md)

## <a name="use-the-api"></a>Använda API:et

Objektidentifieringsfunktionen är en del av [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Du kan anropa det här API:et via ett inbyggt SDK eller via REST-anrop. Inkludera `Objects` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"objects"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
