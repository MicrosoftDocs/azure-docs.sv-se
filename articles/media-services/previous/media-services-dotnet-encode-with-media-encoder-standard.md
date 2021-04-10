---
title: Koda en till gång med Media Encoder Standard med hjälp av .NET | Microsoft Docs
description: Den här artikeln visar hur du använder .NET för att koda en till gång med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014102"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Koda en till gång med Media Encoder Standard med hjälp av .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Kodnings jobb är en av de vanligaste bearbetnings åtgärderna i Media Services. Du skapar kodnings jobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda den inbyggda medie kodare Media Services. Du kan också använda en kodare som tillhandahålls av en Media Services-partner. kodare från tredje part är tillgängliga via Azure Marketplace. 

Den här artikeln visar hur du använder .NET för att koda dina till gångar med Media Encoder Standard (marknads status). Media Encoder Standard konfigureras med hjälp av någon av de förinställda kodare som beskrivs [här](./media-services-mes-presets-overview.md).

Vi rekommenderar att alltid koda källfilerna till en MP4-uppsättning med anpassad bit hastighet och sedan konvertera uppsättningen till önskat format med hjälp av den [dynamiska packningen](media-services-dynamic-packaging-overview.md). 

Om din utgående till gång är krypterad, måste du konfigurera till gångs leverans princip. Mer information finns i [Konfigurera till gångs leverans princip](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> Du skapar en utdatafil med ett namn som innehåller de första 32 tecknen i indatafilens namn. Namnet baseras på vad som anges i den förinställda filen. Till exempel "FileName": "{basename} _ {index} {Extension}". {Basename} ersätts med de första 32 tecknen i indatafilens namn.
> 
> 

### <a name="mes-formats"></a>Status för status
[Format och codecenheter](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-förinställningar
Media Encoder Standard konfigureras med hjälp av någon av de förinställda kodare som beskrivs [här](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Metadata för indata och utdata
När du kodar en inmatnings till gång (eller till gångar) med hjälp av aktiviteter får du en utmatnings till gång när du har slutfört åtgärden. Utmatnings till gången innehåller video, ljud, miniatyrer, manifest osv. baserat på den kodnings inställning som du använder.

Till gången till utdata innehåller också en fil med metadata om inmatnings till gången. Namnet på XML-filen med metadata har följande format: <asset_id # C1_metadata.xml (till exempel 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), där <asset_id> är AssetId-värdet för indata till gången. Schemat för denna XML för indata-metadata beskrivs [här](media-services-input-metadata-schema.md).

Till gången till utdata innehåller också en fil med metadata om utmatnings till gången. Namnet på XML-filen med metadata har följande format: <source_file_name # C1_manifest.xml (till exempel BigBuckBunny_manifest.xml). Schemat för denna XML för utdata-metadata beskrivs [här](media-services-output-metadata-schema.md).

Om du vill undersöka någon av de två metadatafiler kan du skapa en SAS-lokaliserare och ladda ned filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-lokaliserare och laddar ned en fil med hjälp av Media Services .NET SDK-tillägg.

## <a name="download-sample"></a>Ladda ned exempel
Du kan hämta och köra ett exempel som visar hur du kodar med hjälp av [här](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>.NET-exempel kod

I följande kod exempel används Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodnings jobb.
* Hämta en referens till Media Encoder Standard Encoder.
* Ange om du vill använda för hands inställningen för [anpassad direkt uppspelning](media-services-autogen-bitrate-ladder-with-mes.md) . 
* Lägg till en enda kodnings uppgift i jobbet. 
* Ange den inmatade till gång som ska kodas.
* Skapa en utgående till gång som innehåller den kodade till gången.
* Lägg till en händelse hanterare för att kontrol lera jobb förloppet.
* Skicka in jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Avancerade kodnings funktioner som du kan utforska
* [Så här genererar du miniatyrer](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Skapa miniatyrer under kodning](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Beskär videor under kodning](media-services-crop-video.md)
* [Anpassa kodnings för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Överlagra eller lägga till en video med en bild](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Så [här skapar du en miniatyr med Media Encoder Standard med .net](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Översikt över Media Services kodning](media-services-encode-asset.md)