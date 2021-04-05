---
title: Video Indexer scener, bilder och nyckel bild rutor
titleSuffix: Azure Media Services
description: Det här avsnittet ger en översikt över Video Indexer scener, bilder och nyckel rutor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041908"
---
# <a name="scenes-shots-and-keyframes"></a>Scener, klipp och nyckelbilder

Video Indexer har stöd för att segmentera videor till temporala enheter baserat på strukturella och semantiska egenskaper. Den här funktionen gör det möjligt för kunder att enkelt bläddra i, hantera och redigera sitt video innehåll baserat på varierande granularitet. Till exempel, baserat på scener, dum par och nyckel rutor, som beskrivs i det här avsnittet.   

![Scener, klipp och nyckelbilder](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scen identifiering  
 
Video Indexer anger när en scen ändras i video baserat på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande bilder, som är semantiskt relaterade. En scen miniatyr är den första nyckel rutan i den underliggande bilden. Video Indexer segmenterar en video i scener utifrån färg samstämmighet över flera bilder och hämtar start-och slut tid för varje scen. Scen identifiering anses vara en utmanande uppgift eftersom det innebär att du kan kvantifiera semantiska aspekter av videor.

> [!NOTE]
> Gäller för videor som innehåller minst tre scener.

## <a name="shot-detection"></a>Bild identifiering

Video Indexer bestämmer när en bild ändras i videon baserat på visuella tips, genom att spåra både plötsligt och gradvisa över gångar i färgschemat för intilliggande bild rutor. Bildens metadata innehåller en start-och slut tid, samt listan över nyckel rutor som ingår i bilden. Bilderna är i flera bild rutor tagna från samma kamera på samma tid.

## <a name="keyframe-detection"></a>Identifiering av nyckel rutor

Video Indexer väljer den eller de ramar som bäst representerar varje bild. Nyckel rutor är de representativa bild rutor som valts från hela videon baserat på de egenskaper som är markerade (till exempel kontrast och stabilhet). Video Indexer hämtar en lista över nyckel Rute-ID: n som en del av bildens metadata, baserat på vilka kunder som kan extrahera nyckel rutan som en hög upplöst bild.  

### <a name="extracting-keyframes"></a>Extraherar nyckel rutor

Om du vill extrahera nyckel bild rutor med hög upplösning för videon måste du först ladda upp och indexera videon.

![Nyckel rutor](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Med Video Indexer webbplats

Om du vill extrahera nyckel rutor med Video Indexer webbplats laddar du upp och indexerar videon. När indexerings jobbet är klart klickar du på **nedladdnings** knappen och väljer **artefakter (zip)**. Då hämtas mappen artefakter till datorn. 

![Skärm bild som visar List rutan "Ladda ned" med "artefakter" valda.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Zippa upp och öppna mappen. I mappen *_KeyframeThumbnail* och du hittar alla nyckel rutor som har extraherats från videon. 

#### <a name="with-the-video-indexer-api"></a>Med Video Indexer API

Om du vill hämta nyckel rutor med Video Indexer-API: t laddar du upp och indexera videon med [uppladdnings video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) samtalet. När indexerings jobbet är klart anropar du [Hämta video index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Detta ger dig alla insikter som Video Indexer extraheras från ditt innehåll i en JSON-fil.  

Du får en lista över nyckel Rute-ID: n som en del av varje bilds metadata. 

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

Du kommer nu att behöva köra var och en av dessa ID: n för nyckel rutorna i [Get thumbnails](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) -anropet. Detta laddar ned var och en av nyckel Rute bilderna till datorn. 

## <a name="editorial-shot-type-detection"></a>Identifiering av redaktionella typer av tagning

Nyckel rutor är associerade med bilder i JSON-utdata. 

Den bildtyp som är associerad med en enskild bild i insikts-JSON representerar en redaktionell typ. Du kanske tycker att de här typerna av bildtyp är användbara när du redigerar videor i klipp, släp vagnar eller när du söker efter en speciell typ av nyckel bild ruta för konstnärliga bruk. De olika typerna bestäms baserat på analys av den första nyckel rutan för varje bild. Bilderna identifieras av skala, storlek och plats för de ytor som visas i den första nyckel rutan. 

Intagningens storlek och skala bestäms utifrån avståndet mellan kameran och de ytor som visas i ramen. Med dessa egenskaper identifierar Video Indexer följande typer av bild:

* Bred: visar en hel persons brödtext.
* Medel: visar en persons övre brödtext och ansikte.
* Stäng: visar främst en persons ansikte.
* Extrem närbild: visar en persons ansikte som fyller skärmen. 

Olika typer av tagningar kan också bestämmas genom platsen för ämnes tecknen med avseende på ramens centrum. Den här egenskapen definierar följande bild typer i Video Indexer:

* Vänster ansikte: en person visas på vänster sida av ramen.
* Mitten ansikte: en person visas i den centrala regionen i ramen.
* Höger sida: en person visas till höger i ramen.
* Utomhus: en person visas i en inställning för utomhus.
* I inomhus: en person visas i en intill-inställning.

Ytterligare egenskaper:

* Två bilder: visar två personers ansikten av medel storlek.
* Flera ansikten: fler än två personer.


## <a name="next-steps"></a>Nästa steg

[Granska Video Indexer utdata som genereras av API: et](video-indexer-output-json-v2.md#scenes)
