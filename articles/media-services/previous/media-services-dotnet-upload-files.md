---
title: Ladda upp filer till ett Media Services-konto med hjälp av .NET | Microsoft Docs
description: Lär dig hur du hämtar medie innehåll i Media Services genom att skapa och ladda upp till gångar med .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013739"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Ladda upp filer till ett Media Services-konto med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

I Media Services överför du (eller för in) dina digitala filer till en tillgång. **Till gångs** enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer.)  När filerna har laddats upp lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Filerna i tillgången kallas **Tillgångsfiler**. **AssetFile** -instansen och den faktiska medie filen är två distinkta objekt. AssetFile-instansen innehåller metadata om medie filen, medan medie filen innehåller det faktiska medie innehållet.

## <a name="considerations"></a>Överväganden

Följande gäller:
 
 * Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömmande innehåll (till exempel http://{AMSAccount}. ORIGIN. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Därför är procent kodning inte tillåtet. Värdet för **namn** egenskapen får inte ha något av följande [%-encoding-reserverade tecken](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Det kan också finnas en "." för fil namns tillägget.
* Längden på namnet får inte vara längre än 260 tecken.
* Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.
* Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

När du skapar till gångar kan du ange följande krypterings alternativ:

* **Ingen** – Ingen kryptering används. Detta är standardvärdet. När du använder det här alternativet skyddas inte innehållet i överföring eller i vila i lagring.
  Använd det här alternativet om du planerar att leverera en MP4 med progressiv nedladdning: 
* **CommonEncryption** – Använd det här alternativet om du överför innehåll som redan har krypterats och skyddas med common Encryption eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
* **EnvelopeEncrypted** – Använd det här alternativet om du överför HLS som krypteras med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.
* **StorageEncrypted** – krypterar ditt rensade innehåll lokalt med AES-256-bitars kryptering och laddar sedan upp det till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
  
    Media Services tillhandahåller kryptering på disk lagring för dina till gångar, inte över-kabeln som Digital Rights Manager (DRM).
  
    Om tillgången är lagringskrypterad måste du konfigurera principen för tillgångsleverans. Mer information finns i [Konfigurera till gångs leverans princip](media-services-dotnet-configure-asset-delivery-policy.md).

Om du anger att till gången ska krypteras med ett **CommonEncrypted** -alternativ eller ett **EnvelopeEncrypted** -alternativ måste du associera din till gång med en **ContentKey**. Mer information finns i [så här skapar du en ContentKey](media-services-dotnet-create-contentkey.md). 

Om du anger att din till gång ska krypteras med ett **StorageEncrypted** -alternativ, skapar Media Services SDK för .net en **StorageEncrypted** - **ContentKey** för din till gång.

Den här artikeln visar hur du använder Media Services .NET SDK samt Media Services tillägg för .NET SDK för att ladda upp filer till en Media Services till gång.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Ladda upp en enstaka fil med Media Services .NET SDK

I följande kod används .NET för att ladda upp en enda fil. Access policy och Locator skapas och förstörs av uppladdnings funktionen. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Ladda upp flera filer med Media Services .NET SDK
Följande kod visar hur du skapar en till gång och laddar upp flera filer.

Koden gör följande:

* Skapar en tom till gång med CreateEmptyAsset-metoden som definierades i föregående steg.
* Skapar en **Access policy** -instans som definierar behörigheter och varaktighet för åtkomst till till gången.
* Skapar en **lokaliserings** instans som ger åtkomst till till gången.
* Skapar en **BlobTransferClient** -instans. Den här typen representerar en klient som fungerar på Azure-blobar. I det här exemplet övervakar klienten uppladdnings förloppet. 
* Räknar upp genom filer i den angivna katalogen och skapar en **AssetFile** -instans för varje fil.
* Överför filerna till Media Services med **UploadAsync** -metoden. 

> [!NOTE]
> Använd UploadAsync-metoden för att se till att anropen inte blockeras och att filerna överförs parallellt.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Tänk på följande när du överför ett stort antal till gångar:

* Skapa ett nytt **CloudMediaContext** -objekt per tråd. **CloudMediaContext** -klassen är inte tråd säker.
* Öka NumberOfConcurrentTransfers från standardvärdet 2 till ett högre värde, t. ex. 5. Att ange den här egenskapen påverkar alla instanser av **CloudMediaContext**. 
* Behåll ParallelTransferThreadCount med standardvärdet 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Mata in till gångar i bulk med Media Services .NET SDK
Överföring av stora till gångar-filer kan vara en Flask hals under skapandet av till gångar. Genom att mata in till gångar i bulk eller "Mass inmatning", kan du koppla ifrån till gångs skapande från uppladdnings processen. Om du vill använda en Mass inmatnings metod skapar du ett manifest (IngestManifest) som beskriver till gången och tillhör ande filer. Använd sedan överförings metoden som du väljer för att överföra de associerade filerna till Manifestets BLOB-behållare. Microsoft Azure Media Services bevakar BLOB-behållaren som är associerad med manifestet. När en fil har överförts till BLOB-behållaren, Microsoft Azure Media Services slutföra till gångs skapandet baserat på konfigurationen av till gången i manifestet (IngestManifestAsset).

Om du vill skapa en ny IngestManifest anropar du metoden Create som exponeras av IngestManifests-samlingen på CloudMediaContext. Den här metoden skapar en ny IngestManifest med det manifest namn som du anger.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Skapa de till gångar som är associerade med bulk-IngestManifest. Konfigurera önskade krypterings alternativ på till gången för Mass inmatning.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

En IngestManifestAsset kopplar ihop en till gång med en Mass IngestManifest för Mass inmatning. Den associerar också AssetFiles som utgör varje till gång. Använd Create-metoden i Server kontexten om du vill skapa en IngestManifestAsset.

I följande exempel visas hur du lägger till två nya IngestManifestAssets som associerar de två till gångarna som tidigare har skapats till Mass inläsnings manifestet Varje IngestManifestAsset associerar också en uppsättning filer som överförs för varje till gång under Mass inmatning.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Du kan använda valfritt höghastighets klient program som kan överföra till gångs filen till Blob Storage-behållarens URI som tillhandahålls av egenskapen **IIngestManifest. BlobStorageUriForUpload** för IngestManifest. 

Följande kod visar hur du använder .NET SDK för att ladda upp till gångar-filerna.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Koden för att överföra till gångs filen för det exempel som används i den här artikeln visas i följande kod exempel:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Du kan bestämma förloppet för Mass inmatningen för alla till gångar som är associerade med en **IngestManifest** genom att avsöka statistik egenskapen för **IngestManifest**. För att kunna uppdatera statusinformation måste du använda en ny **CloudMediaContext** varje gång du avsöker statistik egenskapen.

I följande exempel demonstreras avsökning av en IngestManifest med hjälp av dess **ID**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Ladda upp filer med .NET SDK-tillägg
I följande exempel visas hur du laddar upp en enstaka fil med .NET SDK-tillägg. I det här fallet används metoden **CreateFromFile** , men den asynkrona versionen är även tillgänglig (**CreateFromFileAsync**). Med **CreateFromFile** -metoden kan du ange fil namn, krypterings alternativ och ett återanrop för att rapportera uppladdnings förloppet för filen.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

I följande exempel anropar funktionen UploadFile och anger lagrings kryptering som alternativet för att skapa till gångar.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade containern. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Nu när du har överfört en till gång till Media Services går du till artikeln [så här hämtar du en medie processor][How to Get a Media Processor] .

[How to Get a Media Processor]: media-services-get-media-processor.md
