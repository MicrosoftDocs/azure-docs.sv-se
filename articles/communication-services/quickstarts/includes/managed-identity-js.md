---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657665"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Lägg till hanterad identitet i din kommunikations tjänst lösning (JS)

### <a name="install-the-client-library-packages"></a>Installera klient biblioteks paketen

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Använda klient biblioteks paketen

Lägg till följande `import` direktiv i koden för att använda Azure-identiteten och Azure Storage klient biblioteken.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

Exemplen nedan använder [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

Information om hur du registrerar program i utvecklings miljön och konfigurerar miljövariabler finns i [bevilja åtkomst med hanterad identitet](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Skapa en identitet och utfärda en token med hanterad identitet

I följande kod exempel visas hur du skapar ett tjänst klient objekt med hanterad identitet och sedan använder klienten för att utfärda en token för en ny användare:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Skicka ett SMS med hanterad identitet

Följande kod exempel visar hur du skapar ett tjänst klient objekt med hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om autentisering](../concepts/authentication.md)

Du kanske också vill:

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för JS](/javascript/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../../quickstarts/telephony-sms/send.md)
- [Läs mer om SMS](../../concepts/telephony-sms/concepts.md)

