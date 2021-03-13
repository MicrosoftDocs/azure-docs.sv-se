---
ms.openlocfilehash: b552629c23991880a2f9cfc6f9e96376daecc1a0
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439055"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Lägg till hanterad identitet i kommunikations tjänst lösningen (.NET)

### <a name="install-the-client-library-packages"></a>Installera klient biblioteks paketen

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Använda klient biblioteks paketen

Lägg till följande `using` direktiv i koden för att använda Azure-identiteten och Azure Storage klient biblioteken.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Exemplen nedan använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`och `AZURE_TENANT_ID` miljövariabler krävs för att skapa ett `DefaultAzureCredential` objekt. Om du vill skapa ett registrerat program i utvecklings miljön och konfigurera miljövariabler, se [bevilja åtkomst med hanterad identitet](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Skapa en identitet och utfärda en token med hanterad identitet

I följande kod exempel visas hur du skapar ett tjänst klient objekt med Azure Active Directory tokens.

Använd sedan klienten för att utfärda en token för en ny användare:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Skicka ett SMS med hanterad identitet

Följande kod exempel visar hur du skapar ett SMS-tjänstens klient objekt med hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

