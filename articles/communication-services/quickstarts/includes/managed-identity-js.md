---
ms.openlocfilehash: a3771a21914831f249696fc3d733c5ea935c2c7e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251456"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Lägg till hanterad identitet i din kommunikations tjänst lösning (JS)

### <a name="install-the-sdk-packages"></a>Installera SDK-paketen

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande `import` direktiv i koden för att använda Azure Identity och Azure Storage SDK: er.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Exemplen nedan använder [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

Ett enkelt sätt att hoppa till med hjälp av hanterad identitets autentisering finns i [ge åtkomst med hanterad identitet](../managed-identity-from-cli.md)

För en mer djupgående titt på hur DefaultAzureCredential-objektet fungerar och hur du kan använda det på sätt som inte anges i den här snabb starten, se [klient bibliotek för Azure Identity för Java Script](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Skapa en identitet och utfärda en token med hanterad identitet

I följande kod exempel visas hur du skapar ett tjänst klient objekt med hanterad identitet och sedan använder klienten för att utfärda en token för en ny användare:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Skicka ett SMS med hanterad identitet

Följande kod exempel visar hur du skapar ett tjänst klient objekt med hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = {
          from: fromNumber,
          to: [toNumber],
          message: message
     };
     const response = await smsClient.send(
          sendRequest,
          {} //Optional SendOptions
          );
}
```