---
title: Koda en anpassad Transformation CLI
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering med Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a91123b22eaef4cbaf2e96c93200777c8a85abab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092377"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Koda med en anpassad transformering – Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du kodar med Azure Media Services kan du snabbt komma igång med en av de rekommenderade inbyggda för inställningarna, baserat på bransch metod tips, som visas i snabb starten av [strömmande filer](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) . Du kan också bygga en anpassad för inställning för att rikta in dig på specifika scenario-eller enhets krav.

## <a name="considerations"></a>Överväganden

När du skapar anpassade för inställningar gäller följande aspekter:

* Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från för inställningarna med våra v2-API: er, som använde kilobit/sekund som enhet. Om bit hastigheten i v2 exempelvis angavs som 128 (kilobit/sekund), skulle den vara inställd på 128000 (bitar/sekund) i v3.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

Kom ihåg att komma ihåg resurs gruppens namn och namnet på Media Services kontot.

## <a name="define-a-custom-preset"></a>Definiera en anpassad för inställning

I följande exempel definieras begär ande texten för en ny transformering. Vi definierar en uppsättning utdata som vi vill ska genereras när denna transformering används.

I det här exemplet lägger vi först till ett AacAudio-lager för ljud kodningen och två H264Video-lager för video kodningen. I video lager tilldelar vi etiketter så att de kan användas i utdatafilernas namn. Nu vill vi att utdata även ska innehålla miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genererade med 50% av upplösningen för Indataporten och vid tre tidsstämplar – {25%, 50%, 75} av den angivna videons längd. Slutligen anger vi formatet för utdatafilerna – ett för video + ljud och en annan för miniatyr bilderna. Eftersom vi har flera H264Layers måste vi använda makron som producerar unika namn per lager. Vi kan antingen använda ett `{Label}` eller `{Bitrate}` -makro, exemplet visar det tidigare.

Vi kommer att spara den här transformeringen i en fil. I det här exemplet namnger vi filen `customPreset.json` .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
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
```

## <a name="create-a-new-transform"></a>Skapa en ny transformering  

I det här exemplet skapar vi en **transformering** som baseras på den anpassade för inställning som vi definierade tidigare. När du skapar en transformering bör du först kontrol lera om det redan finns en. Om transformeringen finns kan du använda den igen. Följande `show` kommando returnerar `customTransformName` omvandlingen om den finns:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Följande Azure CLI-kommando skapar transformeringen baserat på den anpassade för inställningen (definierad tidigare).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

För att Media Services ska kunna använda transformeringen på den angivna videon eller ljudet måste du skicka ett jobb under transformeringen. Ett komplett exempel som visar hur du skickar ett jobb under en transformering finns i [snabb start: strömma videofiler – Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Se även

[Azure CLI](/cli/azure/ams)
