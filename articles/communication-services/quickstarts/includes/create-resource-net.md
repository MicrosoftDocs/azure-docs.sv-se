---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110905"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- Den senaste versionen [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativ system.
- Hämta den senaste versionen av [.net Identity SDK](/dotnet/api/azure.identity).
- Hämta den senaste versionen av [.net Management SDK](../../concepts/sdk-options.md).

## <a name="installing-the-sdk"></a>Installera SDK

Börja med att inkludera kommunikations tjänst hanterings-SDK i C#-projektet:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>Prenumerations-ID:t

Du måste känna till ID: t för din Azure-prenumeration. Detta kan hämtas från portalen:

1.  Logga in på ditt Azure-konto
2.  Välj prenumerationer i den vänstra sid panelen
3.  Välj vilken prenumeration som behövs
4.  Klicka på Översikt
5.  Välj ditt prenumerations-ID

I den här snabb starten antar vi att du har sparat prenumerations-ID: t i en miljö variabel som kallas `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Autentisering

För att kunna kommunicera med Azure Communication Services måste du först autentisera dig till Azure. Du gör detta vanligt vis med en identitet för tjänstens huvud namn.

### <a name="option-1-managed-identity"></a>Alternativ 1: hanterad identitet

Om din kod körs som en tjänst i Azure är det enklaste sättet att autentisera en hanterad identitet från Azure. Läs mer om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

[Azure-tjänster som har stöd för hanterade identiteter](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Använda hanterade identiteter för App Service och Azure Functions](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Systemtilldelad hanterad identitet](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Användardefinierad hanterad identitet](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

ClientId för den hanterade identitet som du skapade måste skickas till det `ManagedIdentityCredential` explicit.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Alternativ 2: tjänstens huvud namn

I stället för att använda en hanterad identitet kanske du vill autentisera till Azure med ett huvud namn för tjänsten som du hanterar själv. Läs mer i dokumentationen om hur du [skapar och hanterar tjänstens huvud namn i Azure Active Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

När du har skapat ditt huvud namn för tjänsten måste du samla in följande information om den från Azure Portal:

- **Klient-ID**
- **Klient hemlighet**
- **Klientorganisations-ID**

Lagra värdena i miljövariabler med namnet `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` `AZURE_TENANT_ID` respektive. Du kan sedan skapa en kommunikations tjänst hanterings klient så här:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Alternativ 3: användar identitet

Om du vill anropa Azure åt en interaktiv användare i stället för att använda en tjänst identitet kan du använda följande kod för att skapa en Azure Communication Services-hanteringsserver. Då öppnas ett webbläsarfönster där användaren uppmanas att ange sina MSA-eller Azure AD-autentiseringsuppgifter.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Hantera kommunikations tjänst resurser

### <a name="interacting-with-azure-resources"></a>Interagera med Azure-resurser

Nu när du har autentiserats kan du använda hanterings klienten för att skapa API-anrop.

För vart och ett av följande exempel kommer vi att tilldela våra kommunikations tjänst resurser till en befintlig resurs grupp.

Om du behöver skapa en resurs grupp kan du göra det med hjälp av [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) eller [Azure Resource Manager SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Skapa och hantera en kommunikations tjänst resurs

Vår instans av kommunikations tjänst hantering SDK client ( ``Azure.ResourceManager.Communication.CommunicationManagementClient`` ) kan användas för att utföra åtgärder på kommunikations tjänsternas resurser.

#### <a name="create-a-communication-services-resource"></a>Skapa en Communication Services-resurs

När du skapar en kommunikations tjänst resurs anger du resurs gruppens namn och resurs namnet. Observera att `Location` egenskapen alltid är `global` och under den offentliga för hands versionen `DataLocation` måste värdet vara `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Uppdatera en kommunikations tjänst resurs

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Visa en lista över alla kommunikations tjänst resurser

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Ta bort en kommunikations tjänst resurs

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Hantera nycklar och anslutnings strängar

Varje kommunikations tjänst resurs har ett par åtkomst nycklar och motsvarande anslutnings strängar. De här nycklarna kan nås med hanterings-SDK och används sedan av andra SDK: er för kommunikations tjänster för att autentisera sig själva till Azure Communication Services.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Hämta åtkomst nycklar för en kommunikations tjänst resurs

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Återskapa en åtkomst nyckel för en kommunikations tjänst resurs

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```