---
title: Varumärkes identifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Den här artikeln beskriver ett specialiserat läge för objekt identifiering. identifiering av märke och/eller logo typ med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 40792585fbc52aaeec8a535b6a82decfce7618f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533697"
---
# <a name="detect-popular-brands-in-images"></a>Identifiera populära varumärken i bilder

Varumärkes identifiering är ett specialiserat läge för [objekt identifiering](concept-object-detection.md) som använder en databas med tusentals globala logo typer för att identifiera kommersiella varumärken i bilder eller video. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering.

Tjänsten Visuellt innehåll identifierar om det finns varumärkes logo typer i en specifik bild. i så fall returneras märkes namnet, en förtroende poäng och koordinaterna för en avgränsnings ruta runt logo typen.

Den inbyggda logo typ databasen omfattar populära varumärken i konsument elektronik, kläder med mera. Om du upptäcker att det varumärke som du söker efter inte upptäcks av Visuellt innehålls tjänsten, kan du vara bättre på att skapa och träna din egen logo typ detektor med hjälp av tjänsten [Custom vision](../custom-vision-service/index.yml) .

## <a name="brand-detection-example"></a>Exempel på varumärkes identifiering

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du identifierar varumärken i exempel bilderna.

![En röd skjorta med Microsofts etikett och logo typ](./Images/red-shirt-logo.jpg)

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

I vissa fall hämtar varumärkes detektorn både logo typ bilden och det namnlösa varumärkes namnet som två separata logo typer.

![En grå sweatshirt med en Microsoft-etikett och logo typ på den](./Images/gray-shirt-logo.jpg)

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

Varumärkes identifierings funktionen är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Brands` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"brands"` avsnittet.

* [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)