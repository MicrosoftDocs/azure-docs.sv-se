---
title: 'Signera en HTTP-begäran med C #'
description: I den här självstudien beskrivs C#-versionen av signering av en HTTP-begäran med en HMAC-signatur för Azure Communication Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 34c7df2b0e61536c0b5f0bc1e4a97d58d0d9c6a4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490524"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication Services-resurs](../../quickstarts/create-communication-resource.md). Du måste registrera din **resourceEndpoint** och **resourceAccessKey** för den här självstudien.

## <a name="sign-an-http-request-with-c"></a>Signera en HTTP-begäran med C #

Autentisering med åtkomst nycklar använder en delad hemlig nyckel för att generera en HMAC-signatur för varje HTTP-begäran. Den här signaturen genereras med SHA256-algoritmen och skickas i `Authorization` rubriken med hjälp av `HMAC-SHA256` schemat. Exempel:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`Består av:

- HTTP-verb (till exempel `GET` eller `PUT` )
- Sökväg för HTTP-begäran
- Datum
- Värd
- x-MS-Content-SHA256

## <a name="setup"></a>Installation

Följande steg beskriver hur du skapar ett Authorization-huvud.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster, till exempel cmd, PowerShell eller bash, använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `SignHmacTutorial` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Ändra katalogen till mappen nyligen skapade appar. Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installera paketet

Installera det paket `Newtonsoft.Json` som används för att serialisera brödtext.

```console
dotnet add package Newtonsoft.Json
```

Uppdatera `Main` metod deklarationen för att stödja asynkron kod. Använd följande kod för att börja.

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
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Skapa ett begär ande meddelande

I det här exemplet ska vi signera en begäran om att skapa en ny identitet med hjälp av API: et för autentisering av kommunikations tjänster (version `2021-03-07` ).

Lägg till följande kod i metoden `Main`.

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Ersätt `resourceEndpoint` med ditt verkliga resurs slut punkts värde.

## <a name="create-a-content-hash"></a>Skapa en innehålls-hash

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

Ersätt `resourceAccessKey` med en åtkomst nyckel för din Real Communication Services-resurs.

## <a name="create-an-authorization-header-string"></a>Skapa en huvud sträng för auktorisering

Nu ska vi skapa strängen som vi ska lägga till i vårt Authorization-huvud.

1. Beräkna en innehålls-hash.
1. Ange UTC-tidsstämpeln (Coordinated Universal Time).
1. Förbered en sträng för att signera.
1. Beräkna signaturen.
1. Sammanfoga strängen som ska användas i Authorization-huvudet.
 
Lägg till följande kod i metoden `Main`.

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Lägg till rubriker i requestMessage

Använd följande kod för att lägga till de obligatoriska rubrikerna i `requestMessage` .

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("Date", date);
//Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testa klienten

Anropa slut punkten genom att använda `HttpClient` och kontrol lera svaret.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
