---
ms.openlocfilehash: ce29158c2a44f01fa06649e4b4497bf5c5076866
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513186"
---
## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Öppna terminal- eller kommandofönstret och skapa en ny katalog för appen och navigera till den.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

Kör `npm init -y` för att skapapackage.js **fil** med standardinställningar.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Installera SDK-paketen

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Skapa en ny fil

Öppna en ny fil med en textredigerare och spara den som . Vi `index.js` placerar koden i den här filen.

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande `require` -direktiv överst i för `index.js` att använda Azure Identity Azure Storage-SDK:er.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>Skapa en DefaultAzureCredential

Vi använder [DefaultAzureCredential för den](/javascript/api/@azure/identity/defaultazurecredential) här snabbstarten. Den här autentiseringsidentifieraren är lämplig för produktions- och utvecklingsmiljöer. Eftersom det behövs för varje åtgärd ska vi skapa den högst upp i `index.js` filen. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Skapa en identitet och utfärda en token med hanterade identiteter

Nu ska vi skriva en funktion som skapar en ny identitet och utfärdar en token för den här identiteten. Vi använder den senare för att testa konfigurationen av den hanterade identiteten.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>Skicka ett SMS med hanterad identitet

Nu ska vi skriva en funktion som använder hanterade identiteter för att skicka ett SMS:

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-function"></a>Skriva main-funktionen

Nu när våra funktioner har skapats kan vi skriva en huvudfunktion för att anropa dem och demonstrera användningen av hanterade identiteter:
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

Den slutliga `index.js` filen bör se ut så här:
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>Köra programmet

När allt är klart kan du köra filen genom `node index.js` att ange från projektets katalog. Om allt gick bra bör du se något som liknar följande.

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
