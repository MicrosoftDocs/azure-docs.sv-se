---
title: Anpassad Bing-sökning snabbstart för C#-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 504057be85902307a121a57dd421254d94167341
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880719"
---
Kom igång med Anpassad Bing-sökning klientbibliotek för C#. Följ de här stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter. Med Anpassad Bing-sökning-API:et kan du skapa skräddarsydda, reklamfria sökupplevelser för ämnen som är viktiga för dig. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Använd Anpassad Bing-sökning för C# för att:
* Hitta sökresultat på webben från din Anpassad Bing-sökning instans.

[Referensdokumentation](/dotnet/api/overview/azure/cognitiveservices/bing-custom-search-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Mer [information finns i Snabbstart: Skapa din Anpassad Bing-sökning instans.](../../quick-start.md)
- Microsoft [.NET Core](https://dotnet.microsoft.com/download)
- Valfri version av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/)
- Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
- Det [Anpassad Bing-sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  NuGet-paketet. 
    - Från **Solution Explorer** i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket** på menyn. Installera paketet `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. När du installerar NuGet-paketet för anpassad sökning installeras även följande sammansättningar:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Lägg sedan till följande paket i projektet.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. I main-metoden för programmet instansierar du sökklienten med din API-nyckel.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Skicka sökbegäran och ta emot ett svar
    
1. Skicka en sökfråga med hjälp av din klients `SearchAsync()`-metod och spara svaret. Ersätt `YOUR-CUSTOM-CONFIG-ID` med instansens konfigurations-ID (du hittar ID:t på [portalen för Anpassad Bing-sökning](https://www.customsearch.ai/)). Det här exemplet söker efter ”Xbox”.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()`-metoden returnerar ett `WebData`-objekt. Använd objektet för att iterera genom eventuella `WebPages` som hittades. Den här koden hittar det första webbsidesresultatet och skriver ut webbsidans `Name` och `URL`.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)
