---
title: Använda en systemtilldelad hanterad identitet för åtkomst till Azure Cosmos DB-data
description: Lär dig hur du konfigurerar en Azure Active Directory (Azure AD) system tilldelad hanterad identitet (hanterad tjänstidentitet) för att komma åt nycklar från Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b85e1fc74688f2883531bd3a6e724a2ce326a9db
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600258"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Använda system tilldelade hanterade identiteter för att få åtkomst Azure Cosmos DB data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln ska du konfigurera en *robust,* nyckelrotationsoberoende lösning för att komma åt Azure Cosmos DB nycklar med hjälp av [hanterade identiteter.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Exemplet i den här artikeln använder Azure Functions, men du kan använda alla tjänster som stöder hanterade identiteter. 

Du lär dig hur du skapar en funktionsapp som kan komma åt Azure Cosmos DB data utan att behöva kopiera några Azure Cosmos DB nycklar. Funktionsappen aktiveras varje minut och registrerar den aktuella temperaturen för en vattenbehållare. Information om hur du ställer in en timerutlöst funktionsapp finns i artikeln Skapa en funktion i Azure som [utlöses av en timer.](../azure-functions/functions-create-scheduled-function.md)

För att förenkla scenariot har [en Time To Live-inställning](./time-to-live.md) redan konfigurerats för att rensa äldre temperaturdokument. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Tilldela en system tilldelad hanterad identitet till en funktionsapp

I det här steget tilldelar du en system tilldelad hanterad identitet till din funktionsapp.

1. I fönstret [Azure Portal](https://portal.azure.com/)du **azure-funktionsfönstret** och går till funktionsappen. 

1. Öppna fliken **Plattformsfunktioner**  >  **Identitet:** 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Skärmbild som visar plattformsfunktioner och identitetsalternativ för funktionsappen.":::

1. På fliken **Identitet** aktiverar du **Systemidentitetsstatus** **och** väljer **Spara**. Fönstret **Identitet** bör se ut så här:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Skärmbild som visar systemidentitetens status inställd på På.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Bevilja åtkomst till ditt Azure Cosmos-konto

I det här steget tilldelar du en roll till funktionsappens system tilldelade hanterade identitet. Azure Cosmos DB har flera inbyggda roller som du kan tilldela till den hanterade identiteten. För den här lösningen använder du följande två roller:

|Inbyggd roll  |Description  |
|---------|---------|
|[DocumentDB-kontodeltagare](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB konton. Tillåter hämtning av läs-/skrivnycklar. |
|[Cosmos DB rollen Kontoläsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB-kontodata. Tillåter hämtning av läsnycklar. |

> [!IMPORTANT]
> Stöd för rollbaserad åtkomstkontroll i Azure Cosmos DB endast för kontrollplansåtgärder. Dataplansåtgärder skyddas via primära nycklar eller resurstoken. Mer information finns i artikeln [Säker åtkomst till data.](secure-access-to-data.md)

> [!TIP] 
> När du tilldelar roller tilldelar du endast den åtkomst som behövs. Om din tjänst bara behöver läsa data tilldelar du rollen **Cosmos DB-kontoläsare** till den hanterade identiteten. Mer information om vikten av lägsta behörighetsåtkomst finns i artikeln Lower exposure of privileged accounts (Lägre [exponering av privilegierade](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) konton).

I det här scenariot läser funktionsappen temperaturen för behållaren och skriver sedan tillbaka dessa data till en container i Azure Cosmos DB. Eftersom funktionsappen måste skriva data måste du tilldela rollen **DocumentDB-kontodeltagare.** 

### <a name="assign-the-role-using-azure-portal"></a>Tilldela rollen med Azure Portal

1. Logga in på Azure Portal och gå till ditt Azure Cosmos DB konto. Öppna fönstret **Åtkomstkontroll (IAM)** och sedan **fliken Rolltilldelningar:**

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Skärmbild som visar fönstret Åtkomstkontroll och fliken Rolltilldelningar.":::

1. Välj **+ Lägg till** > **Lägg till rolltilldelning**.

1. Panelen **Lägg till rolltilldelning** öppnas till höger:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Skärmbild som visar fönstret Lägg till rolltilldelning.":::

   * **Roll:** Välj **DocumentDB-kontodeltagare**
   * **Tilldela åtkomst till**: Under **underavsnittet Välj system tilldelad** hanterad identitet väljer du **Funktionsapp.**
   * **Välj**: Fönstret fylls i med alla funktionsappar i din prenumeration som har en **hanterad systemidentitet**. I det här fallet väljer du **funktionsappen FishServiceTemperatureService:** 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Skärmbild som visar fönstret Lägg till rolltilldelning ifylld med exempel.":::

1. När du har valt funktionsappen väljer du **Spara.**

### <a name="assign-the-role-using-azure-cli"></a>Tilldela rollen med Hjälp av Azure CLI

Om du vill tilldela rollen med hjälp av Azure CLI öppnar Azure Cloud Shell och kör följande kommandon:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Komma åt Azure Cosmos DB programmatiskt

Nu har vi en funktionsapp som har en system tilldelad hanterad identitet med **rollen DocumentDB-kontodeltagare** i Azure Cosmos DB behörigheter. Följande kod för funktionsappen hämtar Azure Cosmos DB nycklar, skapar ett CosmosClient-objekt, hämtar temperaturen för objektet och sparar sedan det för att Azure Cosmos DB.

Det här exemplet använder [API:et Lista nycklar](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listkeys) för att komma åt Azure Cosmos DB-kontonycklar.

> [!IMPORTANT] 
> Om du vill [tilldela rollen Cosmos DB-kontoläsare](#grant-access-to-your-azure-cosmos-account) måste du använda API:et [Lista skrivskyddade nycklar.](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listreadonlykeys) Då fylls bara de skrivskyddade nycklarna i.

API:et Listnycklar returnerar `DatabaseAccountListKeysResult` objektet. Den här typen definieras inte i C#-biblioteken. Följande kod visar implementeringen av den här klassen:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

I exemplet används också ett enkelt dokument med namnet "TemperatureRecord", som definieras enligt följande:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Du använder biblioteket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för att hämta den system tilldelade hanterade identitetstoken. Om du vill lära dig andra sätt att hämta token och få mer information om biblioteket kan du läsa `Microsoft.Azure.Service.AppAuthentication` [artikeln Tjänst-till-tjänst-autentisering.](/dotnet/api/overview/azure/service-to-service-authentication)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Nu är du redo att [distribuera funktionsappen](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Nästa steg

* [Certifikatbaserad autentisering med Azure Cosmos DB och Azure Active Directory](certificate-based-authentication.md)
* [Skydda Azure Cosmos DB med hjälp av Azure Key Vault](access-secrets-from-keyvault.md)
* [Säkerhetsbaslinje för Azure Cosmos DB](security-baseline.md)
