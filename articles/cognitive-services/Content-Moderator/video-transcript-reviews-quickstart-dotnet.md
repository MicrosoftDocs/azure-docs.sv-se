---
title: Skapa video avskrifts granskningar med hjälp av .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en video avskrifts granskning med Azure Cognitive Services Content Moderator SDK för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853466"
---
# <a name="create-video-transcript-reviews-using-net"></a>Skapa video avskrifts granskningar med hjälp av .NET

Den här artikeln innehåller information och kod exempel som hjälper dig att snabbt komma igång med [Content moderator SDK med C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) för att:

- Skapa en video granskning för mänskliga moderatorer
- Lägg till en kontrollerad avskrift i granskningen
- Publicera granskningen

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [Granska verktyg](https://contentmoderator.cognitive.microsoft.com/) om du inte redan har gjort det.
- Den här artikeln förutsätter att du har förstorat [videon](video-moderation-api.md) och [skapat video granskningen](video-reviews-quickstart-dotnet.md) i gransknings verktyget för mänsklig besluts fattande. Nu vill du lägga till kontrollerade video avskrifter i gransknings verktyget.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Se till att API-nyckeln kan anropa gransknings-API: et (jobb skapande)

När du har slutfört föregående steg kan det hända att du har två Content Moderator-nycklar om du startade från Azure-portalen.

Om du planerar att använda den Azure-tillhandahållna API-nyckeln i ditt SDK-exempel följer du de steg som beskrivs i avsnittet om att [använda Azure-nyckel med API:et för granskning](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) så att ditt program kan anropa API:et för granskning och skapa granskningar.

Om du använder den nyckeln för den kostnadsfria utvärderingsversionen som genererades av granskningsverktyget känner ditt granskningsverktygskonto redan till nyckeln, och därför krävs inga ytterligare steg.

## <a name="prepare-your-video-for-review"></a>Förbered din video för granskning

Lägg till avskriften i en video granskning. Videon måste publiceras online. Du behöver dess slut punkt för direkt uppspelning. Slut punkten för direkt uppspelning gör att video spelaren för gransknings verktyget kan spela upp videon.

![Video demo – miniatyr](images/ams-video-demo-view.PNG)

- Kopiera **URL: en** på den här [Azure Media Services demonstrations](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) sidan för manifest-URL: en.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

1. Ge projektet namnet **VideoTranscriptReviews**.

1. Välj det här projektet som det enda startprojektet för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket för TermLists-projektet.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Ändra programmets using-satser enligt följande.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Lägga till privata egenskaper

Lägg till följande privata egenskaper i namn området **VideoTranscriptReviews**, klass **program**. Uppdatera `AzureEndpoint` fälten och `CMSubscriptionKey` med värdena för slut punkts-URL: en och prenumerations nyckeln. Du hittar dessa på fliken **snabb start** i resursen i Azure Portal.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Skapa Content Moderator klient objekt

Lägg till följande metod definition i namn området VideoTranscriptReviews, klass program.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Skapa en video granskning

Skapa en video granskning med **ContentModeratorClient. Reviews. CreateVideoReviews**. Mer information finns i [API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ, vilket ska vara "Application/JSON." 
1. Namnet på din Content Moderator-grupp.
1. Ett **ilist \<CreateVideoReviewsBodyItem>** -objekt. Varje **CreateVideoReviewsBodyItem** -objekt representerar en video granskning. Den här snabb starten skapar en granskning i taget.

**CreateVideoReviewsBodyItem** har flera egenskaper. Du kan minst ange följande egenskaper:
- **Innehåll**. URL till den video som ska granskas.
- **ContentId**. Ett ID som ska tilldelas video granskningen.
- **Status**. Ställ in värdet på "unpublished". Om du inte anger det används som standard "väntar", vilket innebär att video granskningen publiceras och väntar på mänsklig granskning. När en video granskning har publicerats kan du inte längre lägga till video bild rutor, en avskrift eller ett avskrifts redigerings resultat.

> [!NOTE]
> **CreateVideoReviews** returnerar en ilist \<string> . Var och en av de här strängarna innehåller ett ID för en video granskning. Dessa ID: n är GUID och är inte samma som värdet för egenskapen **ContentId** .

Lägg till följande metod definition i namn området VideoReviews, klass program.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Din tjänstnyckel för Content Moderator har en hastighetsgräns för begäranden per sekund (RPS). Om du överskrider gränsen utlöser SDK:n ett undantag med felkoden 429.
>
> En nyckel på den kostnadsfria nivån har en gräns på en RPS.

## <a name="add-transcript-to-video-review"></a>Lägg till avskrift till video granskning

Du lägger till en avskrift i en video granskning med **ContentModeratorClient. Rereviews. AddVideoTranscript**. **AddVideoTranscript** har följande obligatoriska parametrar:
1. Ditt Content Moderator-Team-ID.
1. Video gransknings-ID: t som returnerades av **CreateVideoReviews**.
1. Ett **Stream** -objekt som innehåller avskriften.

Avskriften måste vara i WebVTT-formatet. Mer information finns i [webvtt: webb videons text spår format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Programmet använder en exempel avskrift i VTT-format. I en verklig lösning använder du tjänsten Azure Media Indexer för att [Generera en avskrift](../../media-services/previous/media-services-index-content.md) från en video.

Lägg till följande metod definition i namn området VideotranscriptReviews, klass program.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Lägg till ett avskrifts redigerings resultat för video granskning

Förutom att lägga till en avskrift till en video granskning lägger du också till resultatet av att kontrol lera avskriften. Det gör du med **ContentModeratorClient. Reviews. AddVideoTranscriptModerationResult**. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** har följande obligatoriska parametrar:
1. En sträng som innehåller en MIME-typ, vilket ska vara "Application/JSON." 
1. Namnet på din Content Moderator-grupp.
1. Video gransknings-ID: t som returnerades av **CreateVideoReviews**.
1. En IList \<TranscriptModerationBodyItem> . En **TranscriptModerationBodyItem** har följande egenskaper:
1. **Villkor**. En IList \<TranscriptModerationBodyItemTermsItem> . En **TranscriptModerationBodyItemTermsItem** har följande egenskaper:
1. **Index**. Det nollbaserade indexet för termen.
1. **Term**. En sträng som innehåller termen.
1. **Tidsstämpel**. En sträng som innehåller, i sekunder, den tid i avskriften där villkoren påträffades.

Avskriften måste vara i WebVTT-formatet. Mer information finns i [webvtt: webb videons text spår format](https://www.w3.org/TR/webvtt1/).

Lägg till följande metod definition i namn området VideoTranscriptReviews, klass program. Den här metoden skickar en avskrift till metoden **ContentModeratorClient. TextModeration. ScreenText** . Det översätter också resultatet till en IList \<TranscriptModerationBodyItem> och skickas till **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publicera video granskning

Du publicerar en video granskning med **ContentModeratorClient. Rereviews. PublishVideoReview**. **PublishVideoReview** har följande obligatoriska parametrar:
1. Namnet på din Content Moderator-grupp.
1. Video gransknings-ID: t som returnerades av **CreateVideoReviews**.

Lägg till följande metod definition i namn området VideoReviews, klass program.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Färdigställa allt

Lägg till **huvud** metods definitionen i namn området VideoTranscriptReviews, klass program. Stäng slutligen program klassen och VideoTranscriptReviews-namnområdet.

> [!NOTE]
> Programmet använder en exempel avskrift i VTT-format. I en verklig lösning använder du tjänsten Azure Media Indexer för att [Generera en avskrift](../../media-services/previous/media-services-index-content.md) från en video.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

När du kör programmet visas utdata på följande rader:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigera till din video avskrifts granskning

Gå till granskningen av video avskriften i Content moderator gransknings verktyget på skärmen **Granska** > **video** > **avskrift** .

Följande funktioner visas:
- De två avskrifts raderna som du har lagt till
- Tiden för svordomar hittades och markeras av text redigerings tjänsten
- Om du väljer avskrifts text startar videon från den tidsstämpeln

![Video avskrifts granskning för mänskliga moderatorer](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Nästa steg

Hämta [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för den här och andra Content moderator snabb starter för .net.

Lär dig hur du genererar [video granskningar](video-reviews-quickstart-dotnet.md) i gransknings verktyget.
