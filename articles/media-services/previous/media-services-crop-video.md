---
title: Så här beskär du videor med Media Encoder Standard – Azure | Microsoft Docs
description: Beskärning är en process där du väljer ett rektangulärt fönster i video ramen och bara kodar pixlarna i det fönstret. Den här artikeln visar hur du beskär videor med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016737"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Beskär videoklipp med Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Du kan använda Media Encoder Standard (en gång) för att beskära din InVideo. Beskärning är en process där du väljer ett rektangulärt fönster i video ramen och bara kodar pixlarna i det fönstret. Följande diagram hjälper dig att illustrera processen.

![Beskär en video](./media/media-services-crop-video/media-services-crop-video01.png)

Anta att du har angett en video som har en upplösning på 1920x1080 bild punkter (16:9-höjd-breddförhållandet), men har svarta fält (pelare rutor) till vänster och höger, så att bara ett 4:3-fönster eller 1440x1080 bild punkter innehåller aktiv video. Du kan använda peka på att beskära eller redigera ut de svarta fälten och koda 1440x1080-regionen.

Att beskära i gång är ett för bearbetnings steg, så beskärnings parametrarna i kodnings för inställningen gäller för den ursprungliga indata-videon. Encoding är ett efterföljande steg och inställningarna för bredd/höjd gäller för den *förädlade* videon och inte till den ursprungliga videon. När du utformar din för inställning måste du göra följande: (a) Välj beskärnings parametrarna baserat på den ursprungliga Indataporten och (b) Välj dina kodnings inställningar baserat på den beskurna videon. Om du inte matchar dina kodnings inställningar till den beskurna videon visas inte utdata som du förväntar dig.

I [följande](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnitt visas hur du skapar ett kodnings jobb med aktiviteter och hur du anger en anpassad för inställning för kodnings uppgiften. 

## <a name="creating-a-custom-preset"></a>Skapa en anpassad för inställning
I exemplet som visas i diagrammet:

1. Den ursprungliga indatamängden är 1920x1080
2. Den måste beskäras till utdata från 1440x1080, som centreras i inmatnings ramen
3. Detta innebär en X-förskjutning (1920 – 1440)/2 = 240 och en Y-förskjutning på noll
4. Beskärnings Rektangelns bredd och höjd är 1440 respektive 1080
5. I kodnings fasen är frågan att producera tre lager, är lösningarna 1440x1080, 960x720 och 480x360

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="restrictions-on-cropping"></a>Begränsningar för beskärning
Beskärnings funktionen är avsedd att vara manuell. Du måste läsa in Indataporten i ett lämpligt redigerings verktyg där du kan välja intresse ramar, placera markören för att bestämma förskjutningar för beskärnings rektangeln för att fastställa kodnings inställningen som är justerad för just den videon osv. Den här funktionen är inte avsedd att göra det möjligt för saker som: automatisk igenkänning och borttagning av svarta Letterbox/pelare-ramar i din inspelnings video.

Följande begränsningar gäller för beskärnings funktionen. Om detta inte är uppfyllt, kan Omkodnings uppgiften misslyckats eller generera oväntade utdata.

1. Beskärnings områdets storlek och storlek måste anpassas i Indataporten
2. Som vi nämnt ovan måste bredden & höjden i koda inställningarna motsvara den beskurna videon
3. Beskärning gäller videor som fångats i liggande läge (dvs. inte tillgängligt för videor som registrerats med en smartphone som hålls lodrätt eller i stående läge)
4. Fungerar bäst med progressiv video som samlas in med fyrkantiga pixlar

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Se Azure Media Services inlärnings vägar som hjälper dig att lära dig mer om fantastiska funktioner som erbjuds av AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
