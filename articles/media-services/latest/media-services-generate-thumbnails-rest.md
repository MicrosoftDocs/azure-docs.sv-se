---
title: Generera miniatyrer med Media Encoder Standard REST
description: Den här artikeln visar hur du använder REST för att koda en till gång och skapa miniatyrer samtidigt med hjälp av Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a5c21c384af90341161bab9407b3f6bcd3a4efac
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107612"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Så här genererar du miniatyr bilder med hjälp av Encoder-standarden med REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du kan använda Media Encoder Standard för att generera en eller flera miniatyrer från indata-videon i bild fil formaten [JPEG](https://en.wikipedia.org/wiki/JPEG), [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics)eller [BMP](https://en.wikipedia.org/wiki/BMP_file_format) .

## <a name="recommended-reading-and-practice"></a>Rekommenderad läsning och praxis

Vi rekommenderar att du bekanta dig med anpassade [transformeringar genom att läsa hur du kodar med en anpassad transformering-rest](encode-custom-preset-rest-how-to.md).

## <a name="thumbnail-parameters"></a>Miniatyr parametrar

Du bör ange följande parametrar:

- **Start** – den position i Indataporten där du vill börja skapa miniatyr bilder. Värdet kan vara i ISO 8601-format (t. ex. PT05S för att starta vid 5 sekunder) eller ett antal ramar (till exempel 10 för att starta vid den tionde ramen) eller ett relativt värde till Stream-varaktighet (till exempel 10% för att starta vid 10% av ström varaktigheten). Stöder också ett makro {Best}, som instruerar kodaren att välja den bästa miniatyren från de första sekunderna av videon och kommer bara att producera en miniatyr, oavsett vad andra inställningar är för steg och intervall. Standardvärdet är makro {Best}.
- **steg** – de intervall som miniatyrerna skapas med. Värdet kan vara i formatet ISO 8601 (till exempel PT05S för en bild var femte sekund) eller ett antal ramar (till exempel 30 för en bild var 30: e bild punkt) eller ett relativt värde till Stream-varaktighet (till exempel 10% för en bild var 10: e% av data strömmens varaktighet). Steg värde påverkar den första skapade miniatyr bilden, som kanske inte är exakt den som anges i Start tiden för förinställda transformeringar. Detta beror på Encoder, som försöker välja den bästa miniatyren mellan start tiden och steg positionen från start tiden som det första resultatet. Eftersom standardvärdet är 10% innebär det att om data strömmen har lång varaktighet, kan den första skapade miniatyr bilden vara långt bort från det som anges vid start tiden. Försök att välja ett rimligt värde för steg om den första miniatyren förväntas vara nära start tiden, eller ange värdet 1 om det bara finns en miniatyr som behövs vid start tiden.
- **intervall** – positionen i förhållande till förinställt Transformations start tid i Indataporten där du vill sluta skapa miniatyr bilder. Värdet kan vara i formatet ISO 8601 (till exempel PT5M30S för att stoppas vid 5 minuter och 30 sekunder från start tiden), eller ett antal ramar (till exempel 300 för att stoppa vid 300th-ramen från ramen vid start tillfället. Om det här värdet är 1, innebär det bara att en miniatyr visas vid start tiden, eller ett relativt värde till data Ströms varaktigheten (till exempel 50% för att stoppa vid hälften av ström varaktigheten från start tiden). Standardvärdet är 100%, vilket innebär att stoppa i slutet av data strömmen.
- **lager** – en samling med utgående bild lager som ska skapas av kodaren.

## <a name="example-of-a-single-png-file-preset"></a>Exempel på en "enskild PNG-fil"-förinställd

Följande JSON-förinställning kan användas för att skapa en PNG-fil med en enda utdata från de första sekunderna i indata-videon, där kodaren gör ett bästa försök att hitta en "intressant" ram. Observera att måtten för utgående bilder har angetts till 100%, vilket innebär att dessa matchar måtten för inmatnings videon. Observera också hur inställningen "format" i "utdata" krävs för att matcha användningen av "PngLayers" i avsnittet "codecs".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exempel på en "serie med JPEG-bilder"-förval

Följande JSON-förinställning kan användas för att skapa en uppsättning 10 bilder vid tidsstämplar på 5%, 15%,..., 95% av tids linjen för indata, där bild storleken anges som en fjärdedel av indata-videon.

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exempel på en för inställning för "en bild vid en speciell tidsstämpel"

Följande JSON-förinställning kan användas för att skapa en JPEG-bild med det 30 andra märket av indata-videon. Den här inställningen förväntar sig att Indataporten ska vara mer än 30 sekunder lång (annars Miss lyckas jobbet).

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exempel på en inställning för "miniatyrer med olika upplösningar"

Följande för inställningar kan användas för att generera miniatyrer med olika upplösningar i en aktivitet. I exemplet i positionerna 5%, 15%,..., 95% av ingångs tids linjen, genererar kodaren två bilder – en till 100% av video upplösningen och den andra vid 50%.

Observera att {resolution}-makro används i fil namnet; den anger att kodaren ska använda den bredd och höjd som du angav i avsnittet kodning i förvalet när du genererar fil namnet för de utgående bilderna. Detta gör det också enkelt att skilja mellan olika avbildningar.

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exempel på att skapa en miniatyr vid kodning

Även om alla ovanstående exempel har diskuterat hur du kan skicka en kodnings uppgift som bara genererar bilder, kan du också kombinera video-/ljud kodning med en miniatyr bild. Följande JSON-förinställning instruerar Encoder Standard för att generera en miniatyr under kodning.

### <a name="json-preset"></a>JSON-förinställning

Information om schema finns i [den här](../previous/media-services-mes-schema.md) artikeln.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
[Generera miniatyrer med .NET](media-services-generate-thumbnails-dotnet.md)