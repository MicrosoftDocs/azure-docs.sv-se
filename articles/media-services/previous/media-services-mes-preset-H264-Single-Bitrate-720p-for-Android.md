---
title: H264, enkel bit hastighet 720p för Android | Microsoft Docs
description: Avsnittet ger en översikt över **H264, enkel bit hastighet för Android Task för** inställning.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4f9569a3-5aca-4fea-8242-024925a8af90
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a05203925c0e731c1c05e0c97d0f67f997419970
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016448"
---
# <a name="h264-single-bitrate-720p-for-android"></a>H264, enkel bithastighet, 720p för Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definierar en uppsättning kodnings för inställningar som du kan använda när du skapar kodnings jobb. Du kan antingen använda en `preset name` för att ange i vilket format du vill koda medie filen. Du kan också skapa egna JSON-eller XML-baserade för hands inställningar (med UTF-8-eller UTF-16-kodning. Sedan skickar du den anpassade för inställningen till kodaren. En lista över alla förinställda namn som stöds av den här `Media Encoder Standard` kodaren finns i [uppgifts för inställningar för Media Encoder Standard](media-services-mes-presets-overview.md).  
  
I det här avsnittet visas för `H264 Single Bitrate 720p for Android` inställningen i XML-och JSON-format.  
  
Den här för inställningen skapar en enda MP4-fil med en bit hastighet på 2000 kbit/s och stereo AAC. Detaljerad information om profil, bit hastighet, samplings frekvens osv. för den här för inställningen finns i XML-eller JSON-definitionen nedan. Förklaringar av vad varje element i de här för inställningarna innebär och giltiga värden för varje element finns i avsnittet [Media Encoder Standard schema](media-services-mes-schema.md) .  
  
 XML  
  
```
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:05</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>2000</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Baseline</Profile>
          <Level>3.1</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>false</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2000</MaxBitrate>
        </H264Layer>
      </H264Layers>
      <Chapters />
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>192</Bitrate>
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
      "KeyFrameInterval": "00:00:05",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Profile": "Baseline",
          "Level": "3.1",
          "Bitrate": 2000,
          "MaxBitrate": 2000,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
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
      "Bitrate": 192,
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
