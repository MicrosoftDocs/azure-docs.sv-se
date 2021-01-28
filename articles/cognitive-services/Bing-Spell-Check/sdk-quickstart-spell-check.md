---
title: 'Snabb start: kontrol lera stavningen med Stavningskontroll i Bing SDK för C #'
titleSuffix: Azure Cognitive Services
description: Kom igång med REST API för stavningskontroll i Bing för att kontrollera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 5194dab21842e47d2bf2445c69ccaeec3cb78e4f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943422"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing SDK för C #

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten för att börja kontrollera stavning med SDK för stavningskontroll i Bing för C#. Även om Stavningskontroll i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Programberoenden

* En version av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/).
* [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) för Stavningskontroll i Bing

Om du vill lägga till Stavningskontroll i Bing SDK i projektet väljer du **Hantera NuGet-paket** från **Solution Explorer** i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Paketet installerar även följande beroenden:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande `using`-instruktion.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Skapa en ny klass. Skapa sedan en asynkron funktion som heter `SpellCheckCorrection()` och tar en prenumerationsnyckel och skickar begäran om stavningskontroll.

3. Instansiera klienten genom att skapa ett nytt `ApiKeyServiceClientCredentials`-objekt. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Skicka begäran och läsa svaret

1. I funktionen ovan utför du följande steg. Skicka begäran om stavningskontroll med klienten. Lägg till den text som ska kontrolleras i parametern `text` och ange läget till `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Hämta det första resultatet av stavningskontrollen om det finns ett sådant. Skriv ut det första felstavade ord (token) som returnerats, tokentypen samt antalet förslag.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Hämta den första föreslagna korrigeringen, om det finns en sådan. Skriv ut förslags poängen och det föreslagna ordet. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Kör programmet

Skapa och kör ditt projekt. Om du använder Visual Studio trycker du på **F5** för att felsöka filen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](tutorials/spellcheck.md)

- [Vad är API för stavningskontroll i Bing?](overview.md)
- [Referens guide för Stavningskontroll i Bing C# SDK](/dotnet/api/overview/azure/cognitiveservices/bing-spell-check-readme)
