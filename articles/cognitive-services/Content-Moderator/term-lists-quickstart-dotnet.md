---
title: Kontrollera text mot en anpassad termlista i C# – Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderera text med anpassade termlistor med hjälp av Content Moderator SDK för C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: a9c64d1a5c4c7ada666b5fe3a8bcc70b39871850
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545632"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Kontrollera texten mot en anpassad termlista i C#

Den standardmässiga globala listan med termer i Azure Content Moderator räcker för de flesta modereringsbehov. Du kan dock behöva kontrollera termer som är specifika för din organisation. Till exempel vill du kanske tagga namn på konkurrenter för vidare granskning. 

Du kan använda [Content Moderator-SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) för att skapa anpassade listor med termer för användning med Text Moderation API.

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med Content Moderator SDK för .NET. Du lär dig bland annat att:
- Skapa en lista.
- Lägga till termer i en lista.
- Kontrollera termer mot termer i en lista.
- Ta bort termer från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 

## <a name="sign-up-for-content-moderator-services"></a>Registrera dig för Content Moderator-tjänster

Innan du kan använda Content Moderator-tjänster via REST-API:n eller SDK:n behöver du en prenumerationsnyckel. Prenumerera på Content Moderator-tjänsten i [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) för att hämta en.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till ett nytt projekt för en **konsolapp (.NET Framework)** i lösningen.

1. Namnge projektet **TermLists**. Välj det här projektet som det enda startprojektet för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket för projektet TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmets using-instruktioner

Lägg till följande `using`-uttryck.

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

Lägg till följande kod för att skapa en Content Moderator-klient för din prenumeration. Uppdatera `AzureEndpoint` fälten och `CMSubscriptionKey` med värdena för slut punkts-URL: en och prenumerations nyckeln. Du hittar dessa på fliken **snabb start** i resursen i Azure Portal.

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

### <a name="add-private-properties"></a>Lägga till privata egenskaper

Lägg till följande privata egenskaper till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Skapa en termlista

Du skapar en termlista med **ContentModeratorClient.ListManagementTermLists.Create**. Den första parametern för **Create** (Skapa) är en sträng som innehåller en MIME-typ som ska vara ”application/json”. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Den andra parametern är en **Body**-objekt som innehåller ett namn och beskrivning för den nya termlistan.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

> [!NOTE]
> Content Moderator-tjänstnyckeln har en gräns för antal begäranden per sekund (RPS). Om du överskrider gränsen genererar SDK:t ett undantag med en 429-felkod. En nyckel på den kostnadsfria nivån har en gräns på en RPS.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Uppdatera termlistans namn och beskrivning

Du uppdaterar termlistans information med **ContentModeratorClient.ListManagementTermLists.Update**. Den första parametern för **Update** (Uppdatera) är termlist-ID. Den andra parametern är en MIME-typ som ska vara ”application/json”. Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Den tredje parametern är ett **Body**-objekt som innehåller det nya namnet och en beskrivning.

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Lägga till en term i en termlista

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Hämta alla termer i en termlista

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Lägg till kod för att uppdatera sökindexet

När du gör ändringar i en termlista kan du uppdatera dess sökindex för att ändringarna ska tas med nästa gång du använder termlistan för att kontrollera text. Detta liknar det sätt som en sökmotor på skrivbordet (om det är aktiverat) eller en webbsökmotor kontinuerligt uppdaterar sitt index för att inkludera nya filer och sidor.

Du uppdaterar sökindex för en termlista med **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Kontrollera text med hjälp av en termlista

Du kontrollerar text med hjälp av en termlista med **ContentModeratorClient.TextModeration.ScreenText**, som använder följande parametrar.

- Språket för termerna i termlistan.
- En MIME-typ som kan vara ”text/html”, ”text/xml”, ”text/markdown” eller ”text/plain”.
- Den text som ska kontrolleras.
- Ett booleskt värde. Ange det här fältet till **true** (sant) för att autokorrigera texten innan du kontrollerar den.
- Ett booleskt värde. Ange det här fältet till **Sant** om du vill identifiera personliga data i texten.
- Termlistans ID.

Mer information finns i [API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** returnerar ett **Screen**-objekt som har en **Terms**-egenskap som visar en lista över alla termer som Content Moderator identifierade i kontrollen. Observera att om Content Moderator inte identifierade några termer under kontrollen har egenskapen **Terms** värdet **null**.

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    read.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Ta bort termer och listor

Det är enkelt att ta bort en term eller en lista. Du använder SDK:n för att utföra följande uppgifter:

- Ta bort en term. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Ta bort alla termer i en lista utan att ta bort listan. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Ta bort en lista och allt dess innehåll. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Ta bort en term

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Ta bort alla termer i en termlista

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Ta bort en termlista

Lägg till följande metoddefinition till namnrymden TermLists, klassen Program.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Skapa main-metoden

Lägg till **huvud** metods definitionen i namn området **TermLists**, klass **program**. Stäng slutligen **program** klassen och **TermLists** -namnområdet.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Visa resultatet genom att köra programmet

Konsolens utdata kommer att se ut ungefär så här:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Nästa steg

Hämta [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för den här och andra Content Moderator-snabbstarter för .NET, och kom igång med din integrering.
