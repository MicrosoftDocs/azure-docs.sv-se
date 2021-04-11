---
title: Avancerad kodning med Media Encoder Premium Workflow | Microsoft Docs
description: Lär dig hur du kodar med Media Encoder Premium Workflow. Kod exempel skrivs i C# och använder Media Services SDK för .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 8d39d54498872201afe2f705da1fc5654d8befb2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644994"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avancerad kodning med Media Encoder Premium Workflow

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Encoder Premium Workflow medie processor som diskuteras i den här artikeln är inte tillgänglig i Kina.
>
>

## <a name="overview"></a>Översikt
Microsoft Azure Media Services introducerar **Media Encoder Premium Workflow** medie processorn. I den här processorn finns funktioner för att koda för dina Premium-arbets flöden på begäran.

Följande avsnitt innehåller information om **Media Encoder Premium Workflow**:

* [Format som stöds av Media Encoder Premium Workflow](./media-services-encode-asset.md) – beskriver de fil format och codecenheter som stöds av **Media Encoder Premium Workflow**.
* [Översikt och jämförelse av Azure on-demand Media encoders](media-services-encode-asset.md) jämför kodnings funktionerna i **Media Encoder Premium Workflow** och **Media Encoder Standard**.

Den här artikeln visar hur du kodar med **Media Encoder Premium Workflow** med hjälp av .net.

Kodning av aktiviteter för **Media Encoder Premium Workflow** kräver en separat konfigurations fil som kallas för en arbets flödes fil. De här filerna har fil namns tillägget. Workflow och skapas med hjälp av [arbetsflödesdesigner](media-services-workflow-designer.md) -verktyget.

Du kan också hämta standard arbets flödes filerna [här](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md
). Mappen innehåller också en beskrivning av de här filerna.

Arbets flödes filen måste överföras till ditt Media Services-konto som en till gång, och den här till gången ska skickas till encoding-aktiviteten.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Kodnings exempel

Följande exempel visar hur du kodar med **Media Encoder Premium Workflow**.

Följande steg utförs:

1. Skapa en till gång och överför en arbets flödes fil.
2. Skapa en till gång och överför en käll medie fil.
3. Hämta medie processorn "Media Encoder Premium Workflow".
4. Skapa ett jobb och en uppgift.

    I de flesta fall är konfigurations strängen för aktiviteten tom (som i följande exempel). Det finns vissa avancerade scenarier (som kräver att du anger körnings egenskaper dynamiskt), vilket innebär att du anger en XML-sträng till encoding-aktiviteten. Exempel på sådana scenarier är: skapa ett överlägg, parallellt eller sekventiellt medie häftning, under textning.
5. Lägg till två inmatade till gångar i uppgiften.

   1. 1st – arbets flödets till gång.
   2. andra – video till gången.

      >[!NOTE]
      >Arbets flödets till gång måste läggas till i aktiviteten innan medie till gången.
      Konfigurations strängen för den här aktiviteten måste vara tom.
   
6. Skicka kodnings jobbet.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
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

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
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

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
