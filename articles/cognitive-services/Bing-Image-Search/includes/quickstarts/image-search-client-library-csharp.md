---
title: Snabb start för Bildsökning i Bing C#-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612662"
---
Använd den här snabb starten för att göra din första avbildnings sökning med hjälp av Bildsökning i Bing klient biblioteket. 

Klient Sök biblioteket är ett omslutnings gränssnitt för REST API och innehåller samma funktioner. 

Du skapar ett C#-program som skickar en bilds öknings fråga, parsar JSON-svaret och visar URL: en för den första bilden som returnerades.

Käll koden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) med ytterligare fel hantering och anteckningar.

## <a name="prerequisites"></a>Krav

* Om du använder Windows, vilken version av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/vs/whatsnew/)
* Om du använder macOS eller Linux, [vs Code](https://code.visualstudio.com) med [.net Core installerat](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [En kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Se även Priser för Cognitive Services –API för Bing-sökning.

## <a name="create-a-console-project"></a>Skapa ett konsol projekt

Börja med att skapa ett nytt C#-konsol program.

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Skapa en ny konsol lösning med namnet *BingImageSearch* i Visual Studio.
    
1. Lägg till [kognitivt bildsökning NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. Högerklicka på ditt projekt i **Solution Explorer**.
    1. Välj **Hantera NuGet-paket**.
    1. Sök efter och välj *Microsoft. Azure. CognitiveServices. search. ImageSearch* och installera sedan paketet.
    
# <a name="vs-code"></a>[VS-kod](#tab/vscode)

1. Öppna terminalfönstret i VS Code.
1. Skapa ett nytt konsol projekt med namnet *BingImageSearch* genom att ange följande kod i terminalfönstret:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Öppna mappen *BingImageSearch* i vs Code.
1. Lägg till [kognitiva bildsökning NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) NuGetPackage genom att ange följande kod i terminalfönstret:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Initiera programmet


1. Ersätt alla `using` instruktioner i *program.cs* med följande kod:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. I `Main` -metoden för ditt projekt skapar du variabler för din giltiga prenumerations nyckel, avbildningen blir returnerad av Bing och en sökterm. Sedan skapa en instans av bildsökningsklienten med hjälp av nyckeln.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Skicka en sökfråga med hjälp av klienten
    
`Main`Använd-klienten fortfarande i-metoden för att söka med en frågetext:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Parsa och visa det första avbildningresultatet

Parsa bild resultatet som returneras i svaret. 

Om svaret innehåller Sök Resultat lagrar du det första resultatet och skriver ut lite information.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om enkel app för bildsökning i Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se även

* [Vad är bildsökning i Bing?](../../overview.md)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [.NET-exempel för Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Dokumentation om Azure Cognitive Services](../../../index.yml)
* [API-referens för bildsökning i Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)