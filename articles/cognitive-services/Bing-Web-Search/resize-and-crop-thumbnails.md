---
title: Ändra storlek och Beskär bild miniatyrer – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Vissa svar från API:er för Bing-sökresultat innehåller URL: er till miniatyr bilder som hanteras av Bing, som du kan ändra storlek på och beskära och kan innehålla frågeparametrar.'
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350491"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ändra storlek och beskär miniatyr bilder

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Vissa svar från API:er för Bing-sökresultat innehåller URL: er till miniatyr bilder som hanteras av Bing, som du kan ändra storlek på och beskära och kan innehålla frågeparametrar. Exempel:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Om du visar en delmängd av dessa miniatyr bilder, anger du ett alternativ för att visa återstående bilder.

> [!NOTE]
> Se till att beskära och ändra storlek på miniatyr bilder ger ett Sök scenario som respekterar tredje parts rättigheter, enligt kraven i Bing-sökning API- [användning och visnings krav](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Ändra storlek på en miniatyr bild 

Om du vill ändra storlek på en miniatyr, rekommenderar Bing att du bara anger en `w` frågeparametrar (width) eller `h` (höjd) i miniatyrens URL. Om du bara anger höjd eller bredd kan Bing behålla bildens ursprungliga aspekt. Ange bredd och höjd i bild punkter. 

Om den ursprungliga miniatyr bilden till exempel är 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Och du vill minska dess storlek, anger du `w` parametern till ett nytt värde (till exempel `336` ) och tar bort `h`  parametern:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Om du bara anger höjden eller bredden på en miniatyr kommer bildens ursprungliga höjd-förhållande att behållas. Om du anger båda parametrarna och proportionerna inte upprätthålls, kommer Bing att lägga till vit utfyllnad i bildens kant.

Om du till exempel ändrar storlek på en 480x359-bild till 200x200 utan beskärning, kommer den fulla bredden att innehålla bilden, men höjden kommer att innehålla 25 pixlar med vit utfyllnad överst och längst ned i bilden. Om bilden var 359x480 innehåller vänster och höger kant linjer vit utfyllnad. Om du beskär bilden läggs inte vit utfyllnad.  

Följande bild visar den ursprungliga storleken på en miniatyr bild (480x300).  
  
![Ursprunglig liggande bild](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Följande bild visar bild storleken ändrat till 200x200. Höjd-breddförhållandet upprätthålls och de övre och nedre kant linjerna fylls med vitt (den svarta kant linjen här ingår för att Visa utfyllnaden).  
  
![Ändra storlek på liggande bild](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Om du anger mått som är större än bildens ursprungliga bredd och höjd, så lägger Bing till vit utfyllnad till vänster och övre kant linje.  

## <a name="request-different-thumbnail-sizes"></a>Begär olika miniatyr storlekar

Om du vill begära en annan miniatyr bild storlek tar du bort alla frågeparametrar från miniatyrens URL, förutom `id` `pid` parametrarna och. Lägg sedan till antingen `&w` Frågeparametern (width) eller `&h` (height) med önskad bild storlek i bild punkter, men inte båda. Bing kommer att underhålla bildens ursprungliga höjd förhållande. 

Om du vill öka bredden på den bild som anges av ovanstående URL till 165 bild punkter använder du följande URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Om du begär en bild som är större än bildens ursprungliga storlek, lägger Bing till en vit utfyllnad runt bilden efter behov. Om bildens ursprungliga storlek till exempel är 474x316 och du har angett `&w` till 500 så returnerar Bing en 500x333-bild. Den här bilden kommer att ha 8,5 bild punkter med vit utfyllnad längs de övre och nedre kanterna, och 13 bild punkter i utfyllnaden på vänster och höger sida.

Om du vill förhindra att Bing lägger till vit utfyllnad om den begärda storleken är större än bildens ursprungliga storlek, anger du `&p` Frågeparametern till 0. Om du till exempel inkluderar `&p=0` parametern i ovanstående URL, returnerar Bing en 474x316-bild i stället för en 500x333-bild:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Om du anger båda `&w` `&h` parametrarna och frågeparametrar bevarar Bing bildens höjd-förhållande och lägger till vit utfyllnad vid behov. Om bildens ursprungliga storlek till exempel är 474x316 och du anger parametrarna width och height till 200x200 ( `&w=200&h=200` ), returnerar Bing en bild som innehåller 33 pixlar med vit utfyllnad högst upp och längst ned. Om du inkluderar `&p` Frågeparametern returnerar Bing en 200x134-bild.

## <a name="crop-a-thumbnail"></a>Beskär en miniatyr bild 

Om du vill beskära en avbildning tar du med `c` Frågeparametern (beskärning). Du kan använda följande värden:
  
- `4`&mdash;Blind kvot  
- `7`&mdash;Smart kvot  

### <a name="smart-ratio-cropping"></a>Beskärning av Smart grad

Om du begär en smart kvot beskärning (genom `c` att ange parametern till `7` ) beskär Bing en bild från mitten av dess intresse rad, samtidigt som bildens proportioner bibehålls. Intresse området är det område i bilden som Bing avgör innehåller de flesta import delar. Nedan visas ett exempel på ett intresse område.  
  
![Intresse region](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Om du ändrar storlek på en bild och begär Smart kvot beskärning, minskar Bingen bilden till den begärda storleken samtidigt som proportionerna bibehålls. Bing Beskär sedan bilden baserat på dimensioner för storleks ändring. Om till exempel den ändrade bredden är mindre än eller lika med höjden beskärs bilden till vänster och höger om intresse området. Annars beskärs den till toppen och längst ned i mitten av intresse regionen.  
  
 
Nedan visas bilden som är reducerad till 200x200 med hjälp av Smart grad-beskärning. Eftersom Bing mäter bilden från det övre vänstra hörnet beskärs den nedre delen av bilden. 
  
![Liggande bild beskuren till 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Nedan visas bilden som är reducerad till 200x100 med hjälp av Smart grad-beskärning. Eftersom Bing mäter bilden från det övre vänstra hörnet beskärs den nedre delen av bilden. 
   
![Liggande bild beskuren till 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Nedan visas bilden som är reducerad till 100x200 med hjälp av Smart grad-beskärning. Eftersom Bing mäter bilden från mitten beskärs den vänstra och högra delen av bilden.
  
![Liggande bild beskuren till 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Om Bing inte kan avgöra bildens intresse område, använder tjänsten blinda kvots beskärning.  

### <a name="blind-ratio-cropping"></a>Beskärning av blind förhållandet

Om du begär överanvändning av hemliga kvot (genom `c` att ange parametern till `4` ) använder Bing följande regler för att beskära bilden.  
  
- Om `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` bilden mäts från det övre vänstra hörnet och beskäras längst ned.  
- Om `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` bilden mäts från mitten och beskäras till vänster och höger.  

Nedan visas en stående bild som är 225x300.  
  
![Ursprunglig sol ros bild](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Nedan visas bilden som är reducerad till 200x200 med beskärning av blinda tal. Bilden mäts från det övre vänstra hörnet som visas i den nedre delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Nedan visas bilden som är reducerad till 200x100 med beskärning av blinda tal. Bilden mäts från det övre vänstra hörnet som visas i den nedre delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Nedan visas bilden som är reducerad till 100x200 med beskärning av blinda tal. Bilden mäts från mitten som skapas i den vänstra och högra delen av bilden som beskärs.  
  
![Sol ros bild beskuren till 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Nästa steg

* [Vad är API:er för Bing-sökresultat?](bing-api-comparison.md)
* [Användnings- och visningskrav för API:er för Bing-sökresultat](use-display-requirements.md)