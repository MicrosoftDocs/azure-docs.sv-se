---
title: Signalera beskrivande ljud spår med Media Services v3
description: Följ stegen i den här självstudien för att ladda upp en fil, koda videon, lägga till beskrivande ljud spår och strömma ditt innehåll med Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00a3fa397bf88520fa4923b6fbe7495c0aa0b8a2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277410"
---
# <a name="signal-descriptive-audio-tracks"></a>Signals beskrivande ljud spår

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Du kan lägga till en berättarröst i videon för att hjälpa visuellt synskadade klienter att följa videoinspelningen genom att lyssna på berättarrösten. I Media Services v3 signalerar du beskrivande ljud spår genom att kommentera ljud spåret i manifest filen.

Den här artikeln visar hur du kodar en video, laddar upp en ljudfil med enbart ljudfiler (AAC-codec) som innehåller beskrivande ljud i utmatnings till gången och redigerar. ISM-filen för att inkludera det beskrivande ljudet.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./account-create-how-to.md).
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du behöver använda dem för att få åtkomst till API.
- Granska [dynamisk paketering](encode-dynamic-packaging-concept.md).
- Läs själv studie kursen [Ladda upp, koda och strömma videor](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Skapa en indatatillgång och ladda upp en lokal fil till den 

Funktionen **CreateInputAsset** skapar en ny indata [till gång](/rest/api/media/assets) och laddar upp den angivna lokala video filen i den. Den här **till gången** används som inmatad till ditt kodnings jobb. I Media Services v3 kan indata till ett **jobb** antingen vara en **tillgång** eller innehåll som du gör tillgängligt för Media Services-kontot via HTTPS-webbadresser. 

Om du vill lära dig hur du kodar från en HTTPS-URL, se [den här artikeln](job-input-from-http-how-to.md) .  

I Media Services v3 använder du Azure Storage-API:er till att ladda upp filer. I följande .NET-kodfragment visas hur du gör detta.

Den här funktionen utför följande åtgärder:

* Skapar en **tillgång** 
* Hämtar en skrivbar [SAS-URL](../../storage/common/storage-sas-overview.md) till tillgångens [container i lagringen](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Om du behöver skicka namnet på den skapade Indatakällan till andra metoder, se till att använda `Name` egenskapen på objektet till gångs objekt som returnerades från `CreateInputAssetAsync` , till exempel inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Skapa en utgående till gång för att lagra resultatet av kodnings jobbet

Utmatnings [till gången](/rest/api/media/assets) lagrar resultatet av ditt kodnings jobb. Följande funktion visar hur du skapar en utmatnings till gång.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Om du behöver skicka namnet på den skapade till gången till andra metoder, se till att använda `Name` egenskapen på objektet till gångs objekt som returnerades från `CreateIOutputAssetAsync` , till exempel outputAsset.Name. 

När det gäller den här artikeln skickar du `outputAsset.Name` värdet till- `SubmitJobAsync` och- `UploadAudioIntoOutputAsset` funktionerna.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Skapa en transformering och ett jobb som kodar den överförda filen

När kodningen eller bearbetningen av innehåll i Media Services görs, konfigureras vanligtvis kodningsinställningarna som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video tillämpar du receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **Transformering**. Mer information finns i [Transformeringar och jobb](./transform-jobs-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som kodar videon för att strömma den till olika iOS- och Android-enheter. 

I följande exempel skapas en transformering (om det inte finns någon).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Följande funktion skickar ett jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Vi rekommenderar att du använder Event Grid för att vänta tills jobbet har slutförts.

Jobbet går vanligt vis igenom följande tillstånd: **schemalagda**, **köade**, **bearbetar**, **slutförda** (slutligt tillstånd). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

Mer information finns i [hantera Event Grid händelser](monitoring/reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Ladda upp en MP4-fil med enbart ljud

Ladda upp den ytterligare endast ljudmp4-filen (AAC-codec) som innehåller beskrivande ljud i utmatnings till gången.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Här är ett exempel på ett anrop till `UpoadAudioIntoOutputAsset` funktionen:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Redigera. ISM-filen

När ditt kodnings jobb är färdigt innehåller utmatnings till gången de filer som genereras av kodnings jobbet. 

1. I Azure Portal navigerar du till det lagrings konto som är associerat med ditt Media Services-konto. 
1. Hitta behållaren med namnet på din utmatnings till gång. 
1. Leta upp. ISM-filen i behållaren och klicka på **Redigera BLOB** (i det högra fönstret). 
1. Redigera. ISM-filen genom att lägga till information om den överförda MP4-filen (AAC-codec) som innehåller beskrivande ljud och trycka på **Spara** när du är färdig.

    För att signalera de beskrivande ljud spåren måste du lägga till parametrarna "hjälpmedel" och "roll" i. ISM-filen. Det är ditt ansvar att ange dessa parametrar korrekt för att signalera ljud spår som ljud beskrivning. Du kan till exempel lägga till `<param name="accessibility" value="description" />` och `<param name="role" value="alternate" />` till. ISM-filen för ett särskilt ljud spår, som du ser i följande exempel.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Hämta en utströmnings Locator

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra detta i två steg: Först skapar du en [positionerare för direktuppspelning](/rest/api/media/streaminglocators) och därefter skapar du de strömmande URL:er som klienterna ska använda. 

Processen att skapa en **positionerare för direktuppspelning** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en [StreamingLocator](/rest/api/media/streaminglocators) måste du ange önskat **StreamingPolicyName**. I det här exemplet kommer du att strömma in-Clear (eller icke-krypterat innehåll), så att den fördefinierade rensa streaming-principen (**PredefinedStreamingPolicy. ClearStreamingOnly**) används.

> [!IMPORTANT]
> Om du använder en anpassad [strömningsprincip](/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet strömningsprincipposter. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

Följande kod förutsätter att du anropar funktionen med ett unikt locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Även om exemplet i det här avsnittet beskriver strömning, kan du använda samma anrop för att skapa en positionerare för direktuppspelning som levererar video via progressiv nedladdning.

### <a name="get-streaming-urls"></a>Hämta strömnings-URL:er

Nu när [positioneraren för direktuppspelning](/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelnings-URL:erna, som du ser i **GetStreamingURLs**. För att skapa en webbadress måste du sammanfoga [strömningsslutpunktens](/rest/api/media/streamingendpoints) värdnamn och sökvägen för **positioneraren för direktuppspelning**. I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för* **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

> [!NOTE]
> I den här metoden behöver du det locatorName som användes när du skapade **positioneraren för direktuppspelning** för utdatatillgången.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testa med Azure Media Player

I den här artikeln används Azure Media Player till att testa strömningen. 

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I rutan **URL:** klistrar du in en av de strömmande URL-värdena som du fick från ditt program. 
 
     Du kan klistra in URL:en i formatet HLS, Dash eller Smooth så växlar Azure Media Player automatiskt till ett lämpligt strömningsprotokoll för uppspelning på din enhet.
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="next-steps"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial.md)
