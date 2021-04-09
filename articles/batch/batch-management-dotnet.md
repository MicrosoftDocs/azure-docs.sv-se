---
title: Använd batch Management .NET-biblioteket för att hantera konto resurser
description: Skapa, ta bort och ändra Azure Batch konto resurser med batch Management .NET-biblioteket.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896739"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Hantera batch-konton och kvoter med klient biblioteket för batch-hantering för .NET

Du kan minska underhålls kostnader i Azure Batch-program genom att använda [batch Management .net](/dotnet/api/overview/azure/batch) -biblioteket för att automatisera skapandet av batch-konton, borttagning, nyckel hantering och kvot identifiering.

- **Skapa och ta bort batch-konton** inom vilken region som helst. Om du till exempel är en oberoende program varu leverantör (ISV) som till exempel tillhandahåller en tjänst för dina klienter där varje har tilldelats ett separat batch-konto för fakturering kan du lägga till funktioner för att skapa och ta bort konton på kund portalen.
- **Hämta och återskapa konto nycklar** program mässigt för alla batch-konton. Detta kan hjälpa dig att följa säkerhets principer som tvingar regelbunden förnyelse eller förfallo datum för konto nycklar. När du har flera batch-konton i olika Azure-regioner ökar lösningens effektivitet genom automatisering av den här förnyelse processen.
- **Kontrol lera konto kvoter** och ta prov-och fel gissningen att avgöra vilka batch-konton som har vilka gränser. Genom att kontrol lera konto kvoterna innan jobb startas, skapa pooler eller lägga till Compute-noder, kan du proaktivt justera var eller när dessa beräknings resurser skapas. Du kan avgöra vilka konton som kräver kvot ökningar innan du allokerar ytterligare resurser i dessa konton.
- **Kombinera funktioner i andra Azure-tjänster** för en komplett hanterings upplevelse med hjälp av batch Management .net, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)och [Azure Resource Manager](../azure-resource-manager/management/overview.md) tillsammans i samma program. Genom att använda dessa funktioner och deras API: er, kan du tillhandahålla en friktions lös autentisering, möjlighet att skapa och ta bort resurs grupper och de funktioner som beskrivs ovan för en heltäckande hanterings lösning.

> [!NOTE]
> Den här artikeln fokuserar på programmerings hanteringen av dina batch-konton, nycklar och kvoter, men du kan också utföra många av dessa aktiviteter med [hjälp av Azure Portal](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Skapa och ta bort batch-konton

En av de viktigaste funktionerna i batch Management-API: et är att skapa och ta bort [batch-konton](accounts.md) i en Azure-region. Det gör du genom att använda [BatchManagementClient. account. CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) och [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync), eller deras synkrona motsvarigheter.

Följande kodfragment skapar ett konto, hämtar det nyligen skapade kontot från batch-tjänsten och tar sedan bort det. I det här kodfragmentet och de andra i den här artikeln `batchManagementClient` är en fullständigt initierad instans av [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Program som använder .NET-biblioteket för batch Management och dess BatchManagementClient-klass kräver tjänst administratör eller administratörs åtkomst till den prenumeration som äger det batch-konto som ska hanteras. Mer information finns i avsnittet Azure Active Directory och exemplet på [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) -kod.

## <a name="retrieve-and-regenerate-account-keys"></a>Hämta och återskapa konto nycklar

Hämta primära och sekundära konto nycklar från alla batch-konton i prenumerationen med hjälp av [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Du kan återskapa dessa nycklar med hjälp av [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Du kan skapa ett effektiviserat anslutnings arbets flöde för dina hanterings program. Börja med att skaffa en konto nyckel för det batch-konto som du vill hantera med [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Använd sedan den här nyckeln när du initierade batch .NET-bibliotekets [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) -klass, som används vid initiering av [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrol lera kvoter för Azure-prenumeration och batch-konto

Azure-prenumerationer och enskilda Azure-tjänster som batch alla har standard kvoter som begränsar antalet vissa entiteter i dem. Standard kvoter för Azure-prenumerationer finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md). Standard kvoterna för batch-tjänsten finns i [kvoter och begränsningar för tjänsten Azure Batch](batch-quota-limit.md). Med hjälp av .NET-biblioteket för batch Management kan du kontrol lera kvoterna i dina program. På så sätt kan du fatta beslut om tilldelning innan du lägger till konton eller beräknings resurser som pooler och datornoder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrol lera en Azure-prenumeration för batch-konto kvoter

Innan du skapar ett batch-konto i en region kan du kontrol lera din Azure-prenumeration för att se om du kan lägga till ett konto i den regionen.

I kodfragmentet nedan använder vi först **ListAsync** för att hämta en samling av alla batch-konton som ingår i en prenumeration. När vi har skaffat den här samlingen fastställer vi hur många konton som finns i mål regionen. Sedan använder vi **GetQuotasAsync** för att hämta batch-kontots kvot och bestämmer hur många konton (om det finns några) som kan skapas i den regionen.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

I kodfragmentet ovan `creds` är en instans av **TokenCredentials**. Om du vill se ett exempel på hur du skapar objektet, se kod exemplet [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrol lera ett batch-konto för beräknings resurs kvoter

Innan du ökar beräknings resurserna i din batch-lösning kan du kontrol lera att de resurser som du vill allokera inte överskrider kontots kvoter. I kodfragmentet nedan skriver vi ut kvot informationen för batch-kontot med namnet `mybatchaccount` . I ditt eget program kan du använda sådan information för att avgöra om kontot kan hantera ytterligare resurser som ska skapas.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Även om det finns standard kvoter för Azure-prenumerationer och-tjänster kan många av dessa begränsningar höjas genom att [en kvot ökning begärs i Azure Portal](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Använda Azure AD med batch Management .NET

Batch Management .NET-biblioteket är en Azure Resource Provider-klient och används tillsammans med [Azure Resource Manager](../azure-resource-manager/management/overview.md) för att hantera konto resurser program mässigt. Azure AD krävs för att autentisera begär Anden som görs via en Azure Resource Provider-klient, inklusive batch Management .NET-biblioteket och via Azure Resource Manager. Information om hur du använder Azure AD med batch Management .NET-biblioteket finns i [använda Azure Active Directory för att autentisera batch-lösningar](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Exempel projekt på GitHub

Om du vill se batch Management .NET i praktiken kan du kolla [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -exempelprojektet på GitHub. Exempel programmet AccountManagement demonstrerar följande åtgärder:

1. Hämta en säkerhetstoken från Azure AD med hjälp av [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Om användaren inte redan är inloggad uppmanas de att ange sina autentiseringsuppgifter för Azure.
2. Med säkerhetstoken som hämtats från Azure AD skapar du en [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) för att fråga Azure efter en lista över prenumerationer som är kopplade till kontot. Användaren kan välja en prenumeration i listan om den innehåller fler än en prenumeration.
3. Hämta autentiseringsuppgifter som är associerade med den valda prenumerationen.
4. Skapa ett [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) -objekt med hjälp av autentiseringsuppgifterna.
5. Använd ett [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) -objekt för att skapa en resurs grupp.
6. Använd ett [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) -objekt för att utföra flera batch-konto-åtgärder:
   - Skapa ett batch-konto i den nya resurs gruppen.
   - Hämta det nyligen skapade kontot från batch-tjänsten.
   - Skriv ut konto nycklarna för det nya kontot.
   - Återskapa en ny primär nyckel för kontot.
   - Skriv ut kvot informationen för kontot.
   - Skriv ut kvot informationen för prenumerationen.
   - Skriv ut alla konton i prenumerationen.
   - Ta bort det nyligen skapade kontot.
7. Ta bort resurs gruppen.

För att köra exempel programmet måste du först registrera det med din Azure AD-klient i Azure Portal och bevilja behörighet till Azure Resource Manager API. Följ de steg som beskrivs i [autentisera hanterings lösningar för batch med Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](quick-run-dotnet.md) eller [Python](quick-run-python.md). De här snabb starterna vägleder dig genom ett exempel program som använder batch-tjänsten för att köra en arbets belastning på flera datornoder, med Azure Storage för mellanlagring och hämtning av arbets belastnings filer. git pus
