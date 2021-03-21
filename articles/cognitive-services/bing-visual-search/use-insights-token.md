---
title: Använda Insights-token – Visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: Visar hur du använder en bilds Insight-token med API för visuell sökning i Bing för att få insikter om en bild.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 161266a69308175637f5967b2ded48621d4d9c53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424078"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Använd en Insights-token för att få insikter om en bild

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API för visuell sökning i Bing returnerar information om en bild som du anger. Du kan ange bilden med hjälp av dess URL, en insiktstoken eller genom att ladda upp en bild. Mer information om de här alternativen finns i [Vad är API för visuell sökning i Bing?](overview.md). Den här artikeln visar hur du använder en Insights-token. Exempel som visar hur du laddar upp en bild för att få insikter finns i snabb starter:

* ([C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [Node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)).

Om du skickar Visuell sökning i Bing en bildtoken eller URL, visar följande formulär data som du måste inkludera i POSTens brödtext. Formulär data måste innehålla `Content-Disposition` rubriken och du måste ange `name` parametern till "knowledgeRequest". Mer information om `imageInfo` objektet finns i begäran:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

I exemplen i den här artikeln visas hur du använder Insights-token. Du får insikts-token från ett `Image` objekt i ett/images/Search API-svar. Information om hur du hämtar Insights-token finns i [Vad är API för bildsökning i Bing?](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Exempel som använder Insights-token finns i:

* [C#](#use-with-c)

* [Java](#use-with-java)

* [Node.js](#use-with-nodejs)

* [Python](#use-with-python)

## <a name="use-with-c"></a>Använd med C #

### <a name="c-prerequisites"></a>C#-krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
* En version av [Visual Studio 2019](https://www.visualstudio.com/downloads/) för att hämta den här koden som körs i Windows.

## <a name="run-the-application"></a>Kör programmet

Följ dessa steg om du vill köra programmet:

1. Skapa en konsol lösning i Visual Studio.
2. Ersätt innehållet i program. CS med den kod som visas i den här snabb starten.
3. Ersätt värdet `accessKey` med din prenumerationsnyckel.
4. Ersätt `insightsToken` värdet med en Insights-token från ett/images/search-svar.
5. Kör programmet.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Använd med Java

### <a name="java-prerequisites"></a>Java-krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
* [JDK 7 eller 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) för att kompilera och köra den här koden. Du kan använda en Java IDE om du har en favorit, men det räcker med en text redigerare.


## <a name="run-the-java-application"></a>Köra Java-programmet

Följ dessa steg om du vill köra programmet:

1. Hämta eller installera [Gson Java-biblioteket](https://github.com/google/gson). Du kan också hämta Gson via Maven.
2. Skapa ett nytt Java-projekt i dem IDE eller det redigeringsprogram som du föredrar.
3. Lägg till den angivna koden i en fil med namnet `VisualSearch.java`.
4. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
5. Kör programmet.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Använd med Node.js

### <a name="nodejs-prerequisites"></a>Node.js förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
* Du måste ha [Node.js 6](https://nodejs.org/en/download/) för att kunna köra den här koden.

## <a name="run-the-javascript-application"></a>Köra JavaScript-programmet

Följ dessa steg om du vill köra programmet:

1. Skapa en mapp för ditt projekt (eller använd din favorit-IDE eller redigerare).
2. Gå till mappen som du nyss skapade från en kommandotolk eller terminal.
3. Installera de begärda modulerna:
  
   ```
   npm install request  
   ```
1. Installera modulerna för formulärdata:  
   ```
   npm install form-data  
   ```
1. Skapa en fil med namnet GetVisualInsights.js och lägg till nedanstående kod i den.
1. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
1. Kör programmet.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Använd med python

### <a name="python-prerequisites"></a>Python-krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" skapar du en Bing-sökning resurs "  target="_blank"> skapa en Bing-sökning resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
* Du måste ha [python 3](https://www.python.org/) för att köra den här koden.

## <a name="run-the-python-application"></a>Köra python-programmet

Följ dessa steg om du vill köra programmet:

1. Skapa ett nytt Python-projekt i valfri IDE eller redigeringsprogram.
2. Skapa en fil med namnet visualsearch.py och lägg till den kod som visas i den här snabbstarten.
3. Ersätt värdet `SUBSCRIPTION_KEY` med din prenumerationsnyckel.
4. Kör programmet.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# Run the following in a command console window

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# pip3 install requests

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# the imageInsightsToken field in the Image object.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Nästa steg

[Skapa en Visuell sökning webb program med en enda sida](tutorial-bing-visual-search-single-page-app.md)  
[Vad är API:et för visuell sökning i Bing?](overview.md)  
[Testa Cognitive Services](https://aka.ms/bingvisualsearchtryforfree)  
[Avbildningar – Visuell sökning](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)