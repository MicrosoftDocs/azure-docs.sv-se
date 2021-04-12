---
title: Under klipp en video vid kodning med Media Services REST
description: I det här avsnittet beskrivs hur du klipper en video när du kodar med Azure Media Services med hjälp av REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 094c7affb5b285db4e3311fb1eac9b36afdb6458
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490899"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Under klipp en video vid kodning med Media Services REST

Du kan trimma eller under klipp en video när du kodar den med ett [jobb](/rest/api/media/jobs). Den här funktionen fungerar med alla [transformeringar](/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar. 

RESTEN av exemplet i det här avsnittet skapar ett jobb som rensar en video när den skickar ett kodnings jobb. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste du:

- [Skapa ett Azure Media Services-konto](./account-create-how-to.md).
- [Konfigurera PostMan för Azure Media Services REST API-anrop](setup-postman-rest-how-to.md).
    
    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](setup-postman-rest-how-to.md#get-azure-ad-token). 
- Skapa en transformering och en utmatnings till gångar. Du kan se hur du skapar en transformering och en utmatnings till gångar i [koda en fjärrfil baserat på URL och strömma video rest-](stream-files-tutorial-with-rest.md) kursen.
- Läs avsnittet [encoding Concept](encode-concept.md) .

## <a name="create-a-subclipping-job"></a>Skapa ett under urklipps jobb

1. I Postman-samlingen som du laddade ned väljer du **transformeringar och jobb**  ->  **skapa jobb med under Urklipp**.
    
    **Placerings** förfrågan ser ut så här:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Uppdatera värdet för miljövariabeln "transformName" med Transformations namnet. 
1. Välj fliken **brödtext** och uppdatera "myOutputAsset" med namnet på din utmatnings till gång.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Tryck på **Skicka**.

    Du kan se **svaret** med information om jobbet som skapades och skickades och jobbets status. 

## <a name="next-steps"></a>Nästa steg

[Koda med en anpassad transformering](transform-custom-preset-rest-how-to.md) 
