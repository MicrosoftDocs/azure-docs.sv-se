---
title: Utveckla Azure Functions med Media Services
description: Det här avsnittet visar hur du börjar utveckla Azure Functions med Media Services med hjälp av Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ef8e3a2e733247f713fe0240cb8ef6069803fcb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013966"
---
# <a name="develop-azure-functions-with-media-services"></a>Utveckla Azure Functions med Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln visar hur du kommer igång med att skapa Azure Functions som använder Media Services. Azure-funktionen som definieras i den här artikeln övervakar en lagrings konto behållare med namnet **indata** för nya MP4-filer. När en fil har släppts i lagrings behållaren kör BLOB-utlösaren funktionen. Information om hur du granskar Azure Functions finns i  [Översikt](../../azure-functions/functions-overview.md) och andra ämnen i avsnittet **Azure Functions** .

Om du vill utforska och distribuera befintliga Azure Functions som använder Azure Media Services kan du kolla [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Den här lagrings platsen innehåller exempel som använder Media Services för att Visa arbets flöden som är relaterade till att mata in innehåll direkt från Blob Storage, kodning och skrivning av innehåll till Blob Storage. Den innehåller också exempel på hur du övervakar jobb meddelanden via Webhooks och Azure-köer. Du kan också utveckla dina funktioner baserat på exemplen i [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) -lagringsplatsen. Distribuera funktionerna genom att trycka på knappen **distribuera till Azure** .

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).
- Om du ska skapa Azure Functions som utför åtgärder på ditt Azure Media Services-konto (AMS) eller lyssnar på händelser som skickats av Media Services, bör du skapa ett AMS-konto enligt beskrivningen [här](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Skapa en funktionsapp

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Skapa en Function-app enligt beskrivningen [här](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Ett lagrings konto som du anger i **StorageConnection** -miljövariabeln (se nästa steg) ska finnas i samma region som din app.

## <a name="configure-function-app-settings"></a>Konfigurera funktions program inställningar

När du utvecklar Media Services funktioner är det praktiskt att lägga till miljövariabler som ska användas i dina funktioner. Om du vill konfigurera appinställningar klickar du på länken konfigurera app-inställningar. Mer information finns i  [så här konfigurerar du inställningar för Azure Function-appar](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funktionen som definieras i den här artikeln förutsätter att du har följande miljövariabler i dina app-inställningar:

**AMSAADTenantDomain**: Azure AD-klientens slut punkt. Mer information om hur du ansluter till AMS-API: et finns i [den här](media-services-use-aad-auth-to-access-ams-api.md) artikeln.

**AMSRESTAPIEndpoint**: URI som representerar REST API slut punkten. 

**AMSClientId**: klient-ID för Azure AD-program.

**AMSClientSecret**: Azure AD-programmets klient hemlighet.

**StorageConnection**: lagrings anslutning för det konto som är kopplat till Media Services kontot. Det här värdet används i filen **function.jspå** filen och filen **Run. CSX** (beskrivs nedan).

## <a name="create-a-function"></a>Skapa en funktion

När din Function-app har distribuerats kan du hitta den bland **App Services** Azure Functions.

1. Välj din Function-app och klicka på **ny funktion**.
2. Välj **C#** -språket och **data bearbetnings** scenariot.
3. Välj **en** -mall. Den här funktionen utlöses när en BLOB överförs till behållaren för **indatafilen** . **Indatamängden** anges i **sökvägen** i nästa steg.

    ![Skärm bild som visar dialog rutan Välj en mall med en valt.](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. När du har valt **en** visas vissa fler kontroller på sidan.

    ![Skärm bild som visar dialog rutan namnge din funktion.](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klicka på **Skapa**. 

## <a name="files"></a>Filer

Din Azure-funktion är associerad med kodfragment och andra filer som beskrivs i det här avsnittet. När du använder Azure Portal för att skapa en funktion skapas **function.jspå** och **Kör. CSX** . Du måste lägga till eller ladda upp en **project.jspå** filen. Resten av det här avsnittet ger en kort förklaring av varje fil och visar deras definitioner.

![Skärm bild som visar JSON-filerna i projektet.](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Filen function.jsi definierar funktions bindningarna och andra konfigurations inställningar. Körningen använder den här filen för att avgöra vilka händelser som ska övervakas och hur du skickar data till och returnerar data från funktions körning. Mer information finns i [Azure Functions HTTP and webhook bindings](../../azure-functions/functions-reference.md#function-code) (HTTP- och webhookbindningar i Azure Functions).

>[!NOTE]
>Ange egenskapen **Disabled** till **True** för att förhindra att funktionen körs. 

Ersätt innehållet i den befintliga function.jspå filen med följande kod:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.jspå

project.jsfilen innehåller beroenden. Här är ett exempel på **project.jspå** en fil som innehåller de nödvändiga .net Azure Media Services-paketen från NuGet. Observera att versions numren ändras med de senaste uppdateringarna av paketen, så du bör bekräfta de senaste versionerna. 

Lägg till följande definition i project.jspå. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>Kör. CSX

Det här är C#-koden för din funktion.  Funktionen som definieras nedan övervakar en lagrings konto behållare med namnet **indata** (det vill säga vad som angavs i sökvägen) för nya MP4-filer. När en fil har släppts i lagrings behållaren kör BLOB-utlösaren funktionen.
    
Exemplet som definieras i det här avsnittet visar 

1. Hur man matar in en till gång i ett Media Services konto (genom att kopiera en blob till en AMS-till gång) och 
2. så här skickar du ett kodnings jobb som använder Media Encoder Standard "adaptiv streaming"-för inställning.

I det verkliga livet vill du förmodligen spåra jobbets förlopp och sedan publicera din kodade till gång. Mer information finns i [använda Azure Webhooks för att övervaka Media Services jobb meddelanden](media-services-dotnet-check-job-progress-with-webhooks.md). Fler exempel finns i [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Ersätt innehållet i den befintliga filen Run. CSX med följande kod: när du är klar med att definiera en funktion klickar du på **Spara och kör**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
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

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testa din funktion

Om du vill testa din funktion måste du ladda upp en MP4-fil till **indatafilen** för det lagrings konto som du har angett i anslutnings strängen.  

1. Välj det lagrings konto som du har angett i **StorageConnection** -miljövariabeln.
2. Klicka på **blobbar**.
3. Klicka på **+Behållare**. Namnge behållar **indatamängden**.
4. Tryck på **överför** och bläddra till en MP4-fil som du vill ladda upp.

>[!NOTE]
> När du använder en BLOB-utlösare i en förbruknings plan kan det vara upp till 10 minuter att bearbeta nya blobbar när en Function-app har varit inaktiv. När Function-appen körs bearbetas blobarna direkt. Mer information finns i [Blob Storage-utlösare och bindningar](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja utveckla ett Media Services-program. 
 
Mer information och fullständiga exempel/lösningar för att använda Azure Functions och Logic Apps med Azure Media Services för att skapa anpassade arbets flöden för skapande av innehåll finns i avsnittet [Media Services .net Functions-integrering på GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Se även [använda Azure Webhooks för att övervaka Media Services jobb meddelanden med .net](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
