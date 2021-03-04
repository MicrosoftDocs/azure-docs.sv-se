---
title: API för Azure Monitor HTTP-datainsamling | Microsoft Docs
description: 'Du kan använda API: et Azure Monitor HTTP-datainsamling för att lägga till POST-JSON-data till en Log Analytics arbets yta från vilken klient som helst som kan anropa REST API. Den här artikeln beskriver hur du använder API: et och innehåller exempel på hur du publicerar data med hjälp av olika programmeringsspråk.'
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: 8c5b4eac17b4c7ae9b4081d0eb9118285dc74bd5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030978"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Skicka loggdata till Azure Monitor med API: t för HTTP-datainsamling (offentlig för hands version)
Den här artikeln visar hur du använder API: t för HTTP-datainsamling för att skicka logg data till Azure Monitor från en REST API-klient.  Här beskrivs hur du formaterar data som samlats in av ditt skript eller program, inkluderar dem i en begäran och har den begäran som auktoriserats av Azure Monitor.  Exempel finns för PowerShell, C# och python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> API: et för Azure Monitor HTTP-datacollector finns i offentlig för hands version.

## <a name="concepts"></a>Begrepp
Du kan använda API: t för HTTP-datainsamling för att skicka loggdata till en Log Analytics arbets yta i Azure Monitor från alla klienter som kan anropa en REST API.  Detta kan vara en Runbook i Azure Automation som samlar in hanterings data från Azure eller ett annat moln, eller så kan det vara ett annat hanterings system som använder Azure Monitor för att konsolidera och analysera loggdata.

Alla data i arbets ytan Log Analytics lagras som en post med en viss post typ.  Du formaterar dina data så att de skickas till API: et för HTTP-datainsamling som flera poster i JSON.  När du skickar data skapas en enskild post i lagrings platsen för varje post i nytto lasten för begäran.


![Översikt över HTTP-datainsamling](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Skapa en begäran
Om du vill använda API: et för HTTP-datainsamling skapar du en POST-begäran som innehåller de data som ska skickas i JavaScript Object Notation (JSON).  I följande tre tabeller visas de attribut som krävs för varje begäran. Vi beskriver varje attribut mer detaljerat längre fram i artikeln.

### <a name="request-uri"></a>URI för förfrågan
| Attribut | Egenskap |
|:--- |:--- |
| Metod |POST |
| URI |https:// \<CustomerId\> . ods.OpInsights.Azure.com/API/logs?API-version=2016-04-01 |
| Innehållstyp |application/json |

### <a name="request-uri-parameters"></a>Begär URI-parametrar
| Parameter | Beskrivning |
|:--- |:--- |
| CustomerID |Den unika identifieraren för Log Analytics arbets ytan. |
| Resurs |API-resursens namn:/API/logs. |
| API-version |Den version av API: et som ska användas med den här begäran. För närvarande är det 2016-04-01. |

### <a name="request-headers"></a>Begärandehuvuden
| Huvud | Beskrivning |
|:--- |:--- |
| Auktorisering |Signaturen för auktorisering. Senare i artikeln kan du läsa om hur du skapar ett HMAC-SHA256-huvud. |
| Log-Type |Ange post typen för de data som skickas. Får bara innehålla bokstäver, siffror och under streck (_) och får inte överstiga 100 tecken. |
| x-MS-date |Datumet då begäran bearbetades i RFC 1123-format. |
| x-MS-AzureResourceId | Resurs-ID för den Azure-resurs som data ska associeras med. Detta fyller i egenskapen [_ResourceId](./log-standard-columns.md#_resourceid) och gör att data kan tas med i [resurs kontext](./design-logs-deployment.md#access-mode) frågor. Om det här fältet inte anges tas data inte med i resurs kontext frågor. |
| tidsgenererat-fält | Namnet på ett fält i data som innehåller tidsstämpeln för dataobjektet. Om du anger ett fält används dess innehåll för **TimeGenerated**. Om det här fältet inte anges är standardvärdet för **TimeGenerated** den tidpunkt då meddelandet matas in. Innehållet i meddelande fältet ska följa ISO 8601-formatet ÅÅÅÅ-MM-DDThh: mm: ssZ. |

## <a name="authorization"></a>Auktorisering
Alla förfrågningar till API: et för Azure Monitor HTTP-datainsamling måste innehålla ett Authorization-huvud. Om du vill autentisera en begäran måste du signera begäran med antingen den primära eller sekundära nyckeln för arbets ytan som gör begäran. Sedan skickar du signaturen som en del av begäran.   

Här är formatet för Authorization-huvudet:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* är den unika identifieraren för Log Analytics arbets ytan. *Signaturen* är en [Hash-baserad Message Authentication Code (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) som är konstruerad från begäran och sedan beräknas med hjälp av [SHA256-algoritmen](/dotnet/api/system.security.cryptography.sha256). Sedan kodar du den med base64-kodning.

Använd det här formatet för att koda signatur strängen **SharedKey** :

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Här är ett exempel på en signatur sträng:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

När du har signatur strängen kodar du den med hjälp av HMAC-SHA256-algoritmen på UTF-8-kodad sträng och kodar sedan resultatet som base64. Använd det här formatet:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Exemplen i följande avsnitt innehåller exempel kod som hjälper dig att skapa ett Authorization-huvud.

## <a name="request-body"></a>Begärandetext
Meddelandets brödtext måste vara i JSON. Den måste innehålla en eller flera poster med egenskaps namn och värdepar i följande format. Egenskaps namnet får bara innehålla bokstäver, siffror och under streck (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Du kan gruppera flera poster tillsammans i en enskild begäran med hjälp av följande format. Alla poster måste vara av samma post typ.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Post typ och egenskaper
Du definierar en anpassad posttyp när du skickar data via Azure Monitor API för HTTP-datainsamling. För närvarande kan du inte skriva data till befintliga post typer som har skapats av andra data typer och lösningar. Azure Monitor läser inkommande data och skapar sedan egenskaper som matchar data typerna för de värden som du anger.

Varje begäran till data insamlings-API: n måste innehålla ett **logg typs** huvud med namnet på post typen. Suffixet **_CL** läggs automatiskt till det namn som du anger för att skilja det från andra logg typer som en anpassad logg. Om du till exempel anger namnet **MyNewRecordType** skapar Azure Monitor en post med typen **MyNewRecordType_CL**. Detta säkerställer att det inte finns några konflikter mellan användardefinierade typnamn och de som levererats i aktuella eller framtida Microsoft-lösningar.

Azure Monitor lägger till ett suffix till egenskaps namnet för att identifiera en egenskaps datatyp. Om en egenskap innehåller ett null-värde ingår inte egenskapen i posten. Den här tabellen visar data typen för egenskapen och motsvarande suffix:

| Egenskaps data typ | Huvudnamnssuffix |
|:--- |:--- |
| Sträng |_s |
| Boolesk |_b |
| Double |_d |
| Datum/tid |_t |
| GUID (lagras som en sträng) |_g |

> [!NOTE]
> Sträng värden som verkar vara GUID får _g suffixet och formateras som ett GUID, även om det inkommande värdet inte innehåller några bindestreck. Till exempel kommer både "8145d822-13a7-44ad-859c-36f31a84f6dd" och "8145d82213a744ad859c36f31a84f6dd" att lagras som "8145d822-13a7-44ad-859c-36f31a84f6dd". De enda skillnaderna mellan detta och en annan sträng är _g i namn och infogning av streck om de inte finns i indata. 

Vilken datatyp som Azure Monitor används för varje egenskap beror på om post typen för den nya posten redan finns.

* Om post typen inte finns skapar Azure Monitor en ny med hjälp av JSON-typens härledning för att fastställa data typen för varje egenskap för den nya posten.
* Om post typen finns Azure Monitor försöker skapa en ny post utifrån befintliga egenskaper. Om data typen för en egenskap i den nya posten inte matchar och inte kan konverteras till den befintliga typen, eller om posten innehåller en egenskap som inte finns, skapar Azure Monitor en ny egenskap som har det relevanta suffixet.

Denna överförings post skapar till exempel en post med tre egenskaper, **number_d**, **boolean_b** och **string_s**:

![Exempel post 1](media/data-collector-api/record-01.png)

Om du sedan skickat in nästa post med alla värden formaterade som strängar, ändras inte egenskaperna. Dessa värden kan konverteras till befintliga data typer:

![Exempel post 2](media/data-collector-api/record-02.png)

Men om du sedan gör nästa överföring skapar Azure Monitor de nya egenskaperna **boolean_d** och **string_d**. Dessa värden kan inte konverteras:

![Exempel post 3](media/data-collector-api/record-03.png)

Om du sedan skickade följande post innan post typen skapades, skulle Azure Monitor skapa en post med tre egenskaper, **number_s**, **boolean_s** och **string_s**. I den här posten formateras var och en av de ursprungliga värdena som en sträng:

![Exempel post 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Reserverade egenskaper
Följande egenskaper är reserverade och ska inte användas i en anpassad posttyp. Du får ett fel meddelande om nytto lasten innehåller något av dessa egenskaps namn.

- tenant

## <a name="data-limits"></a>Databegränsningar
Det finns vissa begränsningar kring de data som skickas till API: et för Azure Monitor data insamling.

* Högst 30 MB per post till Azure Monitor API för data insamling. Detta är en storleks gräns för ett enskilt inlägg. Om data från ett enda inlägg som överstiger 30 MB, bör du dela upp data upp till mindre segment och skicka dem samtidigt.
* Högst 32 kB för fältvärden. Om fältvärdet är större än 32 kB kommer data att trunkeras.
* Det rekommenderade maximala antalet fält för en specifik typ är 50. Detta är en praktisk gräns med tanke på användbarhet och sökfunktion.  
* En tabell i en Log Analytics-arbetsyta stöder endast upp till 500 kolumner (kallas för ett fält i den här artikeln). 
* Det maximala antalet tecken för kolumnnamnet är 500.

## <a name="return-codes"></a>Returkoder
HTTP-statuskod 200 innebär att begäran har tagits emot för bearbetning. Detta anger att åtgärden har slutförts.

Den här tabellen innehåller en fullständig uppsättning status koder som tjänsten kan returnera:

| Kod | Status | Felkod | Beskrivning |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Begäran har godkänts. |
| 400 |Felaktig begäran |InactiveCustomer |Arbets ytan har stängts. |
| 400 |Felaktig begäran |InvalidApiVersion |Den angivna API-versionen kändes inte igen av tjänsten. |
| 400 |Felaktig begäran |InvalidCustomerId |Det angivna arbetsyte-ID: t är ogiltigt. |
| 400 |Felaktig begäran |InvalidDataFormat |Ogiltig JSON skickades. Svars texten kan innehålla mer information om hur du löser problemet. |
| 400 |Felaktig begäran |InvalidLogType |Logg typen som anges innehåller specialtecken eller siffror. |
| 400 |Felaktig begäran |MissingApiVersion |Ingen API-version har angetts. |
| 400 |Felaktig begäran |MissingContentType |Innehålls typen har inte angetts. |
| 400 |Felaktig begäran |MissingLogType |Den obligatoriska värde logg typen har inte angetts. |
| 400 |Felaktig begäran |UnsupportedContentType |Innehålls typen har inte ställts in på **Application/JSON**. |
| 403 |Förbjudet |InvalidAuthorization |Tjänsten kunde inte autentisera begäran. Kontrol lera att arbetsyte-ID och anslutnings nyckel är giltiga. |
| 404 |Hittades inte | | Antingen är den angivna URL: en felaktig eller så är begäran för stor. |
| 429 |För många begär Anden | | Tjänsten har en stor mängd data från ditt konto. Försök att utföra begäran senare. |
| 500 |Internt Server fel |UnspecifiedError |Ett internt fel inträffade i tjänsten. Försök att utföra begäran igen. |
| 503 |Tjänsten är inte tillgänglig |ServiceUnavailable |Tjänsten är för närvarande inte tillgänglig för att ta emot begär Anden. Försök att utföra begäran igen. |

## <a name="query-data"></a>Söka i data
Om du vill fråga efter data som skickats av Azure Monitor HTTP-API för data insamling söker du efter poster med en **typ** som är lika med det **LogType** -värde som du angav, sist i **_CL**. Om du till exempel använde **MyCustomLog** returnerar du alla poster med `MyCustomLog_CL` .

## <a name="sample-requests"></a>Exempel förfrågningar
I nästa avsnitt hittar du exempel på hur du skickar data till API: et för Azure Monitor HTTP-datainsamling genom att använda olika programmeringsspråk.

Utför följande steg för varje exempel för att ange variabler för Authorization-huvudet:

1. Leta upp Log Analytics arbets ytan i Azure Portal.
2. Välj **agent hantering**.
2. Till höger om **arbetsyte-ID** väljer du kopierings ikonen och klistrar in ID: t som värdet för variabeln **kund-ID** .
3. Till höger om **primär nyckel** väljer du kopierings ikonen och klistrar sedan in ID: t som värdet för den **delade nyckel** variabeln.

Du kan också ändra variablerna för logg typen och JSON-data.

### <a name="powershell-sample"></a>PowerShell-exempel
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-exempel
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Exempel på python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Python 3-exempel
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java-exempel

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternativ och överväganden
Även om data insamlings-API: n ska innehålla de flesta av dina behov av att samla in information om fritt formulär till Azure-loggar, finns det instanser där ett alternativ kan krävas för att lösa vissa begränsningar i API: et. Alla alternativ är följande: viktiga överväganden ingår:

| Andra | Beskrivning | Passar bäst för |
|---|---|---|
| [Anpassade händelser](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): inbyggd SDK-baserad inmatning i Application Insights | Application Insights, vanligt vis genom ett SDK i ditt program, ger dig möjlighet att skicka anpassade data via anpassade händelser. | <ul><li> Data som genereras i programmet, men som inte hämtats av SDK via någon av standard data typerna (begär Anden, beroenden, undantag och så vidare).</li><li> Data som ofta korreleras med andra program data i Application Insights </li></ul> |
| API för data insamling i Azure Monitor loggar | API för data insamling i Azure Monitor loggar är ett helt öppet sätt att mata in data. Alla data som är formaterade i ett JSON-objekt kan skickas hit. När den har skickats bearbetas den och är tillgänglig i loggarna för att korreleras med andra data i loggarna eller mot andra Application Insights data. <br/><br/> Det är ganska enkelt att överföra data som filer till en Azure blob-blob, från var de här filerna ska bearbetas och överföras till Log Analytics. I [den här](./create-pipeline-datacollector-api.md) artikeln hittar du en exempel implementering av en sådan pipeline. | <ul><li> Data som inte nödvändigt vis genereras inom ett program som är instrumenterade i Application Insights.</li><li> Exempel är lookup-och fakta tabeller, referens data, församlad statistik och så vidare. </li><li> Avsedd för data som ska refereras till i andra Azure Monitor data (Application Insights, andra loggar data typer, Security Center, behållar insikter/virtuella datorer osv.). </li></ul> |
| [Azure-datautforskaren](/azure/data-explorer/ingest-data-overview) | Azure Datautforskaren (ADX) är den data plattform som ger Application Insights analys-och Azure Monitors loggar. Nu är det allmänt tillgängligt ("GA"), med hjälp av data plattformen i sin RAW-form, och ger dig fullständig flexibilitet (men kräver hanterings belastning) över klustret (Kubernetes RBAC, bevarande frekvens, schema och så vidare). ADX tillhandahåller många [](/azure/data-explorer/ingest-data-overview#ingestion-methods) inmatnings alternativ [, till exempel CSV-, TSV-och JSON-](/azure/kusto/management/mappings?branch=master) filer. | <ul><li> Data som inte kommer att korreleras till andra data under Application Insights eller loggar. </li><li> Data som kräver avancerade inmatnings-eller bearbetnings funktioner som inte redan finns i Azure Monitor loggar. </li></ul> |


## <a name="next-steps"></a>Nästa steg
- Använd [API för loggs ökning](./log-query-overview.md) för att hämta data från Log Analytics arbets ytan.

- Läs mer om hur du [skapar en datapipeline med data insamlings-API: et](create-pipeline-datacollector-api.md) med hjälp av Logic Apps arbets flöde till Azure Monitor.