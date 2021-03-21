---
title: Azure Media Services v3-schema för indata-metadata
description: Den här artikeln ger en översikt över Azure Media Services v3-schemat för indata-metadata.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634693"
---
# <a name="input-metadata"></a>Inkommande metadata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ett kodnings jobb är associerat med en ingångs till gång (eller till gångar) som du vill utföra vissa kodnings uppgifter på.  När en aktivitet har slutförts skapas en utmatnings till gång. Utmatnings till gången innehåller video, ljud, miniatyrer, manifest och andra filer. 

Till gången till utdata innehåller också en fil med metadata om inmatnings till gången. Namnet på JSON-filen för metadata har ett slumpmässigt ID. Använd den inte för att identifiera den inmatnings till gång som den utgående till gången tillhör. Använd `Uri` fältet (mer information finns i [andra underordnade element](#other-child-elements)) för att identifiera den indata-till gång som den tillhör.  

Media Services genomsöker inte indata till gångar för att generera metadata. Metadata för indata genereras bara som en artefakt när en indata-till gång bearbetas i ett jobb. Därför skrivs denna artefakt till utmatnings till gången. Olika verktyg används för att generera metadata för indata till gångar och utgående till gångar. Därför har metadata för indata ett något annorlunda schema än utdata-metadata.

I den här artikeln beskrivs element och typer för JSON-schemat där inmetada ( &lt; asset_id &gt;_metadata.jspå) baseras. Information om filen som innehåller metadata om utmatnings till gången finns i [utdata-metadata](output-metadata-schema.md).  

Exemplet på JSON-schemat finns i slutet av den här artikeln.  

## <a name="assetfile"></a>AssetFile  

Innehåller en samling av AssetFile-element för kodnings jobbet.  

> [!NOTE]
> Följande fyra underordnade element måste finnas i en sekvens.

| Name  | Beskrivning |
| --- | --- | 
| **VideoTracks**|Varje fysisk till gångs fil kan innehålla noll eller flera video spår som överlämnas till ett lämpligt behållar format. Mer information finns i [VideoTracks](#videotracks). |
| **AudioTracks**|Varje fysisk till gångs fil kan innehålla noll eller flera ljud spår som överlämnas till ett lämpligt behållar format. Mer information finns i [AudioTracks](#audiotracks) |
| **Metadata**  |Till gångs filens metadata representeras som key\value-strängar. <br />Exempelvis: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Andra underordnade element

| Name | Beskrivning |
| --- | --- |
| **Namn**<br />Obligatorisk |Till gångs fil namn. <br /><br />Exempel: `"Name": "Ignite-short.mp4"` |
| **URI**<br />Obligatorisk |URL: en där inmatad till gång är placerad. För att identifiera den inmatnings till gång som utmatnings till gången tillhör, använder du `Uri` fältet i stället för ID.|
| **Storlek**<br />Obligatorisk |Storlek på till gångs filen i byte.  <br /><br />Exempel: `"Size": 75739259`|
| **Varaktighet**<br />Obligatorisk |Innehållets uppspelnings varaktighet. <br /><br />Exempel: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Obligatorisk |Antal strömmar i till gångs filen.  <br /><br />Exempel: `"NumberOfStreams": 2`|
| **FormatNames**<br />Obligatorisk |Format namn.  <br /><br />Exempel: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Obligatorisk |Formatera utförliga namn. <br /><br />Exempel: `"FormatVerboseName": "QuickTime / MOV"` |
| **/St** |Start tid för innehåll.  <br /><br />Exempel: `"StartTime": "PT0S"` |
| **OverallBitRate** |Genomsnittlig bit hastighet för till gångs filen i bitar per sekund.  <br /><br />Exempel: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Name | Beskrivning |
| --- | --- |
| **FourCC**<br />Obligatorisk |Video-codec FourCC-kod som rapporteras av ffmpeg.<br /><br />Exempel: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Profil** |Video spårets profil. <br /><br />Exempel: `"Profile": "Main"`|
| **Nivå** |Video spårets nivå. <br /><br />Exempel: `"Level": "3.2"`|
| **PixelFormat** |Video spårets bild punkts format. <br /><br />Exempel: `"PixelFormat": "yuv420p"`|
| **Bredd**<br />Obligatorisk |Kodad video bredd i bild punkter. <br /><br />Exempel: `"Width": "1280"`|
| **Höjd**<br />Obligatorisk |Kodad video höjd i bild punkter.<br /><br />Exempel: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Obligatorisk |Täljare för bild förhållande i bild förhållande.<br /><br />Exempel: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Obligatorisk |Nämnare för bild förhållande i bild. <br /><br />Exempel: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Bild förhållande – täljare för video exempel. <br /><br />Exempel: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Exempel: `"SampleAspectRatioDenominator": 1.0`|
| **Ram**<br />Obligatorisk |Uppmätt video bild Rute frekvens i. 3F-format. <br /><br />Exempel: `"FrameRate": 29.970`|
| **Hastigheten** |Genomsnittlig video bit hastighet i bitar per sekund, beräknat från till gångs filen. Endast den grundläggande data Ströms nytto lasten räknas och förpacknings omkostnaderna ingår inte. <br /><br />Exempel: `"Bitrate": 8421583`|
| **HasBFrames** |Video spårs nummer för B-ramar. <br /><br />Exempel: `"HasBFrames": 2`|
| **Metadata** |Generiska nyckel/värde-strängar som kan användas för att lagra en rad olika uppgifter. <br />Se det fullständiga exemplet i slutet av artikeln. |
| **Identitet**<br />Obligatorisk |Noll-baserat index för det här ljud-eller video spåret.<br /><br /> Detta **ID** är inte nödvändigt vis det TrackID som används i en MP4-fil. <br /><br />Exempel: `"Id": 2`|
| **ADPCM** |Video spårets codec-sträng. <br /><br />Exempel: `"Codec": "h264 | hev1"`|
| **CodecLongName** |Ljud-eller video spårs-codec långt namn. <br /><br />Exempel: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **ADPCM** |Video spårets codec-sträng. <br /><br />Exempel: `"Codec": "h264 | hev1"`|
| **Tids**<br />Obligatorisk |Tids bas.<br /><br />Exempel: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Antal bild rutor (som finns för video spår). <br /><br />Exempel: `"NumberOfFrames": 2107`|
| **/St** |Spåra start tid.<br /><br />Exempel: `"StartTime": "PT0.033S"` |
| **Varaktighet** |Spår varaktighet. <br /><br />Exempel: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Name  | Beskrivning |
| --- | --- | 
| **SampleFormat** |Exempel format. <br /><br />Exempel: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Kanalens layout. <br /><br />Exempel: `"ChannelLayout": "stereo"`|
| **Kanaler**<br />Obligatorisk |Antal (0 eller fler) ljud kanaler. <br /><br />Exempel: `"Channels": 2`|
| **SamplingRate**<br />Obligatorisk |Ljud samplings frekvens i sampel/SEK eller Hz. <br /><br />Exempel: `"SamplingRate": 48000`|
| **Hastigheten** |Genomsnittlig ljud bit hastighet i bitar per sekund, beräknat från till gångs filen. Endast den grundläggande data Ströms nytto lasten räknas och förpacknings omkostnaderna ingår inte i det här antalet. <br /><br />Exempel: `"Bitrate": 192080`|
| **Metadata** |Generiska nyckel/värde-strängar som kan användas för att lagra en rad olika uppgifter.  <br />Se det fullständiga exemplet i slutet av artikeln. |
| **Identitet**<br />Obligatorisk |Noll-baserat index för det här ljud-eller video spåret.<br /><br /> Detta är inte nödvändigt vis att TrackID som används i en MP4-fil. <br /><br />Exempel: `"Id": 1`|
| **ADPCM** |Video spårets codec-sträng. <br /><br />Exempel: `"Codec": "aac"`|
| **CodecLongName** |Ljud-eller video spårs-codec långt namn. <br /><br />Exempel: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Tids**<br />Obligatorisk |Tids bas.<br /><br />Exempel: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Antal bild rutor (som finns för video spår). <br /><br />Exempel: `"NumberOfFrames": 3294`|
| **/St** |Spåra start tid. Mer information finns i [iso8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Exempel: `"StartTime": "PT0S"` |
| **Varaktighet** |Spår varaktighet. <br /><br />Exempel: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadata

| Name | Beskrivning |
| --- | --- |
| **knapp**<br />Obligatorisk |Nyckeln i nyckel/värde-paret. |
| **värde**<br /> Obligatorisk |Värdet i nyckel/värde-paret. |

## <a name="schema-example"></a>Schema exempel

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

[Utgående metadata](output-metadata-schema.md)
