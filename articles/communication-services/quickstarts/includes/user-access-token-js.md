---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: eee020e5d96b301e8278d31c26360639553be0ee
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495345"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera klient biblioteket för Azure Communication Services-identitet för Java Script.

```console

npm install @azure/communication-identity --save

```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

## <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna en ny textfil i kod redigeraren
1. Lägg till ett `require` anrop för att läsa in `CommunicationIdentityClient`
1. Skapa strukturen för programmet, inklusive grundläggande undantags hantering

Använd följande kod för att börja:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Spara den nya filen som **issue-access-token.js** i katalogen *åtkomst-tokens-snabb start* .

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `CommunicationIdentityClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till följande kod i `main`-metoden:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

Du kan också separera slut punkten och åtkomst nyckeln.
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

Om du har konfigurerat identiteter, se [Använd hanterade](../managed-identity.md)identiteter, kan du även autentisera med hanterad identitet.
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Skapa en identitet

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `createUser` metoden för att skapa en ny post i katalogen med en unik `Id` . Lagra mottagen identitet med mappning till programmets användare. Till exempel genom att lagra dem i program serverns databas. Identiteten krävs senare för att utfärda åtkomsttoken.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Utfärda åtkomsttoken

Använd `getToken` metoden för att utfärda en åtkomsttoken för en redan befintlig kommunikations tjänst identitet. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md). En ny instans av parametern `communicationUser` kan konstrueras baserat på en sträng representation av Azure Communication Service-identiteten.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Åtkomsttoken är korta autentiseringsuppgifter som måste återutfärdas. Om du inte gör det kan det orsaka störningar i programmets användar upplevelse. `expiresOn`Egenskapen svar anger livs längden för åtkomsttoken.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Skapa en identitet och utfärda en åtkomsttoken inom samma begäran

Använd `createUserAndToken` metoden för att skapa en kommunikations tjänst identitet och utfärda en åtkomsttoken för den. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await this.client.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Uppdatera åtkomsttoken

Att uppdatera åtkomsttoken är lika enkelt som att anropa `getToken` med samma identitet som användes för att utfärda token. Du måste också ange de `scopes` uppdaterade tokens.

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Återkalla åtkomsttoken

I vissa fall kan du uttryckligen återkalla åtkomsttoken. Till exempel när ett programs användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Metoden `revokeTokens` ogiltig verifiera alla aktiva åtkomsttoken som utfärdats till identiteten.

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Ta bort en identitet

Om du tar bort en identitet återkallar du alla aktiva åtkomsttoken och förhindrar att du utfärdar åtkomsttoken för identiteten. Det tar också bort allt beständigt innehåll som är associerat med identiteten.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Kör koden

Gå till den katalog som innehåller *issue-access-token.js* -filen från en konsol tolk och kör sedan följande `node` kommando för att köra appen.

```console
node ./issue-access-token.js
```
