---
title: Utför avancerad kodning genom att anpassa inställningarna för efter var | Microsoft Docs
description: Det här avsnittet visar hur du utför avancerad kodning genom att anpassa Media Encoder Standard uppgifts för inställningar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 9713a2ad10d1edc9a79d475ed58a99b3b24b6483
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696048"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Utför avancerad kodning genom att anpassa inställningarna för efter var

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

I det här avsnittet visas hur du anpassar Media Encoder Standard för inställningar. I avsnittet [kodning med Media Encoder Standard med anpassade för inställningar](media-services-custom-mes-presets-with-dotnet.md) visas hur du använder .net för att skapa en kodnings uppgift och ett jobb som kör uppgiften. När du har anpassat en för inställning anger du de anpassade för inställningarna till encoding-aktiviteten. 

Om du använder en XML-förinställning, se till att bevara element ordningen, som visas i XML-exempel nedan (till exempel KeyFrameInterval ska föregå SceneChangeDetection).

> [!NOTE] 
> Många av de avancerade Media Services v2-funktionerna i Media Encoder Standard är för närvarande inte tillgängliga i v3. Mer information finns i [migreringsguiden](../latest/migrate-v-2-v-3-migration-introduction.md).

## <a name="support-for-relative-sizes"></a>Stöd för relativa storlekar

När du skapar miniatyrer behöver du inte alltid ange utgångs bredd och höjd i bild punkter. Du kan ange dem i procent, i intervallet [1%,..., 100%].

### <a name="json-preset"></a>JSON-förinställning

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Skapa miniatyrbilder

I det här avsnittet visas hur du anpassar en för inställning som genererar miniatyrer. Den för inställning som definierats nedan innehåller information om hur du vill koda filen samt information som behövs för att generera miniatyrer. Du kan ta något av de förvalda för inställningarna för de [här](media-services-mes-presets-overview.md) avsnitten och lägga till kod som genererar miniatyrer.  

> [!NOTE]
> **SceneChangeDetection** -inställningen i följande för inställning kan bara anges till sant om du kodar till en video med en bit hastighet. Om du kodar till en video med flera bit hastigheter och anger **SceneChangeDetection** till true, returnerar kodaren ett fel.  
>
>

Information om schema finns i [det här](media-services-mes-schema.md) avsnittet.

Se till att gå igenom avsnittet [överväganden](#considerations) .

### <a name="json-preset"></a>JSON-förinställning

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>4500</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>4500</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
    <JpgImage Start="{Best}">
      <JpgLayers>
        <JpgLayer>
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Överväganden

Följande gäller:

* Om du använder explicita tidsstämplar för start/steg/intervall antas att Indatakällan är minst 1 minut.
* Jpg/png/BmpImage-element har Start-, steg-och intervall sträng-attribut – dessa kan tolkas som:

  * Ram nummer om de är icke-negativa heltal, till exempel "Start": "120",
  * I förhållande till käll varaktigheten om den uttrycks som% suffix, till exempel "Start": "15%" eller
  * Tidsstämpel om den uttrycks som HH: MM: SS... format, till exempel "Start": "00:01:00"

    Du kan blanda och matcha notationer på samma sätt som du.

    Dessutom stöder start också ett särskilt makro: {Best}, som försöker bestämma den första "intressanta" ramen för innehålls anteckningen: (steg och intervall ignoreras när start är inställt på {Best})
  * Standardvärden: Start: {Best}
* Utmatnings formatet måste anges explicit för varje bild format: jpg/png/BmpFormat. När den är tillgänglig matchar JpgVideo till JpgFormat och så vidare. OutputFormat introducerar ett nytt bild-codec-särskilt makro: {index}, som måste finnas (en gång och bara en gång) för bild format.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Trimma en video (Urklipp)
Det här avsnittet innehåller information om hur du ändrar för inställningarna för kodare till klipp eller trimmar indata-videon där indata är en så kallad mezzaninfil-fil eller fil på begäran. Kodaren kan också användas för att klippa eller rensa en till gång, som fångas in eller arkiveras från en Live-ström – informationen för detta finns i [den här bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Om du vill trimma dina videor kan du ta någon av de efter-och-inställningar som beskrivs i [det här](media-services-mes-presets-overview.md) avsnittet och ändra elementet **sources** (se nedan). Värdet för StartTime måste matcha de absoluta tidsstämplarna för Indataporten. Om den första bild rutan i Indataporten till exempel har en tidsstämpel på 12:00:10 000, ska StartTime vara minst 12:00:10 000 och större. I exemplet nedan antar vi att Indataporten har en start tids stämpling på noll. **Källor** ska placeras i början av förvalet.

### <a name="json-preset"></a><a id="json"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1000,
          "MaxBitrate": 1000,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 650,
          "MaxBitrate": 650,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 400,
          "MaxBitrate": 400,
          "BufferWindow": "00:00:05",
          "Width": 320,
          "Height": 180,
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

### <a name="xml-preset"></a>XML-förinställd
Om du vill trimma dina videor kan du ta någon av de efter-och-inställningar som beskrivs [här](media-services-mes-presets-overview.md) och ändra elementet **sources** (se nedan).

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
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

## <a name="create-an-overlay"></a><a id="overlay"></a>Skapa ett överlägg

Med Media Encoder Standard kan du täcka över en bild till en befintlig video. För närvarande stöds följande format: PNG, jpg, GIF och BMP. Den för inställning som definieras nedan är ett grundläggande exempel på ett video överlägg.

Förutom att definiera en för inställnings fil måste du också låta Media Services veta vilken fil i till gången som överläggs bilden är och vilken fil som är den käll video som du vill täcka över bilden till. Video filen måste vara den **primära** filen.

Om du använder .NET lägger du till följande två funktioner i .NET-exemplet som definierats i [det här](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnittet. Funktionen **UploadMediaFilesFromFolder** överför filer från en mapp (till exempel BigBuckBunny.mp4 och Image001.png) och anger att MP4-filen ska vara den primära filen i till gången. Funktionen **EncodeWithOverlay** använder den anpassade för inställnings filen som skickades till den (till exempel den för inställning som följer) för att skapa kodnings uppgiften.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Aktuella begränsningar:
>
> Inställningen övertäcknings ogenomskinlighet stöds inte.
>
> Käll video filen och bild filen för överlägget måste finnas i samma till gång, och video filen måste anges som primär fil i den här till gången.
>
>

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Infoga ett tyst ljud spår när indata inte har något ljud
Om du skickar ett indata till kodare som bara innehåller video, och inget ljud, innehåller utdata-till gången filer som bara innehåller video data. Vissa spelare kanske inte kan hantera sådana utgående strömmar. Du kan använda den här inställningen för att tvinga kodare att lägga till ett tyst ljud spår till utdata i det scenariot.

Om du vill tvinga kodare att skapa en till gång som innehåller ett tyst ljud spår när indata inte har något ljud, anger du värdet "InsertSilenceIfNoAudio".

Du kan ta något av de förvalda för inställningarna för efterföljande som beskrivs i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

### <a name="json-preset"></a>JSON-förinställning

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Inaktivera automatiska inflätning
Kunderna behöver inte göra något om de som det sammanflätnings innehållet automatiskt ska vara sammanflätade. När den automatiska indelningen är aktive ras (standard) används den automatiska identifieringen av sammanflätade ramar och bara de som är sammanflätade.

Du kan stänga av den automatiska avflätningen. Det här alternativet rekommenderas inte.

### <a name="json-preset"></a>JSON-förinställning

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>För inställningar för endast ljud
I det här avsnittet visas två för hands inställningar för endast ljud: AAC Audio och AAC Audio med bra kvalitet.

### <a name="aac-audio"></a>AAC-ljud

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>Ljud av AAC-bra kvalitet

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Sammanfoga två eller fler videofiler

I följande exempel visas hur du kan generera en för inställning för att sammanfoga två eller fler videofiler. Det vanligaste scenariot är när du vill lägga till ett sidhuvud eller en släpvagn i huvud videon. Den avsedda användningen är när videofilerna som redige ras tillsammans delar egenskaper (video upplösning, bild Rute hastighet, antal ljud spår osv.). Du bör inte vara försiktig med att blanda videor med olika bild Rute hastigheter eller med olika antal ljud spår.

>[!NOTE]
>Den aktuella designen av sammanfognings funktionen förväntar sig att insamlings videoklippen är konsekventa när det gäller upplösning, bild hastighet osv. 

### <a name="requirements-and-considerations"></a>Krav och överväganden

* Inmatade videor får bara ha ett ljud spår.
* Inmatade videor bör ha samma bild Rute hastighet.
* Du måste överföra dina videor till separata till gångar och ange videor som primär fil i varje till gång.
* Du måste veta hur länge dina videor är.
* De förinställda exemplen nedan förutsätter att alla inmatade videor börjar med en tidsstämpel på noll. Du måste ändra StartTime-värdena om videoklippen har olika start tids stämpling, som vanligt vis är fallet med Live-Arkiv.
* JSON-förvalet gör explicita referenser till AssetID-värdena för ingångs till gångarna.
* Exempel koden förutsätter att JSON-förvalet har sparats i en lokal fil, t. ex. "C:\supportFiles\preset.jspå". Det förutsätter också att två till gångar har skapats genom att överföra två videofiler och att du vet de resulterande AssetID-värdena.
* Kodfragmentet och JSON-förvalet visar ett exempel på sammanfogning av två videofiler. Du kan utöka den till fler än två videor genom att:

  1. Anropar uppgift. InputAssets. Add () upprepas flera gånger för att lägga till fler videor i ordning.
  2. Gör motsvarande ändringar i elementet "sources" i JSON, genom att lägga till fler poster i samma ordning.

### <a name="net-code"></a>.NET-kod

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON-förinställning

Uppdatera din anpassade för inställning med ID: n för de till gångar som du vill sammanfoga och med lämpligt tids segment för varje video.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Beskär videoklipp med Media Encoder Standard
Se avsnittet [Beskär videor med Media Encoder Standard](media-services-crop-video.md) .

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Infoga ett video spår om inmatningen saknar video

Om du skickar ett indata till kodare som bara innehåller ljud, och ingen video, innehåller utdata-till gången filer som bara innehåller ljud data. Vissa spelare, inklusive Azure Media Player (se [detta](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) kanske inte kan hantera sådana strömmar. Du kan använda den här inställningen för att tvinga kodare att lägga till ett monokromt video spår till utdata i det scenariot.

> [!NOTE]
> Att tvinga kodaren att infoga ett utdata video spår ökar storleken på utmatnings till gången och därmed kostnaden för kodnings aktiviteten. Du bör köra tester för att kontrol lera att den resulterande ökningen bara har en liten inverkan på dina månatliga avgifter.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Infoga endast video med lägsta bit hastighet

Anta att du använder en kodning för flera bit hastigheter, till exempel ["H264, Multiple bit hastighet"](media-services-mes-preset-h264-multiple-bitrate-720p.md) för att koda hela indata-katalogen för strömning, som innehåller en blandning av videofiler och ljudfiler. I det här scenariot, när indatan inte har någon video, kanske du vill tvinga kodare att infoga en monokrom video spår med enbart den lägsta bit hastigheten, i stället för att infoga video vid varje bit hastighet. För att uppnå detta måste du använda flaggan **InsertBlackIfNoVideoBottomLayerOnly** .

Du kan ta något av de förvalda för inställningarna för efterföljande som beskrivs i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

#### <a name="json-preset"></a>JSON-förinställning

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-förinställd

När du använder XML använder du Condition = "InsertBlackIfNoVideoBottomLayerOnly" som ett attribut till **H264Video** -elementet och Condition = "InsertSilenceIfNoAudio" som ett attribut till **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Infoga video på alla bit hastigheter för utdata
Anta att du använder en kodning för flera bit hastigheter, till exempel ["H264, Multiple bit hastighet 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) för att koda hela indata-katalogen för strömning, som innehåller en blandning av videofiler och ljudfiler. I det här scenariot, när indatan inte har någon video, kanske du vill tvinga kodaren att infoga ett monokromt video spår på alla bit hastigheter. Detta säkerställer att dina utmatnings till gångar är homogena i förhållande till antalet video spår och ljud spår. För att uppnå detta måste du ange flaggan "InsertBlackIfNoVideo".

Du kan ta något av de förvalda för inställningarna för efterföljande som beskrivs i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

#### <a name="json-preset"></a>JSON-förinställning

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-förinställd

När du använder XML använder du Condition = "InsertBlackIfNoVideo" som ett attribut till **H264Video** -elementet och Condition = "InsertSilenceIfNoAudio" som ett attribut till **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Rotera en video
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation efter vinklar på 0/90/180/270. Standard beteendet är "Auto", där det försöker att identifiera rotations-metadata i den inkommande video filen och kompensera för den. Ta med följande **käll** element till en av de förval som definieras i [det här](media-services-mes-presets-overview.md) avsnittet:

### <a name="json-preset"></a>JSON-förinställning

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML-förinställd

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Se även [det här](media-services-mes-schema.md#PreserveResolutionAfterRotation) avsnittet för mer information om hur kodaren tolkar inställningarna för bredd och höjd i förvalet, när rotations kompensationen utlöses.

Du kan använda värdet "0" för att ange att kodaren ska ignorera metadata för rotering, om den finns, i indata-videon.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över Media Services kodning](media-services-encode-asset.md)
