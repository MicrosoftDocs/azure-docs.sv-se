---
title: Indexera mediefiler med Azure Media Indexer
description: Azure Media Indexer gör det möjligt för dig att söka efter innehållet i dina mediefiler och generera en full text avskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder Media Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: c575904d994232726cf8d0d9152b02130fd29cea
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013262"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexera mediefiler med Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Vi rekommenderar att kunderna migrerar från Indexer v1 och Indexer v2 till att använda [Media Services v3 AudioAnalyzerPreset Basic-läge](../latest/analyzing-video-audio-files-concept.md). [Azure Media Indexer](media-services-index-content.md) medie processor och Azure Media Indexer 2-processorer för för [hands versions](./legacy-components.md) mediet dras tillbaka. Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen.

Azure Media Indexer gör det möjligt för dig att söka efter innehållet i dina mediefiler och generera en full text avskrift för dold textning och nyckelord. Du kan bearbeta en mediefil eller flera mediafiler i en batch.  

När du indexerar innehåll måste du se till att använda mediefiler som har tydliga tal (utan Bakgrunds musik, brus, effekter eller mikrofon hiss). Några exempel på lämpligt innehåll är: registrerade möten, föreläsningar eller presentationer. Följande innehåll kanske inte är lämpligt för indexering: filmer, TV-program, allt med blandad ljud-och ljud effekter, dåligt inspelat innehåll med bakgrunds brus (hiss).

Ett indexerings jobb kan generera följande utdata:

* Filer med dold textning i följande format: **ttml** och **webvtt**.
  
    Filer med dold textning innehåller en tagg som kallas identifierbarhet, som ger ett indexerings jobb baserat på hur det går att känna igen talet i käll videon.  Du kan använda värdet för att känna igen för att skriva ut filer för användbarhet. En låg poäng skulle innebära dåliga indexerings resultat på grund av ljud kvaliteten.
* Nyckelords fil (XML).

Den här artikeln visar hur du skapar indexerings jobb för att **indexera en till gång** och **Indexera flera filer**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Använda konfigurations-och MANIFEST filer för indexerings aktiviteter
Du kan ange mer information för dina indexerings aktiviteter med hjälp av en aktivitets konfiguration. Du kan till exempel ange vilka metadata som ska användas för medie filen. Dessa metadata används av språk motorn för att utöka sin vokabulär och förbättrar tal igenkännings precisionen avsevärt.  Du kan också ange önskade utdatafiler.

Du kan också bearbeta flera mediefiler samtidigt med hjälp av en manifest fil.

Mer information finns i [uppgifts för inställning för Azure Media Indexer](./legacy-components.md).

## <a name="index-an-asset"></a>Indexera en till gång
Följande metod laddar upp en mediefil som en till gång och skapar ett jobb för att indexera till gången.

Om ingen konfigurations fil anges indexeras medie filen med alla standardinställningar.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Utdatafiler
Som standard genererar ett indexerings jobb följande utdatafiler. Filerna lagras i den första till gången till utdata.

Om det finns fler än en medietyp i indatafilen genererar indexeraren en manifest fil för jobbets utdata, med namnet JobResult.txt. De resulterande TTML-, WebVTT-och Keyword-filerna numreras sekventiellt och namnges med hjälp av "alias" för varje Media-fil.

| Filnamn | Beskrivning |
| --- | --- |
| **InputFileName. ttml**<br/>**InputFileName. VTT** |Filer med dold textning (CC) i formaten TTML och WebVTT.<br/><br/>De kan användas för att göra ljud-och videofiler tillgängliga för personer med hörsel funktions hinder.<br/><br/>Filer med dold textning innehåller en tagg som kallas <b>identifierbarhet</b> , vilket innebär att ett indexerings jobb baseras på hur det går att känna igen talet i käll videon.  Du kan använda värdet för att <b>känna igen</b> för att skriva ut filer för användbarhet. En låg poäng skulle innebära dåliga indexerings resultat på grund av ljud kvaliteten. |
| **InputFileName.kw.xml<br/> InputFileName.info** |Nyckelord och konfigurationsfiler. <br/><br/>Nyckelords filen är en XML-fil som innehåller nyckelord som har extraherats från tal innehållet, med frekvens och offset-information. <br/><br/>Informations filen är en oformaterad textfil som innehåller detaljerad information om varje term som identifieras. Den första raden är speciell och innehåller tolknings poängen. Varje efterföljande rad är en tabbavgränsad lista med följande data: start tid, slut tid, ord/fras, relevans. Tiderna anges i sekunder och förtroendet ges som ett tal från 0-1. <br/><br/>Exempel rad: "1,20 1,45 Word 0,67" <br/><br/>Dessa filer kan användas för ett antal olika sätt, till exempel för att utföra tal analys, eller exponeras för sökmotorer som Bing, Google eller Microsoft SharePoint för att göra mediefilerna mer synliga eller till och med använda för att leverera mer relevanta annonser. |
| **JobResult.txt** |Utdata manifestet visas bara när du indexerar flera filer, som innehåller följande information:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Fel</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Om inte alla indatafiler indexeras utan fel, Miss lyckas indexerings jobbet med felkoden 4000. Mer information finns i [fel koder](#error_codes).

## <a name="index-multiple-files"></a>Indexera flera filer
Följande metod överför flera mediafiler som en till gång och skapar ett jobb för att indexera alla dessa filer i en batch.

En manifest fil med tillägget ". lst" skapas och överförs till till gången. Manifest filen innehåller en lista över alla till gångs filer. Mer information finns i [uppgifts för inställning för Azure Media Indexer](./legacy-components.md).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Delvis slutfört jobb
Om inte alla indatafiler indexeras korrekt kommer indexerings jobbet att Miss lyckas med felkoden 4000. Mer information finns i [fel koder](#error_codes).

Samma utdata (som slutförda jobb) skapas. Du kan referera till manifest filen för utdata för att ta reda på vilka indatafiler som har misslyckats, enligt värdena i fel kolumnen. De resulterande TTML, WebVTT och keywords-filerna kommer inte att genereras för indatafiler som misslyckades.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Uppgifts för inställning för Azure Media Indexer
Bearbetningen från Azure Media Indexer kan anpassas genom att tillhandahålla en valfri aktivitets för inställning tillsammans med aktiviteten.  I följande avsnitt beskrivs formatet för den här konfigurations-XML-filen.

| Name | Kräver | Beskrivning |
| --- | --- | --- |
| **inleveranstransport** |falskt |Till gångs fil (er) som du vill indexera.</p><p>Azure Media Indexer stöder följande medie fil format: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Du kan ange fil namnen i **indata** -elementets **namn** eller **list** -attribut (se nedan). Om du inte anger vilken till gångs fil som ska indexeras, kommer den primära filen att plockas. Om ingen primär till gångs fil anges indexeras den första filen i indata till gången.</p><p>Om du uttryckligen vill ange namnet på till gångs filen gör du följande:<br/>`<input name="TestFile.wmv">`<br/><br/>Du kan också indexera flera till gångs filer samtidigt (upp till 10 filer). Gör så här:<br/><br/><ol class="ordered"><li><p>Skapa en textfil (manifest fil) och ge den fil tillägget LST. </p></li><li><p>Lägg till en lista över alla till gångs fil namn i din indata till gång till manifest filen. </p></li><li><p>Lägg till (överför) manifest filen till till gången.  </p></li><li><p>Ange namnet på manifest filen i indatans List-attribut.<br/>`<input list="input.lst">`</li></ol><br/><br/>Obs: om du lägger till fler än 10 filer i manifest filen kommer indexerings jobbet att Miss 2006-felkoden. |
| **metadatatjänst** |falskt |Metadata för de angivna till gångs fil (er) som används för vokabulär anpassning.  Användbart för att förbereda indexeraren för att identifiera ord som inte är standard ord listor, till exempel riktiga substantiv.<br/>`<metadata key="..." value="..."/>` <br/><br/>Du kan ange **värden** för fördefinierade **nycklar**. För närvarande stöds följande nycklar:<br/><br/>"title" och "Description" – används för att anpassa ord listan för att anpassa språk modellen för ditt jobb och förbättra precisionen för tal igenkänning.  Värdena för att dirigera Internet söker efter Sammanhangs beroende text dokument, med hjälp av innehållet för att utöka den interna ord listan under din indexerings aktivitet.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **egenskaper** <br/><br/> Tillagt i version 1,2. För närvarande är den enda funktionen som stöds tal igenkänning ("ASR"). |falskt |Tal igenkännings funktionen har följande inställnings nycklar:<table><tr><th><p>Nyckel</p></th>        <th><p>Beskrivning</p></th><th><p>Exempelvärde</p></th></tr><tr><td><p>Språk</p></td><td><p>Det naturliga språket som ska identifieras i multimedie filen.</p></td><td><p>Engelska, spanska</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>en semikolonavgränsad lista med önskade text format för utdata (om det finns några)</p></td><td><p>ttml; webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Värdet !</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>En boolesk flagga som anger om en nyckelords-XML-fil krävs eller inte.</p></td><td><p>Värdet !. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>En boolesk flagga som anger om full textning ska tvingas (oavsett konfidensnivå).  </p><p>Standardvärdet är false, vilket innebär att ord och fraser som har en lägre konfidensnivå än 50% utelämnas från de slutliga text utmatningarna och ersätts av ellipser ("...").  Ellipserna är användbara för text kvalitets kontroll och granskning.</p></td><td><p>Värdet !. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Felkoder
Om ett fel uppstår bör Azure Media Indexer rapportera tillbaka en av följande felkoder:

| Kod | Name | Möjliga orsaker |
| --- | --- | --- |
| 2000 |Ogiltig konfiguration |Ogiltig konfiguration |
| 2001 |Ogiltiga inmatade till gångar |Inmatade till gångar eller tom till gång saknas. |
| 2002 |Ogiltigt manifest |Manifestet är tomt eller så innehåller manifestet ogiltiga objekt. |
| 2003 |Det gick inte att hämta medie filen |Ogiltig URL i manifest filen. |
| 2004 |Protokollet stöds inte |Protokoll för medie webb adress stöds inte. |
| 2005 |Filtyper som inte stöds |Medietyp för indatafilen stöds inte. |
| 2006 |För många indatafiler |Det finns fler än 10 filer i manifestet för indatamängden. |
| 3000 |Det gick inte att avkoda medie filen |Medie-codec som inte stöds <br/>eller<br/> Skadad medie fil <br/>eller<br/> Ingen ljud ström i indata-mediet. |
| 4000 |Batch-indexering delvis genomförd |Det gick inte att indexera några av medierna med indatafiler. Mer information finns i <a href="#output_files">utdatafiler</a>. |
| other |Interna fel |Kontakta support teamet. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Språk som stöds
För närvarande stöds de engelska och spanska språken.  

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Översikt över Azure Media Services Analytics](./legacy-components.md)

[Indexera mediefiler med Azure Media Indexer 2 för hands version](./legacy-components.md)