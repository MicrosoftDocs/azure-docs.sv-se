---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033471"
---
## <a name="authentication"></a>Autentisering

Varje begäran kräver ett Authorization-huvud. I den här tabellen visas vilka huvuden som stöds för varje tjänst:

| Begärandehuvuden som stöds | Tal till text | Text till tal |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Ja |
| Auktorisering: Bearer | Ja | Ja |

När du använder `Ocp-Apim-Subscription-Key` -huvudet behöver du bara ange din prenumerations nyckel. Exempel:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

När du använder `Authorization: Bearer` sidhuvudet måste du göra en begäran till `issueToken` slut punkten. I den här förfrågan utbyter du din prenumerations nyckel för en åtkomsttoken som är giltig i 10 minuter. I följande avsnitt får du lära dig hur du hämtar en token och använder en token.

### <a name="how-to-get-an-access-token"></a>Så här hämtar du en åtkomsttoken

Om du vill hämta en åtkomsttoken måste du göra en begäran till `issueToken` slut punkten med hjälp av `Ocp-Apim-Subscription-Key` och din prenumerations nyckel.

`issueToken`Slut punkten har följande format:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Ersätt `<REGION_IDENTIFIER>` med den identifierare som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Använd de här exemplen för att skapa en begäran om åtkomst-token.

#### <a name="http-sample"></a>HTTP-exempel

Det här exemplet är en enkel HTTP-begäran för att hämta en token. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerations nyckel för röst tjänsten. Om din prenumeration inte finns i regionen Västra USA ersätter du `Host` rubriken med din regions värdnamn.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Bröd texten i svaret innehåller åtkomst-token i JSON Web Token-formatet (JWT).

#### <a name="powershell-sample"></a>PowerShell-exempel

Det här exemplet är ett enkelt PowerShell-skript för att hämta en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerations nyckel för röst tjänsten. Se till att använda rätt slut punkt för den region som matchar din prenumeration. Det här exemplet är för närvarande inställt på västra USA.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Spiral exempel

Sväng är ett kommando rads verktyg som är tillgängligt i Linux (och i Windows-undersystemet för Linux). Detta spiral kommando visar hur du hämtar en åtkomsttoken. Ersätt `YOUR_SUBSCRIPTION_KEY` med din prenumerations nyckel för röst tjänsten. Se till att använda rätt slut punkt för den region som matchar din prenumeration. Det här exemplet är för närvarande inställt på västra USA.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-exempel

Den här C#-klassen illustrerar hur du får en åtkomsttoken. Skicka din prenumerations nyckel för röst tjänsten när du instansierar klassen. Om din prenumeration inte finns i regionen USA, västra, ändrar du värdet för för `FetchTokenUri` att matcha regionen för din prenumeration.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python-exempel

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Så här använder du en åtkomsttoken

Åtkomsttoken ska skickas till tjänsten som `Authorization: Bearer <TOKEN>` rubrik. Varje åtkomsttoken är giltig i 10 minuter. Du kan när som helst hämta en ny token för att minimera nätverks trafiken och svars tiden, men vi rekommenderar att du använder samma token i nio minuter.

Här är ett HTTP-exempel på tal-till-text-REST API för kort ljud:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
