---
title: 'Snabbstart: Anropa din Anpassad Bing-sökning med hjälp av C#-| Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning i C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1d3accdb20073bd1e9b29988b78d7eacd49976b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862317"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Snabbstart: Anropa din Anpassad Bing-sökning med C # 

> [!WARNING]
> Bing-sökning API:er flyttas från Cognitive Services till Bing-sökning Services. Från **och med 30 oktober 2020** måste alla nya instanser av Bing-sökning etableras enligt den process som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing-sökning API:er som etablerats med Cognitive Services kommer att stödjas under de kommande tre åren eller fram till slutet av ditt Enterprise-avtal, beroende på vilket som inträffar först.
> Migreringsanvisningar finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten för att lära dig hur du begär sökresultat från din Anpassad Bing-sökning instans. Även om det här programmet är skrivet i C# är Anpassad Bing-sökning-API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Mer information finns i [Snabbstart: Skapa din första Anpassad Bing-sökning instans](quick-start.md).
- [Microsoft .NET Core](https://dotnet.microsoft.com/download).
- Alla versioner av [Visual Studio 2019 eller senare](https://www.visualstudio.com/downloads/).
- Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
- Det [Anpassad Bing-sökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet-paketet. 

   Installera det här paketet i Visual Studio: 
     1. Högerklicka på projektet i **Solution Explorer** och välj sedan Hantera **NuGet-paket.** 
     2. Sök efter och välj *Microsoft.Azure.CognitiveServices.Search.CustomSearch* och installera sedan paketet.

   När du installerar Anpassad Bing-sökning NuGet-Visual Studio även följande paket:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Lägg sedan till följande paket i projektet:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Skapa följande klasser för att lagra sökresultaten som returneras av Anpassad Bing-sökning API:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. I main-metoden i projektet skapar du följande variabler för din Anpassad Bing-sökning-API-prenumerationsnyckel, sökinstansens anpassade konfigurations-ID och sökterm:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Skapa begärande-URL:en genom att lägga till söktermen i frågeparametern `q=` och sökinstansens anpassade konfigurations-ID till `customconfig=` parametern . Avgränsa parametrarna med ett et-et-an ( `&` ). För variabelvärdet kan du använda den globala slutpunkten i följande kod eller använda den anpassade slutpunkten för underdomänen som visas `url` i Azure Portal för din resurs. [](../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Skapa en begäranklient och lägg till prenumerationsnyckeln i `Ocp-Apim-Subscription-Key`-rubriken.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Utför sökbegäran och få svaret som ett JSON-objekt.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Bearbeta och visa svaren

- Iterera över svarsobjektet för att visa information om varje sökresultat, inklusive dess namn, URL och webbsidans senaste crawlningsdatum.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)