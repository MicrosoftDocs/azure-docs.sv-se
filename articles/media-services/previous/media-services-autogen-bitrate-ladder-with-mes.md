---
title: Använd Media Encoder Standard för att automatiskt skapa en bit hastighets steg-Azure | Microsoft Docs
description: I det här avsnittet visas hur du använder Media Encoder Standard (en gång) för att automatiskt generera en bit hastighets steg baserat på inlösningen och bit hastigheten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6ea28d61cc142c3191d591721b92e08d651c7ed5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014408"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Använd Media Encoder Standard för att automatiskt generera en bit hastighets steg

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder Media Encoder Standard (en gång) för att automatiskt generera en bit hastighets steg (bit upplösnings par) baserat på inlösningen och bit hastigheten. Den automatiskt genererade inställningen kommer aldrig att överskrida den angivna upplösningen och bit hastigheten. Om indata t. ex. är 720p vid 3 Mbit/s, är utmatningen översatt till bästa och kommer att starta med lägre taxa än 3 Mbit/s.

### <a name="encoding-for-streaming-only"></a>Kodning för endast strömning

Om avsikten är att koda din käll video endast för strömning, bör du använda för inställningen "anpassningsbar strömning" när du skapar en kodnings aktivitet. När du använder den **anpassningsbara direkt uppspelnings** för hands inställningen kommer en stege-kodare att använda en bit hastighets steg. Du kommer dock inte att kunna kontrol lera kodnings kostnaderna eftersom tjänsten fastställer hur många skikt som ska användas och i vilken upplösning. Du kan se exempel på utmatnings lager som har producerats av ingångs punkt som ett resultat av kodning med den **anpassningsbara streaming** -förinställningen i slutet av artikeln. Utmatnings till gången innehåller MP4-filer där ljud och video inte är överlagrade.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodning för strömning och progressiv nedladdning

Om avsikten är att koda din käll video för strömning och skapa MP4-filer för progressiv nedladdning bör du använda för inställningen "anpassa flera bit hastigheter för att skapa en kodning" när du skapar en kodnings aktivitet. När du använder den anpassade MP4-förvalet med **flera bit** hastigheter, använder en underordnad-kodare samma kodnings logik som ovan, men nu kommer den resulterande till gången att innehålla MP4-filer där ljud och video är överlagrade. Du kan använda någon av de här MP4-filerna (till exempel den högsta bit versionen) som en progressiv nedladdnings fil.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Koda med Media Services .NET SDK

I följande kod exempel används Media Services .NET SDK för att utföra följande uppgifter:

- Skapa ett kodnings jobb.
- Hämta en referens till Media Encoder Standard Encoder.
- Lägg till en kodnings uppgift i jobbet och ange att du vill använda den **anpassade** för hands inställningen. 
- Skapa en utgående till gång som innehåller den kodade till gången.
- Lägg till en händelse hanterare för att kontrol lera jobb förloppet.
- Skicka in jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

        // Field for service context.
        private static CloudMediaContext _context = null;

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

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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

## <a name="output"></a><a id="output"></a>Utdata

I det här avsnittet visas tre exempel på utmatnings lager som har skapats av ingångs punkt som ett resultat av kodning med den **anpassade** för hands inställningen 

### <a name="example-1"></a>Exempel 1
Källa med höjden "1080" och RAM hastighet "29,970" producerar 6 video lager:

|Skikt|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjden "720" och RAM hastighet "23,970" producerar 5 video lager:

|Skikt|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjden "360" och RAM hastighet "29,970" producerar 3 video lager:

|Skikt|Höjd|Bredd|Bit hastighet (kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över Media Services kodning](media-services-encode-asset.md)

