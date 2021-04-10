---
title: Uppgifts för inställningar för Media Encoder Standard (status) | Microsoft Docs
description: Avsnittet innehåller och en översikt över de tjänstedefinierade exempel för inställningarna för Media Encoder Standard (marknads status).
author: IngridAtMicrosoft
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011654"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Exempel för inställningar för Media Encoder Standard (marknads status)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** definierar en uppsättning fördefinierade system kodnings för inställningar som du kan använda när du skapar kodnings jobb. Vi rekommenderar att du använder för inställningen "anpassningsbar strömning" om du vill koda en video för strömning med Media Services. När du anger den här för inställningen [genererar Media Encoder standard automatiskt en stega-bit](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Skapa anpassade för inställningar från exempel
Media Services har fullständigt stöd för att anpassa alla värden i för inställningar för att uppfylla dina särskilda kodnings behov och krav. Om du behöver anpassa en kodnings för inställning, bör du börja med en av de nedan system för inställningar som anges i det här avsnittet som en mall för din anpassade konfiguration. Förklaringar av vad varje element i de här för inställningarna innebär och giltiga värden för varje element finns i avsnittet [Media Encoder Standard schema](media-services-mes-schema.md) .  
  
> [!NOTE]
>  När du använder en för inställning för 4K-kodningar bör du hämta den `S3` reserverade enhets typen. Mer information finns i [skala kodning](./media-services-scale-media-processing-overview.md).  

#### <a name="video-rotation-default-setting-in-presets"></a>Standardinställning för video rotation i för inställningar:
När du arbetar med Media Encoder Standard är video rotationen aktive rad som standard. Om din video har spelats in på en mobil enhet i stående läge, roterar dessa för inställningarna dem i liggande läge före kodning.
 
## <a name="available-presets"></a>Tillgängliga för inställningar: 

 [H264, med flera bit hastigheter 1080p 5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) ger en uppsättning av 8 GOP MP4-filer, från 6000 kbps till 400 kbit/s och AAC 5,1-ljud.  
  
 [H264, Multiple bit hastighet](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) ger en uppsättning av 8 GOP MP4-filer, mellan 6000 och 400 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bit hastighets 16x9 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) genererar en uppsättning 8 GOP MP4-filer, från 8500 kbps till 200 kbit/s och stereo AAC-ljud.  
  
 [H264, med flera bit hastigheter 16X9 SD audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) ger en uppsättning av 5 GOP MP4-filer, från 1900 kbps till 400 kbit/s och AAC 5,1-ljud.  
  
 [H264, Multiple bit hastighet 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) skapar en uppsättning av 5 GOP MP4-filer, från 1900 kbps till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, för flera bit hastigheter i 4k 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) ger en uppsättning av 12 GOP MP4-filer, från 20000 kbps till 1000 kbit/s och AAC 5,1 Audio.  
  
 [H264, Multiple bit hastighet](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) ger en uppsättning av 12 GOP MP4-filer, från 20000 kbps till 1000 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bit hastighets 4x3 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) genererar en uppsättning 8 GOP MP4-filer, från 8500 kbps till 200 kbit/s och stereo AAC-ljud.  
  
 [H264, med flera bit hastigheter 4X3 SD audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) ger en uppsättning av 5 GOP MP4-filer, från 1600 kbps till 400 kbit/s och AAC 5,1-ljud.  
  
 [H264, Multiple bit hastighet 4X3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) skapar en uppsättning av 5 GOP MP4-filer, från 1600 kbps till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, Multiple bit hastighet 720p audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) ger en uppsättning med 6 GOP MP4-filer, från 3400 kbps till 400 kbit/s och AAC 5,1 Audio.  
  
 [H264, Multiple bit hastighet](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) skapar en uppsättning med 6 GOP MP4-filer, från 3400 kbps till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, enbits 1080p-ljud 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) skapar en enda MP4-fil med en bit hastighet på 6750 kbps och AAC 5,1-ljud.  
  
 [H264, enbits 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) skapar en enda MP4-fil med en bit hastighet på 6750 kbit/s och stereo AAC-ljud.  
  
 [H264, med enkel bit 5,1 hastighet](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) ger en enda MP4-fil med en bit hastighet på 18000 kbps och AAC 5,1-ljud.  
  
 [H264, för enkel bit hastighet](media-services-mes-preset-H264-Single-Bitrate-4K.md) ger en enda MP4-fil med en bit hastighet på 18000 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bit hastighet 4X3 SD audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) skapar en enda MP4-fil med en bit hastighet på 1800 kbps och AAC 5,1-ljud.  
  
 [H264, Single bit hastighet 4X3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) skapar en enda MP4-fil med en bit hastighet på 1800 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bit hastighet 16X9 SD audio 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) skapar en enda MP4-fil med en bit hastighet på 2200 kbps och AAC 5,1-ljud.  
  
 [H264, Single bit hastighet 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) skapar en enda MP4-fil med en bit hastighet på 2200 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bit hastighet 720p audio 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) skapar en enda MP4-fil med en bit hastighet på 4500 kbps och AAC 5,1-ljud.  
  
 [H264, enkel bit hastighet för Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) -förvalet genererar en enda MP4-fil med en bit hastighet på 2000 kbit/s och stereo AAC.  
  
 [H264, enskild bit hastighet](media-services-mes-preset-H264-Single-Bitrate-720p.md) skapar en enda MP4-fil med en bit hastighet på 4500 kbit/s och stereo AAC-ljud.  
  
 [H264, med hög kvalitet och hög kvalitet som ger Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) en enda MP4-fil med en bit hastighet på 500 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bit hastighet med låg kvalitet för Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) skapar en enda MP4-fil med en bit hastighet på 56 kbit/s och stereo AAC-ljud.  
  
 Mer information om Media Services kodare finns i [encoding on-demand med Azure Media Services](./media-services-encode-asset.md).
