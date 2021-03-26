---
title: Så här beskär du videofiler med Media Services-.NET | Microsoft Docs
description: Beskärning är en process där du väljer ett rektangulärt fönster i video ramen och bara kodar pixlarna i det fönstret. Det här avsnittet visar hur du beskär videofiler med Media Services med .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572441"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Så här beskär du videofiler med Media Services-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du kan använda Media Services för att beskära en video. Beskärning är en process där du väljer ett rektangulärt fönster i video ramen och bara kodar pixlarna i det fönstret. Följande diagram hjälper dig att illustrera processen.

## <a name="pre-processing-stage"></a>För bearbetnings steg

Beskärning är ett för bearbetnings steg, så *beskärnings parametrarna* i kodnings för inställningen gäller för *indata* -videon. Encoding är ett efterföljande steg och inställningarna för bredd/höjd gäller för den *förädlade* videon och inte till den ursprungliga videon. När du utformar din för inställning gör du följande:

1. Välj beskärnings parametrarna baserat på den ursprungliga Indataporten
1. Välj dina kodnings inställningar baserat på den beskurna videon.

> [!WARNING]
> Om du inte matchar dina kodnings inställningar till den beskurna videon visas inte utdata som du förväntar dig.

Till exempel har Indataporten en upplösning på 1920x1080 bild punkter (16:9 höjd-breddförhållandet), men har svarta fält (pelare rutor) till vänster och höger, så att bara ett 4:3-fönster eller 1440x1080 bild punkter innehåller aktiv video. Du kan beskära de svarta fälten och koda 1440x1080-ytan.

## <a name="transform-code"></a>Transformera kod

Följande kodfragment visar hur du skriver en transformering i .NET för att beskära videor.  Koden förutsätter att du har en lokal fil att arbeta med.

- Left är den vänstra placeringen av beskärningen.
- Överkant är den viktigaste placeringen av beskärningen.
- Bredden är beskärningens slut bredd.
- Height är beskärningens slutliga höjd.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
