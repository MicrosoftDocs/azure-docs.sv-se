---
title: H264, enkel bit hastighet Media Encoder Standard för inställning – Azure | Microsoft Docs
description: Artikeln ger en översikt över för hands inställningen Media Encoder Standard H264,-enkel bit hastighet för 4K-uppgifter.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 62984c938a3db67550b36c731e7a5a27de5df7ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012878"
---
# <a name="h264-single-bitrate-4k"></a>H264, enkel bithastighet, 4K

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definierar en uppsättning kodnings för inställningar som du kan använda när du skapar kodnings jobb. Du kan antingen använda en `preset name` för att ange i vilket format du vill koda medie filen. Du kan också skapa egna JSON-eller XML-baserade för hands inställningar (med UTF-8-eller UTF-16-kodning. Sedan skickar du den anpassade för inställningen till kodaren. En lista över alla förinställda namn som stöds av den här `Media Encoder Standard` kodaren finns i [uppgifts för inställningar för Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 I det här avsnittet visas för `H264 Single Bitrate 4K` inställningen i XML-och JSON-format.  
  
 Den här för inställningen skapar en enda MP4-fil med en bit hastighet på 18000 kbit/s och stereo AAC-ljud. Detaljerad information om profil, bit hastighet, samplings frekvens osv. för den här för inställningen finns i XML-eller JSON-definitionen nedan. Förklaringar av vad varje element i de här för inställningarna innebär och giltiga värden för varje element finns i avsnittet [Media Encoder Standard schema](media-services-mes-schema.md) .  
  
> [!NOTE]
>  Du bör hämta den reserverade enhets typen Premium med 4K-kodar. Mer information finns i [skala kodning](./media-services-scale-media-processing-overview.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
