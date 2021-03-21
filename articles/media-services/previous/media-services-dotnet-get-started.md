---
title: Kom igång med att leverera innehåll på begäran med hjälp av .NET | Microsoft Docs
description: Dessa självstudier vägleder dig genom stegen för implementera ett program med leverans av innehåll på begäran med Azure Media Services genom att använda .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e7f9fa33af43fb846255815d862d4cd3a07e99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013983"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Kom igång med att leverera innehåll på begäran med hjälp av .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Den här självstudiekursen beskriver steg för steg hur du implementerar en enkel VoD-innehållsleveranstjänst (Video-on-Demand) med Azure Media Services-programmet (AMS) med hjälp av Azure Media Services .NET SDK.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra vägledningen:

* Ett Azure-konto. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett Media Services-konto. Information om hur du skapar ett Media Services konto finns i [så här skapar du ett Media Services konto](media-services-portal-create-account.md).
* .NET Framework 4.0 eller senare.
* Visual Studio.

Vägledningen innehåller följande uppgifter:

1. Starta slutpunkt för direktuppspelning (med hjälp av Azure Portal).
2. Skapar och konfigurerar ett Visual Studio-projekt.
3. Ansluter till Media Services-kontot.
2. Överföra en videofil.
3. Kodar källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.
4. Publicera tillgången och få URL:er för strömning och progressiv överföring.  
5. Spela upp ditt innehåll.

## <a name="overview"></a>Översikt
Den här självstudiekursen vägleder dig genom stegen för att implementera ett program för leverans av Video-on-Demand (VoD) med Azure Media Services (AMS) SDK för .NET.

Självstudierna innehåller det grundläggande Media Services-arbetsflödet och de vanligaste programmeringsobjekt och -uppgifter som krävs för utveckling av Media Services. I slutet av självstudierna kommer du att kunna strömma eller progressivt hämta en exempelmediefil som du har överfört, kodat och hämtat.

### <a name="ams-model"></a>AMS-modell

Följande bild visar några av de vanligast använda objekten när du utvecklar VoD-program mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

[![Diagram över några av de vanligaste objekten i Azure Media Services objekt data modell för utveckling av video på begäran-program.](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

Du kan visa hela modellen [här](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starta slutpunkter för direktuppspelning med Azure Portal

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera video via direktuppspelning med anpassningsbar bithastighet. Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassningsbar bithastighet i direktuppspelningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) direkt när du så önskar, utan att du behöver lagra på förhand paketerade versioner av vart och ett av dessa direktuppspelningsformat.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

Starta slutpunkten för direktuppspelning genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I fönstret Inställningar klickar du på Slutpunkter för direktuppspelning.
3. Klicka på den slutpunkt för direktuppspelning som är standard.

    Fönstret INFORMATION OM DEN SLUTPUNKT FÖR DIREKTUPPSPELNING SOM ÄR STANDARD visas.

4. Klicka på ikonen Start.
5. Klicka på knappen Spara för att spara ändringarna.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 
2. Skapa en ny mapp (mappen kan vara på valfri plats på den lokala enheten) och kopiera en MP4-fil som du vill koda och strömma eller ladda ner progressivt. I det här exemplet används sökvägen ”C:\VideoFiles”.

## <a name="connect-to-the-media-services-account"></a>Anslut till Media Services-kontot

När du använder Media Services med .NET ska du använda klassen **CloudMediaContext** för de flesta Media Services-programmeringsuppgifter. Det gäller till exempel att ansluta till Media Services-kontot, skapa, uppdatera, komma åt och ta bort följande objekt: tillgångar, tillgångsfiler, jobb, åtkomstprinciper, positionerare o.s.v.

Skriv över standardprogramklassen med följande kod: Koden visar hur du läser anslutningsvärdena från filen App.config och hur du skapar objektet **CloudMediaContext** för att kunna ansluta till Media Services. Mer information finns i avsnittet om hur du [ansluter till Media Services API](media-services-use-aad-auth-to-access-ams-api.md).

Se till att uppdatera filnamnet och sökvägen till där du har din mediefil.

Funktionen **Main** anropar metoder som definieras ytterligare i det här avsnittet.

> [!NOTE]
> Kompileringsfel kommer att uppstå tills du lägger till definitioner för alla funktioner som definieras längre ned i den här artikeln.

```csharp
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

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Skapa en ny tillgång och ladda upp en videofil

I Media Services överför du (eller för in) dina digitala filer till en tillgång. **Till gångs** enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer.)  När filerna har laddats upp lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning. Filerna i tillgången kallas **Tillgångsfiler**.

Den **UploadFile**-metod som definieras nedan kallas **CreateFromFile** (definieras i .NET SDK-tillägg). **CreateFromFile** skapar en ny tillgång som den angivna källfilen överförs till.

**CreateFromFile**-metoden tar **AssetCreationOptions**, vilket gör att du kan ange något av följande alternativ för att skapa tillgångar:

* **Ingen** – Ingen kryptering används. Detta är standardvärdet. Observera att när du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagringsutrymmet.
  Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet.
* **StorageEncrypted** – Använd det här alternativet för att kryptera innehållet lokalt med hjälp av 256-bitars AES-kryptering (Advanced Encryption Standard), som sedan överför det till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
* **CommonEncryptionProtected** – Använd det här alternativet om du överför innehåll som redan har krypterats och som skyddas med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
* **EnvelopeEncryptionProtected** – Använd det här alternativet om du överför HLS som krypterats med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.

Med metoden **CreateFromFile** kan du även ange ett återanrop för att rapportera filens överföringsförlopp.

I följande exempel anger vi **Inget** för tillgångsalternativen.

Lägg till följande metod i programklassen.

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

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet 
När du har fört in tillgångar i Media Services kan media kodas, användas med transmux, förses med vattenstämpel och så vidare innan de skickas till klienter. Dessa aktiviteter schemaläggs och körs mot flera bakgrundsrollinstanser för höga prestanda och tillgänglighet. De här aktiviteterna kallas jobb och varje jobb består av atomiska uppgifter som gör det faktiska arbetet i tillgångsfilen.

Som tidigare nämnts är ett mycket vanligt scenario när du arbetar med Azure Media Services att du levererar strömning med anpassningsbar bithastighet till klienterna. Media Services kan dynamiskt paketera en uppsättning MP4-filer med anpassningsbar bithastighet till något av följande format: HTTP Live Streaming(HLS), Smooth Streaming och MPEG DASH.

Om du vill dra nytta av dynamisk paketering måste du koda eller omkoda din mezzaninfil (källa) till en uppsättning MP4- eller Smooth Streaming-filer med anpassningsbar bithastighet.  

Följande kod visar hur du skickar ett kodningsjobb. Jobbet innehåller en uppgift som anger att omkodning av mezzaninfilen till en uppsättning MP4-filer med anpassningsbar bithastighet genom att använda **Media Encoder Standard**. Koden skickar jobbet och väntar tills det har slutförts.

När jobbet har slutförts ska du kunna strömma din tillgång eller progressivt hämta MP4-filer som skapades på grund av omkodningen.

Lägg till följande metod i programklassen.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicera tillgången och få URL:er för strömning och progressiv nedladdning

Om du vill strömma eller hämta en tillgång behöver du först ”publicera” den genom att skapa en positionerare. Lokaliserare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) och Access Signature (SAS)-positionerare som används för att hämta mediefiler.

### <a name="some-details-about-url-formats"></a>Information om URL-format

När du har skapat positionerna kan du skapa de URL:er som skulle användas för att strömma eller hämta dina filer. Exemplen i den här handledningen skapar URL:er som du kan klistra in i rätt webbläsare. Det här avsnittet ger bara korta exempel på hur olika format ser ut.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>En strömmande URL för MPEG DASH har följande format:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>En strömmande URL för HLS har följande format:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>En strömmande URL för Smooth Streaming har följande format:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>En SAS-URL som används för att hämta filer har följande format:

{blob-containernamn}/{tillgångsnamn}/{filnamn}/{SAS-signatur}

Media Services .NET SDK-tillägg ger praktiska hjälpmetoder som returnerar formaterade URL:er för den publicerade tillgången.

Följande kod använder .NET SDK-tillägg för att skapa positionerare och hämta URL:er för strömning och progressiv nedladdning. Koden visar även hur du laddar ned filer till en lokal mapp.

Lägg till följande metod i programklassen.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testa genom att spela upp ditt innehåll

När du kör programmet som definieras i föregående avsnitt visas URL:er som liknar dessa i konsolfönstret.

URL:er för anpassningsbar strömning:

Smooth Streaming

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

URL:er för progressiv nedladdning (ljud och video).

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


Strömma din video genom att klistra in URL:en i URL-textrutan i [Azure Media Services Player](https://aka.ms/azuremediaplayer).

Testa den progressiva nedladdningen genom att klistra in en URL i en webbläsare (till exempel Internet Explorer, Chrome eller Safari).

Mer information finns i följande avsnitt:

- [Spela upp ditt innehåll med befintliga spelare](media-services-playback-content-with-existing-players.md)
- [Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Ladda ned exempel
Följande kodexempel innehåller koden som du skapade i den här kursen: [exempel](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
