---
title: Hantera Media Services v3-till gångar över flera lagrings konton | Microsoft Docs
description: De här artiklarna ger vägledning om hur du hanterar Media Services v3-till gångar över flera lagrings konton.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b336bfdd6146c92473d5998187cbd4148b26a48c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282233"
---
# <a name="managing-media-services-v3-assets-across-multiple-storage-accounts"></a>Hantera Media Services v3-till gångar över flera lagrings konton  

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du kan bifoga flera lagrings konton till ett enda Media Services-konto. Möjligheten att ansluta flera lagrings konton till ett Media Services-konto ger följande fördelar:

* Belastnings utjämning av till gångar över flera lagrings konton.
* Skalnings Media Services för stora mängder innehålls bearbetning (som för närvarande ett enda lagrings konto har en Max gräns på 500 TB).

Den här artikeln visar hur du kopplar flera lagrings konton till ett Media Services-konto med hjälp av [Azure Resource Manager-API: er](/rest/api/media/operations/azure-media-services-rest-api-reference) och [PowerShell](/powershell/module/az.media). Det visar också hur du anger olika lagrings konton när du skapar till gångar med hjälp av Media Services SDK.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="considerations"></a>Överväganden

När du kopplar flera lagrings konton till ditt Media Services-konto gäller följande saker:

* Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar att du använder lagringskonton på samma plats som Media Services-kontot.
* När ett lagrings konto är kopplat till det angivna Media Services kontot går det inte att koppla från.
* Primärt lagrings konto är det som angavs när Media Servicess konto skapas. För närvarande kan du inte ändra standard lagrings kontot.
* Om du vill lägga till ett cool Storage-konto till AMS-kontot måste lagrings kontot vara av typen blob och vara inställt på icke-primär.

Andra överväganden:

Media Services använder värdet för egenskapen **IAssetFile.name** när du skapar URL: er för strömmande innehåll (till exempel http://{WAMSAccount}. ORIGIN. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Därför är procent kodning inte tillåtet. Värdet för namn egenskapen får inte ha något av följande [%-encoding-reserverade tecken](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Det kan också finnas en "." för fil namns tillägget.

## <a name="to-attach-storage-accounts"></a>För att ansluta lagrings konton  

Om du vill koppla lagrings konton till ditt AMS-konto använder du [Azure Resource Manager API: er](/rest/api/media/operations/azure-media-services-rest-api-reference) och [PowerShell](/powershell/module/az.media), som du ser i följande exempel:

```azurepowershell
$regionName = "West US"
$subscriptionId = " xxxxxxxx-xxxx-xxxx-xxxx- xxxxxxxxxxxx "
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccount1Name = "skystorage1"
$storageAccount2Name = "skystorage2"
$storageAccount1Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount1Name"
$storageAccount2Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount2Name"
$storageAccount1 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount1Id -IsPrimary
$storageAccount2 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount2Id
$storageAccounts = @($storageAccount1, $storageAccount2)

Set-AzMediaService -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccounts $storageAccounts
```

### <a name="support-for-cool-storage"></a>Stöd för cool Storage

För närvarande måste lagrings kontot vara en Blob-typ och vara inställt på icke-primär om du vill lägga till ett cool Storage-konto till AMS-kontot.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>Hantera Media Services till gångar över flera lagrings konton

I följande kod används den senaste Media Services SDK: n för att utföra följande uppgifter:

1. Visa alla lagrings konton som är associerade med det angivna Media Services kontot.
2. Hämta namnet på standard lagrings kontot.
3. Skapa en ny till gång på standard lagrings kontot.
4. Skapa en utmatnings till gång för kodnings jobbet i det angivna lagrings kontot.

```cs
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace MultipleStorageAccounts
{
    class Program
    {
        // Location of the media file that you want to encode. 
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Display the storage accounts associated with 
            // the specified Media Services account:
            foreach (var sa in _context.StorageAccounts)
                Console.WriteLine(sa.Name);

            // Retrieve the name of the default storage account.
            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
            Console.WriteLine("Name: {0}", defaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

            // Retrieve the name of a storage account that is not the default one.
            var notDefaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
            Console.WriteLine("Name: {0}", notDefaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", notDefaultStorageName.IsDefault);

            // Create the original asset in the default storage account.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None,
                defaultStorageName.Name, _singleInputFilePath);
            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

            // Create an output asset of the encoding job in the other storage account.
            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStorageName.Name, _singleInputFilePath);
            if (outputAsset != null)
                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

        }

        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);

            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset", storageName,
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return null;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];

            return outputAsset;
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
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
                    Console.WriteLine("An error occurred in {0}", job.Id);
                    break;
                default:
                    break;
            }
        }

        static IJob GetJob(string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
    }
}
```
