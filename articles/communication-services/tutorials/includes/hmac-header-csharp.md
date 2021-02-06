---
title: 'Signera en HTTP-begäran med C #'
description: Det här är C#-versionen av signering av en HTTP-begäran med en HMAC-signatur för kommunikations tjänster.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1985ae92b68c16798fc26d7517520c0a70a2ad28
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628361"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:
- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installera [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../quickstarts/create-communication-resource.md). Du måste registrera din **resourceEndpoint** och  **resourceAccessKey** för den här självstudien.



## <a name="sign-an-http-request-with-c"></a>Signera en HTTP-begäran med C #
Autentisering med åtkomst nycklar använder en delad hemlig nyckel för att generera en HMAC-signatur för varje HTTP-begäran. Den här signaturen genereras med SHA256-algoritmen och skickas i `Authorization` rubriken med `HMAC-SHA256` schemat. Exempel:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`Består av: 

- HTTP-verb (t. ex. `GET` eller `PUT` )
- Sökväg för HTTP-begäran
- Datum
- Värd
- x-MS-Content-SHA256

## <a name="setting-up"></a>Konfigurera
Följande steg beskriver hur du skapar ett Authorization-huvud:

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `SignHmacTutorial` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installera paketet

Installera paketet `Newtonsoft.Json` som används för att serialisera brödtext:

```console
dotnet add package Newtonsoft.Json
```

Uppdatera `Main` metod deklarationen för att stödja asynkron kod. Använd följande kod för att börja:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Skapa ett begär ande meddelande

I det här exemplet ska vi signera en begäran om att skapa en ny identitet med hjälp av kommunikations tjänsternas API för autentisering (version `2020-07-20-preview2` )

Lägg till följande kod i- `Main` metoden:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2020-07-20-preview2");
//Endpoint identities?api-version=2020-07-20-preview2 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Ersätt `resourceEndpoint` med ditt verkliga resurs slut punkts värde.

## <a name="create-content-hash"></a>Skapa innehålls-hash

Innehålls-hashen är en del av din HMAC-signatur. Använd följande kod för att beräkna hash för innehållet. Du kan lägga till den här metoden i `Progam.cs` under `Main` metoden.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Beräkna en signatur
Använd följande kod för att skapa en metod för att beräkna en HMAC-signatur.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Ersätt `resourceAccessKey` med åtkomst nyckeln till din riktiga Azure Communication Services-resurs.

## <a name="create-an-authorization-header-string"></a>Skapa en huvud sträng för auktorisering

Nu ska vi skapa strängen som vi ska lägga till i vårt Authorization-huvud:

1. Beräkna en innehålls-hash
2. Ange UTC-tidsstämpeln (Coordinated Universal Time)
3. Förbered en sträng för att signera
4. Beräkna signaturen
5. Sammanfoga strängen som ska användas i Authorization-huvudet
 
Lägg till följande kod i- `Main` metoden:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Lägg till rubriker i requestMessage

Använd följande kod för att lägga till de obligatoriska rubrikerna i din `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testa klienten
Anropa slut punkten med `HttpClient` och kontrol lera svaret.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
