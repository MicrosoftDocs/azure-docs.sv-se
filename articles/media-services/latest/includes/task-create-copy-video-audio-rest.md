---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/19/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c01f57e2cfc9cd4351a39292258c554412eca555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94974953"
---
<!--Create a basic audio transform REST-->

Med följande Azure REST-kommando kan du kopiera video och ljud från en plats till en annan utan ytterligare kodning. Ersätt värdena `subscriptionID` , `resourceGroup` och `accountName` med värden som du håller på att arbeta med. Ge transformeringen ett namn genom att ange `transformName` .

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>Brödtext

```json
{
    "properties": {
        "description": "Copy video and audio files with no encoding",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="sample-response"></a>Exempelsvar
```json
{
    "name": "aacaudio",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/aacaudio",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-11-19T00:12:48.6001786Z",
        "description": "Copy audio files with no encoding",
        "lastModified": "2020-11-19T17:50:23.9602077Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}",
                            "outputFiles": []
                        }
                    ]
                }
            }
        ]
    }
}
```