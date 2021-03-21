---
title: Azure App konfiguration REST API-HMAC-autentisering
description: Använd HMAC för att autentisera till Azure App konfiguration med hjälp av REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: d5b5470b30848fd31be63f25c85c23f88cdaf0c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732232"
---
# <a name="hmac-authentication---rest-api-reference"></a>HMAC-autentisering – REST API referens

Du kan autentisera HTTP-begäranden med hjälp av autentiseringsschemat HMAC-SHA256. (HMAC refererar till hash-baserad meddelande kod.) Dessa begär Anden måste överföras via TLS.

## <a name="prerequisites"></a>Förutsättningar

- **Certifiering** - \<Access Key ID\>
- **Hemligt** Base64-kodat åtkomst nyckel värde. ``base64_decode(<Access Key Value>)``

Värdena för Credential (även kallat `id` ) och hemlighet (även kallade `value` ) måste hämtas från instansen av Azure App-konfigurationen. Du kan göra detta med hjälp av [Azure Portal](https://portal.azure.com) eller [Azure CLI](/cli/azure/).

Ange varje begäran med alla HTTP-huvuden som krävs för autentisering. Minimi kravet är:

|  Begärandehuvud | Beskrivning  |
| --------------- | ------------ |
| **Värd** | Internet värd och port nummer. Mer information finns i avsnitt  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2). |
| **Datum** | Datum och tid då begäran kom. Den får inte vara mer än 15 minuter från den aktuella koordinerade universella tiden (Greenwich Mean Time). Värdet är ett HTTP-datum enligt beskrivningen i avsnitt [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1).
| **x-MS-date** | Samma som ```Date``` ovan. Du kan använda den i stället när agenten inte kan komma åt ```Date``` begär ande huvudet direkt, eller om en proxy ändrar den. Om ```x-ms-date``` och ```Date``` båda anges, ```x-ms-date``` prioriteras. |
| **x-MS-Content-SHA256** | Base64-kodad SHA256-hash för begär ande texten. Det måste anges även om det inte finns någon brödtext. ```base64_encode(SHA256(body))```|
| **Auktorisering** | Autentiseringsinformation som krävs av HMAC-SHA256-schemat. Format och information beskrivs senare i den här artikeln. |

**Exempel:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>Authorization-huvud

### <a name="syntax"></a>Syntax

``Authorization``: **HMAC-SHA256**```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  Argument | Beskrivning  |
| ------ | ------ |
| **HMAC-SHA256** | Authorization-schema. _kunna_ |
| **Autentiseringsuppgift** | ID för den åtkomst nyckel som används för att beräkna signaturen. _kunna_ |
| **SignedHeaders** | HTTP-begärandehuvuden har lagts till i signaturen. _kunna_ |
| **Signatur** | Base64-kodad HMACSHA256 för sträng till tecken. _kunna_|

### <a name="credential"></a>Autentiseringsuppgift

ID för den åtkomst nyckel som används för att beräkna signaturen.

### <a name="signed-headers"></a>Signerade rubriker

Rubrik namn för HTTP-begäran, avgränsade med semikolon, som krävs för att signera begäran. Dessa HTTP-huvuden måste också anges korrekt med begäran. Använd inte blank steg.

### <a name="required-http-request-headers"></a>Obligatoriska HTTP-begärandehuvuden

```x-ms-date```[eller ```Date``` ]; ```host``` ;```x-ms-content-sha256```

Alla andra HTTP-begärandehuvuden kan också läggas till i signeringen. Lägg bara till dem i ```SignedHeaders``` argumentet.

**Exempel:**

x-MS-date; värd; x-MS-Content-SHA256; ```Content-Type``` ;```Accept```

### <a name="signature"></a>Signatur

Base64-kodad HMACSHA256-hash för strängen-till-Sign. Den använder åtkomst nyckeln som identifieras av `Credential` .
```base64_encode(HMACSHA256(String-To-Sign, Secret))```

### <a name="string-to-sign"></a>Sträng-till-tecken

Det är en kanonisk representation av begäran:

_Sträng-till-tecken =_

**HTTP_METHOD** + \n + **path_and_query** + \n + **signed_headers_values**

|  Argument | Beskrivning  |
| ------ | ------ |
| **HTTP_METHOD** | Versaler HTTP-metod namn som används med begäran. Mer information finns i [avsnitt 9](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). |
|**path_and_query** | Sammanfogning av begär ande absolut URI-sökväg och frågesträng. Mer information finns i [avsnitt 3,3](https://tools.ietf.org/html/rfc3986#section-3.3).
| **signed_headers_values** | Semikolonavgränsade värden för alla HTTP-begärandehuvuden som anges i `SignedHeaders` . Formatet följer `SignedHeaders` semantik. |

**Exempel:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Fel

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Orsak:** Huvud för auktoriseringsbegäran med HMAC-SHA256-schema har inte angetts.

**Lösning:** Ange ett giltigt ```Authorization``` http-begär ande huvud.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**Orsak:** ```Date``` eller ```x-ms-date``` begär ande huvudet är över 15 minuter av från den aktuella koordinerade universella tiden (Greenwich Mean Time).

**Lösning:** Ange korrekt datum och tid.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**Orsak:** Saknad eller ogiltig ```Date``` eller ```x-ms-date``` begär ande rubrik.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**Orsak:** En obligatorisk parameter saknas från ```Authorization``` begär ande huvudet.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**Orsak:** Det angivna [ ```Host``` ]/[åtkomst nyckel-ID: t] hittades inte.

**Lösning:** Kontrol lera ```Credential``` parametern för ```Authorization``` begär ande huvudet. Se till att det är ett giltigt ID för åtkomst nyckel och se till att ```Host``` sidhuvudet pekar på det registrerade kontot.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**Orsak:** Det ```Signature``` angivna är inte detsamma som servern förväntar sig.

**Lösning:** Kontrol lera att ```String-To-Sign``` är korrekt. Kontrol lera att ```Secret``` är korrekt och korrekt använda (base64 avkodat innan du använder).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**Orsak:** Begär ande huvudet som krävs av ```SignedHeaders``` parametern i  ```Authorization``` rubriken saknas.

**Lösning:** Ange det obligatoriska huvudet med rätt värde.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**Orsak:** Saknad parameter i ```SignedHeaders``` .

**Lösning:** Kontrol lera minimi kraven för signerade huvuden.

## <a name="code-snippets"></a>Kodfragment

### <a name="javascript"></a>JavaScript

*Krav*: [kryptografi-JS](https://code.google.com/archive/p/crypto-js/)

```js
function signRequest(host, 
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = 
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, byte[] secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(secret))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret) 
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body 
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*Krav*:

| Förutsättning | Kommando | Testade versioner |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | TR | 8,28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [OpenSSL](https://www.openssl.org/) | openssl | 1.1.0 g, 1.1.1 a |
| [util – linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```
