---
title: Så här genererar du miniatyrer med Media Encoder Standard med .NET
description: Det här avsnittet visar hur du använder .NET för att koda en till gång och skapa miniatyrer samtidigt med hjälp av Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0159a706bd5f5229a12ab52bc92d240f01a2ed11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014051"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Så här genererar du miniatyrer med Media Encoder Standard med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Du kan använda Media Encoder Standard för att generera en eller flera miniatyrer från indata-videon i bild fil formaten [JPEG](https://en.wikipedia.org/wiki/JPEG), [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics)eller [BMP](https://en.wikipedia.org/wiki/BMP_file_format) . Du kan skicka uppgifter som bara producerar bilder, eller så kan du kombinera miniatyrer med kodning. Den här artikeln innehåller några exempel på XML-och JSON-miniatyrer för sådana scenarier. I slutet av artikeln finns det en [exempel kod](#code_sample) som visar hur du använder Media Services .NET SDK för att utföra kodnings uppgiften.

Om du vill ha mer information om elementen som används i exempel för för inställningar bör du granska [Media Encoder Standard schema](media-services-mes-schema.md).

Se till att gå igenom avsnittet [överväganden](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) .
    
## <a name="example-of-a-single-png-file-preset"></a>Exempel på en "enskild PNG-fil"-förinställd

Följande JSON-och XML-förinställning kan användas för att skapa en PNG-fil med en enda utdata från de första sekunderna i indata-videon, där kodaren gör ett bästa försök att hitta en "intressant" ram. Observera att måtten för utgående bilder har angetts till 100%, vilket innebär att dessa matchar måtten för inmatnings videon. Observera också hur inställningen "format" i "utdata" krävs för att matcha användningen av "PngLayers" i avsnittet "codecs". 

### <a name="json-preset"></a>JSON-förinställning

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
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
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exempel på en "serie med JPEG-bilder"-förval

Följande JSON-och XML-förinställning kan användas för att skapa en uppsättning 10 bilder vid tidsstämplar på 5%, 15%,..., 95% av tids linjen för indata, där bild storleken anges som en fjärdedel av indata-videon.

### <a name="json-preset"></a>JSON-förinställning

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
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
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exempel på en för inställning för "en bild vid en speciell tidsstämpel"

Följande JSON-och XML-förinställning kan användas för att skapa en JPEG-bild med det 30 andra märket av indata-videon. Den här inställningen förväntar sig att Indataporten ska vara mer än 30 sekunder lång (annars Miss lyckas jobbet).

### <a name="json-preset"></a>JSON-förinställning

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
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
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exempel på en inställning för "miniatyrer med olika upplösningar"

Följande för inställningar kan användas för att generera miniatyrer med olika upplösningar i en aktivitet. I exemplet i positionerna 5%, 15%,..., 95% av ingångs tids linjen, genererar kodaren två bilder – en till 100% av video upplösningen och den andra vid 50%.

Observera att {resolution}-makro används i fil namnet; den anger att kodaren ska använda den bredd och höjd som du angav i avsnittet kodning i förvalet när du genererar fil namnet för de utgående bilderna. Detta gör det också lättare att skilja mellan olika avbildningar

### <a name="json-preset"></a>JSON-förinställning

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-förinställd
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exempel på att skapa en miniatyr vid kodning

Även om alla ovanstående exempel har diskuterat hur du kan skicka en kodnings uppgift som bara genererar bilder, kan du också kombinera video-/ljud kodning med en miniatyr bild. Följande JSON-och XML-förinställning instruerar **Media Encoder Standard** att generera en miniatyr under kodning.

### <a name="json-preset"></a><a id="json"></a>JSON-förinställning
Information om schema finns i [den här](./media-services-mes-schema.md) artikeln.

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
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
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
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML-förinställd
Information om schema finns i [den här](./media-services-mes-schema.md) artikeln.

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
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Koda video och generera miniatyr med .NET

I följande kod exempel används Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodnings jobb.
* Hämta en referens till Media Encoder Standard Encoder.
* Läs in den förinställda [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) eller [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) som innehåller kodnings för inställningen samt information som behövs för att generera miniatyrer. Du kan spara den här  [XML-](media-services-dotnet-generate-thumbnail-with-mes.md#xml) filen eller [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) -filen i en fil och använda följande kod för att läsa in filen.

    ```csharp
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

* Lägg till en enda kodnings uppgift i jobbet. 
* Ange den inmatade till gång som ska kodas.
* Skapa en utgående till gång som innehåller den kodade till gången.
* Lägg till en händelse hanterare för att kontrol lera jobb förloppet.
* Skicka in jobbet.

I artikeln [Media Services utveckling med .net](media-services-dotnet-how-to-use.md) finns anvisningar om hur du konfigurerar din utvecklings miljö.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
                    TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="considerations"></a>Överväganden
Följande gäller:

* Om du använder explicita tidsstämplar för start/steg/intervall antas att Indatakällan är minst 1 minut.
* Jpg/png/BmpImage-element har Start-, steg-och intervall sträng-attribut – dessa kan tolkas som:
  
  * Ram nummer om de är icke-negativa heltal, till exempel "Start": "120",
  * I förhållande till käll varaktigheten om den uttrycks som% suffix, till exempel "Start": "15%" eller
  * Tidsstämpel om den uttrycks som HH: MM: SS... . Till exempel "Start": "00:01:00"
    
    Du kan blanda och matcha notationer på samma sätt som du.
    
    Dessutom stöder start också ett särskilt makro: {Best}, som försöker bestämma den första "intressanta" ramen för innehålls anteckningen: (steg och intervall ignoreras när start är inställt på {Best})
  * Standardvärden: Start: {Best}
* Utmatnings formatet måste anges explicit för varje bild format: jpg/png/BmpFormat. När den är tillgänglig matchar JpgVideo till JpgFormat och så vidare. OutputFormat introducerar ett nytt bild-codec-särskilt makro: {index}, som måste finnas (en gång och bara en gång) för bild format.

## <a name="next-steps"></a>Nästa steg

Du kan kontrol lera [jobb förloppet](media-services-check-job-progress.md) medan kodnings jobbet väntar.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över Media Services kodning](media-services-encode-asset.md)