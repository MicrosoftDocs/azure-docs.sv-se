---
title: Sök efter GIF-bilder med hjälp av API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Med API för bildsökning i Bing kan du också söka på hela webben efter de mest relevanta. gif-bilderna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341911"
---
# <a name="search-for-gif-images"></a>Sök efter GIF-bilder 

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med API för bildsökning i Bing kan du också söka på hela webben efter de mest relevanta. gif-bilderna.  Utvecklare kan integrera intressanta gif i olika konversations scenarier. 

Följande URL är en fråga för animerade. gif-bilder.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
Parametern [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) anger Sök villkoren.  Den föregående frågan anger även `animatedGif` med hjälp av filter parametern [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) .

Om du vill se exempel på resultat använder du följande URL för att söka i bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Frågeparametrar

Mer information om frågeparametrar och alternativ finns i [bildsökning API-referens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Ett exempel visas under rubriken [exempel Sök efter animerad GIF med Java](#gifExample).

## <a name="tips-and-suggestions"></a>Tips och förslag

- Du kan ange parametrarna [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) och [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) . Vi rekommenderar att du ställer in maxFileSize = 2000000 som merparten av GIF i vårt index är under 2 MB.  Detta hjälper också till att kontrol lera data storleken om bandbredden är ett problem, till exempel i mobila mobil scenarier.
- För att förbättra uppfattandet av prestanda måste du först läsa in miniatyren innan du läser in käll-URL: en.  
- För första körnings-eller landnings sidan där du inte har en användar fråga än, kan du prova med hjälp av vår Trends GIF-bilds ökning för att få hjälp från [API: et för Trends](trending-images.md)ökning
- Det finns tre inställningar för parametern [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) .  `strict`Alternativet blockerar vuxen innehåll.
- Se [mkt](./language-support.md) för en fullständig lista över språk och platser som stöds.
- *AnimatedGifHttps* returnerar endast animerade GIF-bilder från en HTTPS-adress. För säkerhet kräver många program anslutning till externa webb länkar via https. Till exempel kräver Apple App Store anslutning till webb tjänster via HTTPS, som krypterar användar data under överföring.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Exempel sökning efter animerad GIF med Java

Följande URL söker efter animerade. gif-bilder: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Som du ser i följande exempel kräver URL-frågan [OCP-APIM-Subscription-Key-](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) huvud.

Följande Java-exempel skapar och skickar begäran.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Resultat
Koden hämtar följande resultat som JSON-objekt:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](quickstarts/csharp.md)
- [Självstudie Bildsökning ett program med en sida](tutorial-bing-image-search-single-page-app.md)