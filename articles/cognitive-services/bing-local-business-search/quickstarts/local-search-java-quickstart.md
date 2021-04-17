---
title: Snabbstart – Skicka en fråga till API:et med Java – Bing-företagssökning i närområde
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att börja skicka begäranden i Java Bing-företagssökning i närområde API, som är en Azure Cognitive Service.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 001fc80b30eaa736db27ba76384aaf273bdec903
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536661"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Snabbstart: Skicka en fråga Bing-företagssökning i närområde API:et med Java

> [!WARNING]
> Bing-sökning API:er flyttas från Cognitive Services till Bing-sökning Services. Från **och med 30 oktober 2020** måste alla nya instanser av Bing-sökning etableras enligt den process som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing-sökning API:er som etablerats med Cognitive Services kommer att stödjas under de kommande tre åren eller fram till slutet av ditt Enterprise-avtal, beroende på vilket som inträffar först.
> Migreringsanvisningar finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten för att lära dig hur du skickar begäranden Bing-företagssökning i närområde API: et, som är en Azure Cognitive Service. Även om det här enkla programmet är skrivet i Java är API:et en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON.

Det här exempelprogrammet hämtar lokala svarsdata från API:et för en sökfråga.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* När du har din Azure-prenumeration skapar du en Bing-sökning-resurs för att skapa Bing-sökning resurs i Azure Portal för att hämta <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> din nyckel och </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.

## <a name="create-the-request"></a>Skapa begäran 

Följande kod skapar en `WebRequest` , anger åtkomstnyckelrubriken och lägger till en frågesträng *för hotellet i Bellevue*.  Därefter skickar den en begäran och tilldelar svaret till en sträng som ska innehålla JSON-texten.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>Kör det fullständiga programmet

Följande kod använder API:Bing-företagssökning i närområde för att returnera sökresultat från Bing-sökmotorn. Kör den här koden genom att följa dessa steg:
1. Hämta eller installera gson-biblioteket.
2. Skapa ett nytt Java-projekt i dem IDE eller det redigeringsprogram som du föredrar.
3. Lägg till koden nedan.
4. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
5. Kör programmet.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
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
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

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

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>Nästa steg
- [C#-snabbstart för sökning efter lokala företag](local-quickstart.md)
- [Snabbstart för sökning efter Node.js företag](local-search-node-quickstart.md)
- [Snabbstart för sökning efter lokala företag i Python](local-search-python-quickstart.md)
