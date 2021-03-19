---
title: Använda moderator jobb med .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd Content Moderator .NET SDK för att initiera innehålls redigerings jobb från slut punkt till slut punkt för bild-eller text innehåll i Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ec101786f33aa6f2525d685993d6b6c891ab2e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936324"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definiera och använda kontroll jobb (.NET)

Ett redigerings jobb fungerar som en typ av omslutning för funktionerna i Content moderatoring, arbets flöden och recensioner. Den här guiden innehåller information och kod exempel som hjälper dig att komma igång med [Content moderator SDK för .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) för att:

- Starta ett modereringsjobb för att genomsöka och skapa granskningar för mänskliga moderatorer
- Hämta status för väntande granskning
- Spåra och hämta den slutgiltiga statusen för granskningen
- Skicka gransknings resultaten till återanrops-URL: en

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Se till att din API-nyckel kan anropa gransknings-API:et för att skapa en granskning

När du har slutfört föregående steg kan det hända att du har två Content Moderator-nycklar om du startade från Azure-portalen.

Om du planerar att använda den Azure-tillhandahållna API-nyckeln i ditt SDK-exempel följer du de steg som beskrivs i avsnittet om att [använda Azure-nyckel med API:et för granskning](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) så att ditt program kan anropa API:et för granskning och skapa granskningar.

Om du använder den nyckeln för den kostnadsfria utvärderingsversionen som genererades av granskningsverktyget känner ditt granskningsverktygskonto redan till nyckeln, och därför krävs inga ytterligare steg.

## <a name="define-a-custom-moderation-workflow"></a>Definiera ett anpassat modereringsarbetsflöde

Ett modereringsjobb söker igenom innehållet med hjälp av API:erna och använder ett **arbetsflöde** för att avgöra huruvida det ska skapas granskningar.
Granskningsverktyget innehåller ett standardarbetsflöde, men vi [definierar ett anpassat arbetsflöde](Review-Tool-User-Guide/Workflows.md) för den här snabbstarten.

Du använder namnet på arbetsflödet i din kod som startar modereringsjobbet.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

   I exempelkoden ger du projektet namnet **CreateReviews**.

1. Välj det här projektet som det enda startprojektet för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Ändra programmets using-instruktioner.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Skapa Content Moderator-klienten

Lägg till följande kod för att skapa en Content Moderator-klient för din prenumeration.

> [!IMPORTANT]
> Uppdatera fälten **AzureEndpoint** och **CMSubscriptionKey** med värdena för din slut punkts-URL och prenumerations nyckel.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Initiera programspecifika inställningar

Lägg till följande konstanter och statiska fält till klassen **Program** i Program.cs.

> [!NOTE]
> Du kan ange konstanten TeamName till det namn som du använde när du skapade Content Moderator-prenumerationen. Du hämtar TeamName från den Content Moderator-webbplatsen.
> När du har loggat in väljer du **Autentiseringsuppgifter** från menyn **Inställningar** (kugghjulet).
>
> Teamnamnet är värdet för **Id**-fältet i avsnittet **API**.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Lägg till kod för att automoderera, skapa en granskning och hämta jobbinformation

> [!Note]
> I praktiken ställer du in motringnings-URL:ens **CallbackEndpoint** till den URL som tar emot resultatet av den manuella granskningen (via en HTTP POST-begäran).

Börja med att lägga till följande kod i metoden **Main**.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> Din tjänstnyckel för Content Moderator har en hastighetsgräns för begäranden per sekund (RPS). Om du överskrider gränsen utlöser SDK:n ett undantag med felkoden 429.
>
> En nyckel på den kostnadsfria nivån har en gräns på en RPS.

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

Du kan se följande exempel på utdata i konsolen:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Logga in på Content Moderator-granskningsverktyget för att se den väntande bildgranskningen.

Använd knappen **Nästa** för att skicka.

![Bildgranskning för mänskliga moderatorer](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Se exempelutdata i loggfilen

> [!NOTE]
> I utdatafilen speglar strängarna **Teamname**, **ContentId**, **CallBackEndpoint** och **WorkflowId** de värden som du använde tidigare.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Motringnings-URL tar emot det här svaret om det finns

Ett svar liknande följande returneras:

> [!NOTE]
> I ditt motringningssvar speglar strängarna **ContentId** och **WorkflowId** de värden som du använde tidigare.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Hämta [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för den här och andra Content Moderator-snabbstarter för .NET, och kom igång med din integrering.
