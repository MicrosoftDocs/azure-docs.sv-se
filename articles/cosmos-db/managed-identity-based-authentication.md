---
title: Använda en systemtilldelad hanterad identitet för åtkomst till Azure Cosmos DB-data
description: Lär dig hur du konfigurerar en Azure Active Directory (Azure AD) systemtilldelad hanterad identitet (hanterad tjänst identitet) för att komma åt nycklar från Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927408"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Använd systemtilldelade hanterade identiteter för att få åtkomst till Azure Cosmos DB data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln ska du ställa in en *robust lösning för nyckel rotations oberoende* för att få åtkomst till Azure Cosmos DB nycklar med hjälp av [hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Exemplet i den här artikeln använder Azure Functions, men du kan använda vilken tjänst som helst som har stöd för hanterade identiteter. 

Du lär dig hur du skapar en Function-app som kan komma åt Azure Cosmos DB data utan att behöva kopiera några Azure Cosmos DB nycklar. Function-appen aktive ras varje minut och registrerar den aktuella temperaturen hos en Aquarium-fiskart. Information om hur du konfigurerar en timer-utlöst Function-app finns i [skapa en funktion i Azure som utlöses av en timer](../azure-functions/functions-create-scheduled-function.md) -artikel.

För att förenkla scenariot är en [Time to Live](./time-to-live.md) -inställning redan konfigurerad för att rensa äldre temperatur dokument. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Tilldela en systemtilldelad hanterad identitet till en Function-app

I det här steget tilldelar du en systemtilldelad hanterad identitet till din Function-app.

1. Öppna **Azure Function** -fönstret i [Azure Portal](https://portal.azure.com/)och gå till din Function-app. 

1. Öppna fliken identitet för **plattforms funktioner**  >   : 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Skärm bild som visar plattforms funktioner och identitets alternativ för Function-appen.":::

1. På fliken **identitet** **aktiverar du** system identitetens **status** och väljer **Spara**. Fönstret **identitet** bör se ut så här:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Skärm bild som visar systemets identitets status inställt på på.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Bevilja åtkomst till ditt Azure Cosmos-konto

I det här steget ska du tilldela en roll till funktionens programs systemtilldelade hanterade identitet. Azure Cosmos DB har flera inbyggda roller som du kan tilldela till den hanterade identiteten. I den här lösningen använder du följande två roller:

|Inbyggd roll  |Description  |
|---------|---------|
|[DocumentDB-konto deltagare](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton. Tillåter hämtning av Läs-/skriv nycklar. |
|[Cosmos DB konto läsar roll](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB konto data. Tillåter hämtning av Läs nycklar. |

> [!IMPORTANT]
> Stöd för rollbaserad åtkomst kontroll i Azure Cosmos DB gäller endast för kontroll Plans åtgärder. Data Plans åtgärder skyddas via primära nycklar eller resurs-token. Mer information finns i artikeln [säker åtkomst till data](secure-access-to-data.md) .

> [!TIP] 
> När du tilldelar roller tilldelar du bara den åtkomst som krävs. Om din tjänst bara behöver läsa data, tilldelar du rollen **Cosmos DB konto läsare** till den hanterade identiteten. Mer information om betydelsen av minsta behörighets åtkomst finns i artikeln om [lägre exponering för privilegierade konton](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) .

I det här scenariot läser Function-appen temperaturen i Aquarium och skriver sedan tillbaka dessa data till en behållare i Azure Cosmos DB. Eftersom Function-appen måste skriva data, måste du tilldela rollen **DocumentDB Account Contributor** . 

### <a name="assign-the-role-using-azure-portal"></a>Tilldela rollen med hjälp av Azure Portal

1. Logga in på Azure Portal och gå till ditt Azure Cosmos DB-konto. Öppna fönstret **åtkomst kontroll (IAM)** och sedan fliken **roll tilldelningar** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Skärm bild som visar åtkomst kontroll panelen och fliken roll tilldelningar.":::

1. Välj **+ Lägg till** > **Lägg till rolltilldelning**.

1. Panelen **Lägg till roll tilldelning** öppnas till höger:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Skärm bild som visar fönstret Lägg till roll tilldelning.":::

   * **Roll**: Välj **DocumentDB-konto deltagare**
   * **Tilldela åtkomst till**: Välj **Funktionsapp** under avsnittet **Välj systemtilldelad hanterad identitet** .
   * **Välj**: fönstret fylls i med alla Function-appar i din prenumeration som har en **hanterad system identitet**. I det här fallet väljer du appen **FishTankTemperatureService** -funktion: 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Skärm bild som visar fönstret Lägg till roll tilldelning ifyllt med exempel.":::

1. När du har valt din Function-app väljer du **Spara**.

### <a name="assign-the-role-using-azure-cli"></a>Tilldela rollen med hjälp av Azure CLI

Om du vill tilldela rollen med hjälp av Azure CLI öppnar du Azure Cloud Shell och kör följande kommandon:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Åtkomst till Azure Cosmos DB nycklar via programmering

Nu har vi en Function-app med en systemtilldelad hanterad identitet med rollen **DocumentDB Account Contributor** i Azure Cosmos DB behörigheter. Följande kod för Function-appen hämtar Azure Cosmos DB nycklar, skapar ett CosmosClient-objekt, hämtar temperaturen för Aquarium och sparar sedan detta till Azure Cosmos DB.

I det här exemplet används [API: erna List nycklar](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) för att komma åt dina Azure Cosmos DB konto nycklar.

> [!IMPORTANT] 
> Om du vill [tilldela rollen som Cosmos DB konto läsare](#grant-access-to-your-azure-cosmos-account) måste du använda [API: et för skrivskyddade nycklar](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Detta fyller bara på skrivskyddade nycklar.

API: erna för List nycklar returnerar `DatabaseAccountListKeysResult` objektet. Den här typen är inte definierad i C#-biblioteken. Följande kod visar implementeringen av den här klassen:  

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

Exemplet använder också ett enkelt dokument med namnet "TemperatureRecord" som definieras enligt följande:

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

Du använder biblioteket [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för att hämta den systemtilldelade hanterade identitets-token. Information om andra sätt att hämta token och få mer information om `Microsoft.Azure.Service.AppAuthentication` biblioteket finns i artikeln [tjänst-till-tjänst-autentisering](/dotnet/api/overview/azure/service-to-service-authentication) .


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

Nu är du redo att [distribuera din Function-app](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Nästa steg

* [Certifikatbaserad autentisering med Azure Cosmos DB och Azure Active Directory](certificate-based-authentication.md)
* [Skydda Azure Cosmos DB nycklar med Azure Key Vault](access-secrets-from-keyvault.md)
* [Säkerhets bas linje för Azure Cosmos DB](security-baseline.md)