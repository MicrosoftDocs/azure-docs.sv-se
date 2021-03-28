---
ms.openlocfilehash: 0f775f97a204bc758e632c92702b93227b47bc15
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645415"
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

Information om hur du registrerar program i utvecklings miljön och konfigurerar miljövariabler finns i [bevilja åtkomst med hanterad identitet](../managed-identity-from-cli.md)  

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

