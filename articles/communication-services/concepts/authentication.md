---
title: Autentisera till Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till kommunikations tjänster.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: e20c822c2e792c67ed655080385a3c90794d53fd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545147"
---
# <a name="authenticate-to-azure-communication-services"></a>Autentisera till Azure Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Den här artikeln innehåller information om hur du autentiserar klienter i Azure Communication Services med hjälp av *åtkomst nycklar* och *åtkomsttoken för användar åtkomst*. Varje klient interaktion med Azure Communication Services måste autentiseras.

I följande tabell beskrivs vilka autentiseringsalternativ som stöds av klient biblioteken för Azure Communication Services:

| Klientbibliotek | Åtkomstnyckel    | Token för användar åtkomst |
| -------------- | ------------- | ------------------ |
| Administration | Stöds     | Stöds inte      |
| SMS            | Stöds     | Stöds inte      |
| Chatt           | Stöds inte | Stöds          |
| Sänder        | Stöds inte | Stöds          |

Varje Authorization-alternativ beskrivs kortfattat nedan:

- **Åtkomst nyckel** autentisering för SMS-och administrations åtgärder. Autentisering av åtkomst nycklar är lämpligt för program som körs i en betrodd tjänst miljö. För att autentisera med en åtkomst nyckel genererar en klient en [hash-baserad meddelande verifierings kod (HMAC)](https://en.wikipedia.org/wiki/HMAC) och inkluderar den i `Authorization` rubriken för varje http-begäran. Mer information finns i [autentisera med en åtkomst nyckel](#authenticate-with-an-access-key).
- **Token** -autentisering för användar åtkomst för chatt och uppringning. Token för användar åtkomst gör att klient programmen autentiseras direkt mot Azure Communication Services. Dessa tokens genereras på en server sidans etablerings tjänst för token som du skapar. De tillhandahålls sedan till klient enheter som använder token för att initiera chatten och anropa klient biblioteken. Mer information finns i [autentisera med en token för användar åtkomst](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Autentisera med en åtkomst nyckel

Autentisering med åtkomst nycklar använder en delad hemlig nyckel för att generera en HMAC för varje HTTP-begäran som beräknas med hjälp av SHA256-algoritmen och skickar den i `Authorization` rubriken med `HMAC-SHA256` schemat.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Klient biblioteken för Azure Communication Services som använder autentisering med åtkomst nycklar ska initieras med din resurs anslutnings sträng. Om du inte använder ett klient bibliotek kan du program mässigt generera HMAC med hjälp av resursens åtkomst nyckel. Mer information om anslutnings strängar finns i [snabb start för resurs etablering](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Signera en HTTP-begäran

Om du inte använder ett klient bibliotek för att göra HTTP-förfrågningar till REST-API: er för Azure Communication Services måste du skapa HMAC-meddelanden för varje HTTP-begäran. Följande steg beskriver hur du skapar ett Authorization-huvud:

1. Ange UTC-tidsstämpel (Coordinated Universal Time) för begäran i antingen `x-ms-date` rubriken eller i standard-HTTP- `Date` huvudet. Tjänsten verifierar detta för att skydda mot vissa säkerhets attacker, inklusive repetitions attacker.
1. Hash-meddelandet HTTP-begäran med SHA256-algoritmen och sedan skicka den med begäran via `x-ms-content-sha256` rubriken.
1. Skapa strängen som ska signeras genom att sammanfoga HTTP-verbet (t. ex. `GET` eller `PUT` ), HTTP-begärans sökväg och värden för `Date` `Host` och `x-ms-content-sha256` http-huvuden i följande format:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Generera en HMAC-256-signatur för den UTF-8-kodade sträng som du skapade i föregående steg. Koda sedan resultaten som base64. Observera att du också måste base64-avkoda din åtkomst nyckel. Använd följande format (visas som pseudo-kod):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Ange Authorization-huvudet enligt följande:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Där `<hmac-sha256-signature>` är det HMAC-beräknade i föregående steg.

## <a name="authenticate-with-a-user-access-token"></a>Autentisera med en token för användar åtkomst

Token för användar åtkomst gör att klient programmen autentiseras direkt mot Azure Communication Services. För att uppnå detta bör du ställa in en betrodd tjänst som autentiserar dina program användare och utfärdar användar åtkomst-token med administrations klient biblioteket. Gå till dokumentationen för [klient-och server arkitektur](./client-and-server-architecture.md) och lär dig mer om våra arkitektur överväganden.

`CommunicationTokenCredential`Klassen innehåller logiken för att tillhandahålla autentiseringsuppgifter för användarens åtkomsttoken till klient biblioteken och hantera deras livs cykel.

### <a name="initialize-the-client-libraries"></a>Initiera klient biblioteken

Om du vill initiera klient bibliotek för Azure Communication Services som kräver autentisering med användar åtkomst-token skapar du först en instans av `CommunicationTokenCredential` klassen och använder den för att initiera en API-klient.

Följande kodfragment visar hur du initierar klient biblioteket för chatten med en åtkomsttoken för användare:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
var userCredential = new CommunicationTokenCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance with the AzureCommunicationTokenCredential class
const userCredential = new AzureCommunicationTokenCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
let userCredential = try CommunicationTokenCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
CommunicationTokenCredential userCredential = new CommunicationTokenCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Uppdatera token för användar åtkomst

Token för användar åtkomst är korta autentiseringsuppgifter som måste återutfärdas för att förhindra att användarna upplever avbrott i tjänsten. `CommunicationTokenCredential`Konstruktorn accepterar en funktion för att uppdatera motringning så att du kan uppdatera användarens åtkomsttoken innan de upphör att gälla. Du bör använda det här återanropet för att hämta en ny åtkomsttoken för användare från den betrodda tjänsten.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationTokenCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationTokenCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationTokenCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, token, true);
```
---

Med `refreshProactively` alternativet kan du bestämma hur du ska hantera livs cykeln för token. Som standard när en token är inaktuell blockerar återanropet API-begäranden och försöker uppdatera det. När `refreshProactively` ställs in på `true` motringningen schemaläggs och körs asynkront innan token upphör att gälla.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)

Mer information finns i följande artiklar:
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
