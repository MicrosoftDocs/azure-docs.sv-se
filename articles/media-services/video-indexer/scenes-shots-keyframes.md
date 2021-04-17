---
title: Video Indexer, bilder och nyckelbilder
titleSuffix: Azure Media Services
description: Det här avsnittet ger en översikt över Video Indexer, bilder och nyckelbilder.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 5a738152296aacbb5914e859a65976bd0f6dbf0a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532415"
---
# <a name="scenes-shots-and-keyframes"></a>Scener, klipp och nyckelbilder

Video Indexer stöder segmentering av videor i temporala enheter baserat på strukturella och semantiska egenskaper. Den här funktionen gör det möjligt för kunder att enkelt bläddra, hantera och redigera videoinnehåll baserat på varierande kornighet. Till exempel, baserat på scener, bilder och nyckelbildrutor, som beskrivs i det här avsnittet.   

![Scener, klipp och nyckelbilder](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scenidentifiering  
 
Video Indexer avgör när en scen ändras i video baserat på visuella tips. En scen visar en enda händelse och består av en serie efterföljande bilder som är semantiskt relaterade. En scenminiatyr är den första nyckelbild av dess underliggande bild. Video Indexer segmenterar en video i scener baserat på färgkonsekvens i flera efterföljande bilder och hämtar början och sluttiden för varje scen. Scenidentifiering anses vara en utmanande uppgift eftersom den omfattar kvantifiering av semantiska aspekter av videor.

> [!NOTE]
> Gäller för videor som innehåller minst 3 scener.

## <a name="shot-detection"></a>Bildidentifiering

Video Indexer avgör när en bild ändras i videon baserat på visuella tips, genom att spåra både plötsliga och gradvisa övergångar i färgschemat för intilliggande bildrutor. Skärmdumpens metadata innehåller en start- och sluttid samt en lista över nyckelbildrutor som ingår i den bilden. Bilderna är på varandra följande bildrutor som tas från samma kamera samtidigt.

## <a name="keyframe-detection"></a>Identifiering av nyckelbild

Video Indexer väljer de bildruteramar som bäst representerar varje bild. Nyckelbilder är representativa bildrutor som väljs ut från hela videon baserat på estetiska egenskaper (till exempel kontrast och stabilitet). Video Indexer hämtar en lista över nyckelbilds-ID:n som en del av skärmdumpens metadata, baserat på vilka kunder kan extrahera nyckelramen som en högupplöst bild.  

### <a name="extracting-keyframes"></a>Extrahera nyckelramar

Om du vill extrahera högupplösta nyckelbildrutor för videon måste du först ladda upp och indexera videon.

![Nyckelrutor](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Med Video Indexer webbplats

Om du vill extrahera nyckelbildrutor Video Indexer på webbplatsen laddar du upp och indexerar videon. När indexeringsjobbet är klart klickar du på **knappen Ladda** ned och **väljer Artefakter (ZIP).** Då laddas mappen artifacts ned till datorn. 

![Skärmbild som visar listrutan "Ladda ned" med "Artefakter" valt.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Packa upp och öppna mappen. I *_KeyframeThumbnail* hittar du alla nyckelbildrutor som extraherades från videon. 

#### <a name="with-the-video-indexer-api"></a>Med Video Indexer API

Om du vill hämta nyckeldatorer med hjälp Video Indexer API:et laddar du upp och indexerar videon med hjälp av [anropet Överför](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) video. När indexeringsjobbet är klart anropar du [Hämta videoindex.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index) På så sätt får du alla insikter som Video Indexer extraherat från ditt innehåll i en JSON-fil.  

Du får en lista över nyckelbilds-ID:n som en del av varje bilds metadata. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Du måste nu köra var och en av dessa nyckelbilds-ID:er i [anropet Hämta miniatyrer.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) Då laddas var och en av nyckelbildbilderna ned till datorn. 

## <a name="editorial-shot-type-detection"></a>Identifiering av redigeringsbildtyper

Nyckeldatorer är associerade med bilder i JSON-utdata. 

Den typ av skärmdump som är associerad med en enskild bild i JSON för insikter representerar dess redigeringstyp. Du kanske tycker att dessa egenskaper för skärmdumpar är användbara när du redigerar videor till klipp, vagnar eller när du söker efter en specifik typ av nyckelbild för särskilda ändamål. De olika typerna bestäms baserat på analys av den första nyckelramen för varje bild. Bilder identifieras med hjälp av ansiktenas skala, storlek och plats som visas i den första nyckelramen. 

Bildens storlek och skala bestäms baserat på avståndet mellan kameran och ansiktena som visas i bilden. Med hjälp av dessa Video Indexer identifierar följande bildtyper:

* Bred: visar en hel persons brödtext.
* Medel: visar en persons övre brödtext och ansikte.
* När närmare: visar huvudsakligen en persons ansikte.
* Extrem närbild: visar en persons ansikte som fyller skärmen. 

Bildtyper kan också fastställas av platsen för ämnestecken med avseende på mitten av bildrutan. Den här egenskapen definierar följande bildtyper i Video Indexer:

* Vänster ansikte: en person visas på vänster sida av ramen.
* Mitt ansikte: en person visas i den centrala regionen av ramen.
* Höger ansikte: en person visas på höger sida av ramen.
* Utomhus: en person visas i en utomhusmiljö.
* Inomhus: en person visas i en inomhusmiljö.

Ytterligare egenskaper:

* Två bilder: visar två personers ansikten i medelstorlek.
* Flera ansikten: fler än två personer.


## <a name="next-steps"></a>Nästa steg

[Granska de Video Indexer som produceras av API:et](video-indexer-output-json-v2.md#scenes)
