---
title: Schema för Azure Media Services utdata-metadata
description: Den här artikeln ger en översikt över Azure Media Services v3-schemat för utdata för metadata.
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
ms.openlocfilehash: 1a27db81866760bff56c900a908162f356c01a66
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895211"
---
# <a name="output-metadata"></a>Utgående metadata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ett kodnings jobb är associerat med en ingångs till gång (eller till gångar) som du vill utföra vissa kodnings uppgifter på. Koda till exempel en MP4-fil till H. 264 MP4-anpassade bit hastighets uppsättningar; skapa en miniatyr bild. Skapa överlägg. När en aktivitet har slutförts skapas en utmatnings till gång.  Utmatnings till gången innehåller video, ljud, miniatyrer och andra filer. Till gången till utdata innehåller också en fil med metadata om utmatnings till gången. Namnet på JSON-filen för metadata har följande format: `<source_file_name>_manifest.json` (till exempel `BigBuckBunny_manifest.json` ). Du bör söka efter * _metadata.jspå och fråga Sök väg strängen i för att hitta käll fil namnet (utan trunkering).

Media Services genomsöker inte indata till gångar för att generera metadata. Metadata för indata genereras bara som en artefakt när en indata-till gång bearbetas i ett jobb. Därför skrivs denna artefakt till utmatnings till gången. Olika verktyg används för att generera metadata för indata till gångar och utgående till gångar. Därför har metadata för indata ett något annorlunda schema än utdata-metadata.

I den här artikeln beskrivs element och typer för JSON-schemat där metadata för utdata ( &lt; source_file_name &gt;_manifest.jspå) baseras. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Du hittar den fullständiga schema koden och JSON-exemplet i slutet av den här artikeln.  

## <a name="assetfile"></a>AssetFile

Samling av AssetFile-poster för kodnings jobbet.  

| Name | Beskrivning |
| --- | --- |
| **Källor** |Samling med indatafiler/källfiler, som bearbetades för att skapa den här AssetFile.<br />Exempel: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Varje fysisk AssetFile kan innehålla i noll eller flera videor spårar överlagrad i ett lämpligt behållar format. <br />Se [VideoTracks](#videotracks). |
| **AudioTracks**|Varje fysisk AssetFile kan innehålla i noll eller flera ljud spår som är överlagrade i ett lämpligt behållar format. Detta är samlingen av alla dessa ljud spår.<br /> Mer information finns i [AudioTracks](#audiotracks). |
| **Namn**<br />Obligatorisk |Medie till gångens fil namn. <br /><br />Exempel: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Storlek**<br />Obligatorisk |Storlek på till gångs filen i byte. <br /><br />Exempel: `"Size": 32414631`|
| **Varaktighet**<br />Obligatorisk |Innehållets uppspelnings varaktighet. Mer information finns i [iso8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. <br /><br />Exempel: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Varje fysisk AssetFile kan innehålla i noll eller flera videor spårar överlagrad i ett lämpligt behållar format. Elementet **VideoTracks** representerar en samling med alla video spår.  

| Name | Beskrivning |
| --- | --- |
| **Identitet**<br /> Obligatorisk |Noll-baserat index för det här video spåret. **Obs:**  Detta **ID** är inte nödvändigt vis det TrackID som används i en MP4-fil. <br /><br />Exempel: `"Id": 1`|
| **FourCC**<br />Obligatorisk | Video-codec FourCC-kod som rapporteras av ffmpeg.  <br /><br />Exempel: `"FourCC": "avc1"`|
| **Profil** |H264,-profil (gäller endast H264,-codec).  <br /><br />Exempel: `"Profile": "High"` |
| **Nivå** |H264,-nivå (gäller endast H264,-codec).  <br /><br />Exempel: `"Level": "3.2"`|
| **Bredd**<br />Obligatorisk |Kodad video bredd i bild punkter.  <br /><br />Exempel: `"Width": "1280"`|
| **Höjd**<br />Obligatorisk |Kodad video höjd i bild punkter.  <br /><br />Exempel: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Obligatorisk|Täljare för bild förhållande i bild förhållande.  <br /><br />Exempel: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Obligatorisk |Nämnare för bild förhållande i bild.  <br /><br />Exempel: `"DisplayAspectRatioDenominator": 9.0`|
| **Bildfrekvens**<br />Obligatorisk |Uppmätt video bild Rute frekvens i. 3F-format.  <br /><br />Exempel: `"Framerate": 29.970`|
| **Hastigheten**<br />Obligatorisk |Genomsnittlig video bit hastighet i bitar per sekund, beräknat från AssetFile. Räknar endast den grundläggande nytto lasten i data strömmen och omfattar inte förpacknings omkostnaderna.  <br /><br />Exempel: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Obligatorisk |Genomsnittlig bit hastighet för mål för det här video spåret enligt begäran via kodnings inställningen i bitar per sekund. <br /><br />Exempel: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Varje fysisk AssetFile kan innehålla i noll eller flera ljud spår som är överlagrade i ett lämpligt behållar format. **AudioTracks** -elementet representerar en samling med alla dessa ljud spår.  

| Name  | Beskrivning |
| --- | --- |
| **Identitet**<br />Obligatorisk  |Noll-baserat index för det här ljud spåret. **Obs:**  Detta är inte nödvändigt vis TrackID som används i en MP4-fil.  <br /><br />Exempel: `"Id": 2`|
| **ADPCM**  |Ljud spårets codec-sträng.  <br /><br />Exempel: `"Codec": "aac"`|
| **Språk**|Exempel: `"Language": "eng"`|
| **Kanaler**<br />Obligatorisk|Antal ljud kanaler.  <br /><br />Exempel: `"Channels": 2`|
| **SamplingRate**<br />Obligatorisk |Ljud samplings frekvens i sampel/SEK eller Hz.  <br /><br />Exempel: `"SamplingRate": 48000`|
| **Hastigheten**<br />Obligatorisk |Genomsnittlig ljud bit hastighet i bitar per sekund, beräknat från AssetFile. Räknar endast den grundläggande nytto lasten i data strömmen och omfattar inte förpacknings omkostnaderna.  <br /><br />Exempel: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Exempel på JSON-schema

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md)
