---
title: Bortredigering-ansikten med Azure-medieanalys | Microsoft Docs
description: Azure Media Redactor är en Azure-medieanalys medie processor som erbjuder skalbara ansikts bortredigering i molnet. Den här artikeln visar hur du kan redigera med Azure Media Analytics.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d416810f6a39fb36bfa3c5225301fe87fdd128c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013473"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Bortredigering-ansikten med Azure-medieanalys

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

**Azure Media Redactor** är en [Azure-medieanalys](./legacy-components.md) medie processor (MP) som erbjuder skalbara ansikts bortredigering i molnet. Med ansikts bortredigering kan du ändra videon så att det blir oskarpa ytor på valda individer. Du kanske vill använda ansikts bortredigering i offentliga säkerhets-och nyhets medie scenarier. Några minuter av tagningar som innehåller flera ansikten kan ta timmar till bortredigering manuellt, men med den här tjänsten krävs bara några få enkla steg.

Den här artikeln innehåller information om **Azure Media Redactor** och visar hur du använder det med Media Services SDK för .net.

## <a name="face-redaction-modes"></a>Lägen för ansikts bortredigering

Ansikts bortredigering fungerar genom att identifiera ansikten i varje bild ruta i videon och spåra objektet ansikte både framåt och bakåt i tiden, så att samma person kan vara suddig från andra vinklar. Den automatiserade bortredigering-processen är komplicerad och producerar inte alltid 100% av önskade utdata, av den här anledningen Medieanalys ger dig ett par olika sätt att modifiera den slutliga utmatningen.

Förutom ett helt automatiskt läge, finns det ett två-pass-arbetsflöde, som gör att valet/de-valet av hittade ansikten via en lista med ID: n. Om du vill göra godtyckliga justeringar av varje ram-nivå används en metadatafil i JSON-format. Det här arbets flödet är uppdelat i **analys** -och **bortredigering** -läge. Du kan kombinera de två lägena i ett enda pass som kör båda uppgifterna i ett jobb. Det här läget kallas **kombinerat**.

   > [!NOTE]
   > Medie processorn för ansikts detektor har föråldrats från och med juni 2020, [Azure Media Services äldre komponenter](./legacy-components.md). Överväg att använda Azure Media Services v3-API.

### <a name="combined-mode"></a>Kombinerat läge

Detta skapar en förredigerad MP4 automatiskt utan manuella indatatyper.

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |foo.bar |Video i WMV-, MOV-eller MP4-format |
| Konfiguration av indatamängd |Inställning av jobb konfiguration |{' version ': ' 1.0 ', ' alternativ ': {' läge ': ' kombinerat '}} |
| Mata ut till gång |foo_redacted.mp4 |Video med suddigt använt |

### <a name="analyze-mode"></a>Analysera läge

Det **analyserande** passet i det två-pass-arbets flödet tar en video indata och skapar en JSON-fil med ansikts platser och jpg-bilder av varje identifierad yta.

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |foo.bar |Video i WMV-, MPV-eller MP4-format |
| Konfiguration av indatamängd |Inställning av jobb konfiguration |{' version ': ' 1.0 ', ' alternativ ': {' läge ': ' analysera '}} |
| Mata ut till gång |foo_annotations.jspå |Antecknings data för ansikts platser i JSON-format. Detta kan redige ras av användaren för att ändra de oskarpa avgränsnings rutorna. Se exemplet nedan. |
| Mata ut till gång |foo_thumb% 06d.jpg [foo_thumb000001.jpg foo_thumb000002.jpg] |En beskurna jpg för varje identifierad ansikte, där talet anger labelId för ansikte |

#### <a name="output-example"></a>Exempel på utdata

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-mode"></a>Bortredigering-läge

Det andra steget i arbets flödet tar ett större antal indata som måste kombineras till en enda till gång.

Detta inkluderar en lista med ID: n som är suddig, den ursprungliga videon och antecknings-JSON. I det här läget används anteckningarna för att använda oskärpa på ingångs videon.

Den ursprungliga videon ingår inte i resultatet från analys steget. Videon måste överföras till indata till gången för uppgiften bortredigering och väljs som primär fil.

| Fas | Filnamn | Kommentarer |
| --- | --- | --- |
| Inmatad till gång |foo.bar |Video i WMV-, MPV-eller MP4-format. Samma video som i steg 1. |
| Inmatad till gång |foo_annotations.jspå |anteckningarnas metadatafil från fas ett, med valfria ändringar. |
| Inmatad till gång |foo_IDList.txt (valfritt) |Valfri ny blankstegsavgränsad lista över ansikts-ID: n till bortredigering. Om detta lämnas tomt oskarps alla ansikten. |
| Konfiguration av indatamängd |Inställning av jobb konfiguration |{' version ': ' 1.0 ', ' alternativ ': {' läge ': ' bortredigering '}} |
| Mata ut till gång |foo_redacted.mp4 |Video med oskärpa som används baserat på anteckningar |

#### <a name="example-output"></a>Exempel på utdata

Detta är resultatet från en IDList med ett ID valt.

Exempel foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Suddiga typer

I det **kombinerade** eller **bortredigering** -läget finns det fem olika suddiga lägen som du kan välja mellan via JSON-inkonfigurationen: **låg**, **mellan,** **hög**, **ruta** och **svart**. Som standard **används** med.

Du kan hitta exempel på suddiga typer nedan.

### <a name="example-json"></a>Exempel-JSON

```json
{
    'version':'1.0',
    'options': {
        'Mode': 'Combined',
        'BlurType': 'High'
    }
}
```

#### <a name="low"></a>Låg

![Låg](./media/media-services-face-redaction/blur1.png)

#### <a name="med"></a>Notifiera

![Notifiera](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Högt

![Högt](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Svarta

![Svart](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

För borttagnings-MP: en finns hög precisions plats för identifiering och spårning som kan identifiera upp till 64 mänskliga ansikten i en video bild ruta. Front ansikten ger bäst resultat, medan sidan ansikten och små ytor (mindre än eller lika med 24x24 bild punkter) är utmanande.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du:

1. Skapa en till gång och överför en mediefil till till gången.
2. Skapa ett jobb med en ansikts bortredigering-uppgift baserad på en konfigurations fil som innehåller följande JSON-förval: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Hämta JSON-filerna för utdata.

### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md).

#### <a name="example"></a>Exempel

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                error.Code,
                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                       mediaProcessorName));

            return processor;
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
    }
}
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar

[Översikt över Azure Media Services Analytics](./legacy-components.md)

[Azure-medieanalys demonstrationer](http://amslabs.azurewebsites.net/demos/Analytics.html)
