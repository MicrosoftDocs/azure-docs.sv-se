---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657673"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Lägg till hanterad identitet i kommunikations tjänst lösningen (.net)

### <a name="install-the-client-library-packages"></a>Installera klient biblioteks paketen

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Använda klient biblioteks paketen

Lägg till följande `using` direktiv i koden för att använda Azure-identiteten och Azure Storage klient biblioteken.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Exemplen nedan använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`och `AZURE_TENANT_ID` miljövariabler krävs för att skapa ett `DefaultAzureCredential` objekt. Om du vill skapa ett registrerat program i utvecklings miljön och konfigurera miljövariabler, se [bevilja åtkomst med hanterad identitet](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Skapa en identitet och utfärda en token med hanterad identitet

I följande kod exempel visas hur du skapar ett tjänst klient objekt med Azure Active Directory tokens.

Använd sedan klienten för att utfärda en token för en ny användare:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Skicka ett SMS med hanterad identitet

Följande kod exempel visar hur du skapar ett SMS-tjänstens klient objekt med hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om autentisering](../concepts/authentication.md)

Du kanske också vill:

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för .NET](/dotnet/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../telephony-sms/send.md)
- [Läs mer om SMS](../../concepts/telephony-sms/concepts.md)
