---
title: Varumärkesidentifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs ett specialläge för objektidentifiering. varumärkes- och/eller logotypidentifiering med hjälp Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 5892fc0bbbd07690ff010e8e1212a914733cbb18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778993"
---
# <a name="detect-popular-brands-in-images"></a>Identifiera populära varumärken i bilder

Varumärkesidentifiering är ett specialläge för [objektidentifiering](concept-object-detection.md) som använder en databas med tusentals globala logotyper för att identifiera kommersiella varumärken i bilder eller video. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering.

Tjänsten Visuellt innehåll identifierar om det finns varumärkeslogotyp i en viss bild. I så fall returneras varumärkesnamnet, förtroendepoängen och koordinaterna för en avgränsare runt logotypen.

Den inbyggda logotypdatabasen omfattar populära varumärken inom konsumentprodukter, kläder med mera. Om du upptäcker att det varumärke som du letar efter inte identifieras av Visuellt innehåll-tjänsten kan det vara bättre att skapa och träna din egen logotypdetektor med [hjälp av Custom Vision tjänsten.](../custom-vision-service/index.yml)

## <a name="brand-detection-example"></a>Exempel på varumärkesidentifiering

Följande JSON-svar illustrerar Visuellt innehåll returneras när du identifierar varumärken i exempelbilderna.

![En röd skjorta med en Microsoft-etikett och logotyp på sig](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

I vissa fall hämtar varumärkesdetektorn både logotypbilden och det slogade varumärkesnamnet som två separata logotyper.

![En grå sedel med en Microsoft-etikett och logotyp på sig](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Använda API:et

Varumärkesidentifieringsfunktionen är en [](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) del av Analysera bild-API:et. Du kan anropa det här API:et via en intern SDK eller via REST-anrop. Inkludera `Brands` i **frågeparametern visualFeatures.** När du sedan får det fullständiga JSON-svaret parsar du helt enkelt strängen för innehållet i `"brands"` avsnittet.

* [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)