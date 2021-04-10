---
ms.openlocfilehash: 07a8d792bbb17df1401b5892b09fb7ff2f5f8e52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629424"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Den senaste versionen av [klient biblioteket för .net Core](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativ system.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Krav kontroll

- Kör kommandot i ett terminalfönster-eller kommando fönster `dotnet` för att kontrol lera att .net-klient biblioteket är installerat.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `PhoneNumbersQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: **program. cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du klient biblioteket Azure Communication PhoneNumbers för .NET-paketet med hjälp av `dotnet add package` kommandot.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.5
```

Lägg till ett `using` direktiv överst i **program. cs** för att inkludera namn områdena.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
using Azure.Communication.PhoneNumbers.Models;
```

Uppdatering `Main` av funktions signatur som ska vara asynkron.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Autentisera klienten

Telefonnummer klienter kan autentiseras med hjälp av anslutnings sträng som hämtats från en Azure-kommunikations resurser i [Azure Portal] [azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Telefonnummer klienter har också möjlighet att autentisera med Azure Active Directory autentisering. Med det här alternativet måste du `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` `AZURE_TENANT_ID` Konfigurera miljövariabler för autentisering.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Hantera telefonnummer

### <a name="search-for-available-phone-numbers"></a>Sök efter tillgängliga telefonnummer

För att kunna köpa telefonnummer måste du först söka efter tillgängliga telefonnummer. Om du vill söka efter telefonnummer anger du rikt nummer, tilldelnings typ, [telefonnummerets kapacitet](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefonnummer typ](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)och kvantitet. Observera att det är valfritt att ange rikt nummer för det kostnads fria telefonnumret för nummer.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Köp telefonnummer

Resultatet av att söka efter telefonnummer är en `PhoneNumberSearchResult` . Detta innehåller en `SearchId` som kan skickas till inköps nummer-API: n för att hämta numren i sökningen. Observera att om du anropar API: t för inköps samtal debiteras ditt Azure-konto.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionAsync();
```

### <a name="get-phone-numbers"></a>Hämta telefonnummer (er)

Efter ett inköps nummer kan du hämta det från klienten.
```csharp
var getPhoneNumberResponse = await client.GetPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Du kan också hämta alla inköpta telefonnummer.
``` csharp
var purchasedPhoneNumbers = client.GetPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Uppdatera funktioner för telefonnummer

Med ett inköpt nummer kan du uppdatera funktionerna.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Versions nummer

Du kan släppa ett inköpt telefonnummer.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionAsync();
````

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `dotnet run` kommandot.

```console
dotnet run
```

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)
