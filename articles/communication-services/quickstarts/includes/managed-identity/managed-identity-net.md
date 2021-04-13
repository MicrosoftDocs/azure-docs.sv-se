---
ms.openlocfilehash: 424e70f4f3656be2196f4dbdfbddb852846b6897
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307490"
---
## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `ManagedIdentitiesQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: `Program.cs` .

```console
dotnet new console -o ManagedIdentitiesQuickstartQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd ManagedIdentitiesQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>Installera SDK-paketen

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande `using` direktiv i `Program.cs` för att använda Azure Identity och Azure Storage SDK: er.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>Skapa en DefaultAzureCredential

Vi använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) för den här snabb starten. Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer. Eftersom det behövs för varje åtgärd ska vi skapa den i- `Program.cs` klassen. Lägg till följande överst i filen.

```csharp
     private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-managed-identities"></a>Utfärda en token med hanterade identiteter

Nu ska vi lägga till kod som använder den skapade autentiseringsuppgiften för att utfärda en VoIP-åtkomsttoken. Vi kommer att anropa den här koden senare.

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

## <a name="send-an-sms-with-managed-identities"></a>Skicka ett SMS med hanterade identiteter

Ett annat exempel på att använda hanterade identiteter är att lägga till den här koden som använder samma autentiseringsuppgift för att skicka ett SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
     }
```

## <a name="write-the-main-method"></a>Skriv main-metoden

Du `Program.cs` bör redan ha en huvud metod, vi lägger till kod som anropar den tidigare skapade koden för att demonstrera användningen av hanterade identiteter:

```csharp
     static void Main(string[] args)
     {
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

          // We need an instance of the program class to use within this method.
          Program instance = new();

          Console.WriteLine("Retrieving new Access Token, using Managed Identities");
          Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
          Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

          Console.WriteLine("Sending SMS using Managed Identities");

          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Managed Identities");
          Console.WriteLine($"Sms id: {result.MessageId}");
          Console.WriteLine($"Send Result Successful: {result.Successful}");
     }
```

Den slutgiltiga `Program.cs` filen bör se ut så här:

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Managed Identities");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Managed Identities");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "+18445504651", "+14256253982", "Hello from Managed Identities");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>Kör programmet

Du bör nu kunna köra ditt program med hjälp av `dotnet run` från programmappen. Utdata bör likna följande:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey....
Sending SMS using Managed Identities
Sms id: Outgoing_..._noam
Send Result Successful: True
```
