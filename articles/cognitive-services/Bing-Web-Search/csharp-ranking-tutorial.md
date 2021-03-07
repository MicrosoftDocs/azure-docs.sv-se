---
title: Använda rang för att Visa Sök Resultat
titleSuffix: Azure Cognitive Services
description: Visar hur du kan använda Bing RankingResponse-svaret för att Visa Sök resultat i rangordnings ordning.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 907899d8c5be2cbba7ff3eee4a59713718357a5c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430096"
---
# <a name="build-a-console-app-search-client-in-c"></a>Bygg en klients öknings klient i C #

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här självstudien visar hur du skapar en enkel .NET Core-konsol som gör det möjligt för användarna att fråga API för webbsökning i Bing och Visa rankade resultat.

Den här självstudien visar hur du:

- Skapa en enkel fråga till API för webbsökning i Bing
- Visa frågeresultat i rangordnad ordning

## <a name="prerequisites"></a>Förutsättningar

För att följa med i själv studie kursen behöver du:

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
* [Visual Studio IDE](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Skapa ett nytt konsol program projekt

Skapa ett projekt i Visual Studio med `Ctrl`+`Shift`+`N`.

I dialog rutan **nytt projekt** klickar du på **Visual C# > Windows klassisk Desktop > Console-appen (.NET Framework)**.

Ge programmet namnet **MyConsoleSearchApp** och klicka sedan på **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Lägg till JSON.net NuGet-paketet i projektet

Med JSON.net kan du arbeta med de JSON-svar som returneras av API: et. Lägg till dess NuGet-paket i projektet:

- I **Solution Explorer** högerklickar du på projektet och väljer **Hantera NuGet-paket...**.
- Sök efter på fliken  **Bläddra** `Newtonsoft.Json` . Välj den senaste versionen och klicka sedan på **Installera**.
- Klicka på **OK** -knappen i fönstret **Granska ändringar** .
- Stäng Visual Studio-fliken med namnet **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Lägg till en referens i system. Web

Den här självstudien är beroende av `System.Web` sammansättningen. Lägg till en referens till den här sammansättningen i ditt projekt:

- Högerklicka på **referenser** i **Solution Explorer** och välj **Lägg till referens...**
- Välj **sammansättningar > Framework** och rulla sedan ned och kontrol lera **system. Web**
- Välj **OK**

## <a name="add-some-necessary-using-statements"></a>Lägg till några nödvändiga using-instruktioner

Koden i den här självstudien kräver tre ytterligare using-instruktioner. Lägg till dessa uttryck under de befintliga- `using` satserna överst i **program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Fråga användaren om en fråga

Öppna **program.cs** i **Solution Explorer**. Uppdatera `Main()` metoden:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Den här metoden:

- Ber användaren om en fråga
- Anropar `RunQueryAndDisplayResults(userQuery)` för att köra frågan och visa resultaten
- Väntar på användarindata för att förhindra att konsol fönstret stängs omedelbart.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Sök efter frågeresultaten med hjälp av API för webbsökning i Bing

Lägg sedan till en metod som skickar frågor till API: et och visar resultatet:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Den här metoden:

- Skapar en `HttpClient` för att fråga webbsökning-API: et
- Anger det `Ocp-Apim-Subscription-Key` http-huvud som Bing använder för att autentisera begäran
- Kör begäran och använder JSON.net för att deserialisera resultaten
- Anrop `DisplayAllRankedResults(responseObjects)` för att visa alla resultat i rangordnings ordning

Se till att ange värdet för `Ocp-Apim-Subscription-Key` prenumerations nyckeln.

## <a name="display-ranked-results"></a>Visa rankade resultat

Innan du visar hur resultatet visas i rangordningen kan du ta en titt på ett exempel på Webbs öknings svar:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse`JSON-objektet ([dokumentation](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) beskriver lämplig visnings ordning för Sök resultat. Den innehåller en eller flera av följande prioriterade grupper:

- `pole`: Sök resultaten för att få den mest synliga behandlingen (till exempel, som visas ovanför Mainline och sid panelen).
- `mainline`: Sök resultaten visas i Mainline.
- `sidebar`: Sök resultaten visas i sid listen. Om det inte finns någon sidpanel visas resultaten under Mainline.

JSON för ranknings svar kan innehålla en eller flera av grupperna.

I **program.cs** lägger du till följande metod för att visa resultat i korrekt rankad ordning:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Den här metoden:

- Slingor över `rankingResponse` grupper som svaret innehåller
- Visar objekten i varje grupp genom att anropa `DisplaySpecificResults(...)`

I **program.cs** lägger du till följande två metoder:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Dessa metoder fungerar tillsammans för att mata ut Sök resultaten till-konsolen.

## <a name="run-the-application"></a>Kör programmet

Kör appen. Resultatet bör se ut ungefär så här:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [du använder rangordning för att visa resultat](rank-results.md).