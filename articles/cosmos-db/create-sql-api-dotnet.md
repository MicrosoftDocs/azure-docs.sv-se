---
title: Snabb start – Bygg en .NET-konsol-app för att hantera Azure Cosmos DB SQL API-resurser
description: Lär dig hur du skapar en .NET-konsol app för att hantera Azure Cosmos DB SQL API-konto resurser i den här snabb starten.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/21/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f25c8e2ca7abeae0a918825654c55256182189ae
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785367"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Snabb start: Bygg en .NET-konsol-app för att hantera Azure Cosmos DB SQL API-resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Kom igång med klient biblioteket Azure Cosmos DB SQL API för .NET. Följ stegen i det här dokumentet för att installera .NET-paketet, bygga en app och testa exempel koden för grundläggande CRUD-åtgärder för de data som lagras i Azure Cosmos DB. 

Azure Cosmos DB är Microsofts snabba NoSQL-databas med öppna API: er för alla skalor. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-, dokument-och Graf-databaser. Använd klient biblioteket Azure Cosmos DB SQL-API för .NET för att:

* Skapa en Azure Cosmos-databas och en behållare
* Lägg till exempel data i behållaren
* Fråga efter data 
* Ta bort databasen

[API-referens dokumentation](/dotnet/api/microsoft.azure.cosmos)  |  [Biblioteks käll kod](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/) eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. 
* [.Net Core 2,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du skapar ett Azure Cosmos-konto och konfigurerar ett projekt som använder Azure Cosmos DB SQL API-klient bibliotek för .NET för att hantera resurser. Exempel koden som beskrivs i den här artikeln skapar en `FamilyDatabase` databas och familje medlemmar (varje familje medlem är ett objekt) i databasen. Varje familje medlem har egenskaper som `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Egenskapen används som partitions nyckel för behållaren. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Om du använder alternativet [prova Azure Cosmos dB för kostnads fri](https://azure.microsoft.com/try/cosmosdb/) för att skapa ett Azure Cosmos-konto måste du skapa ett Azure Cosmos DB konto av typen **SQL API**. Ett Azure Cosmos DB test konto har redan skapats åt dig. Du behöver inte skapa kontot explicit, så du kan hoppa över det här avsnittet och gå vidare till nästa avsnitt.

Om du har en egen Azure-prenumeration eller skapat en prenumeration kostnads fritt bör du skapa ett Azure Cosmos-konto explicit. Följande kod skapar ett Azure Cosmos-konto med konsekvens i sessionen. Kontot replikeras i `South Central US` och `North Central US` .  

Du kan använda Azure Cloud Shell för att skapa ett Azure Cosmos-konto. Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell. I den här snabb starten väljer du **bash** läge. Azure Cloud Shell kräver också ett lagrings konto, så du kan skapa ett när du uppmanas till det.

Välj knappen **prova** bredvid följande kod och välj **bash** läge Välj **skapa ett lagrings konto** och logga in på Cloud Shell. Nästa kopiera och klistra in följande kod för att Azure Cloud Shell och köra den. Namnet på Azure Cosmos-kontot måste vara globalt unikt, se till att uppdatera `mysqlapicosmosdb` värdet innan du kör kommandot.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Det tar en stund att skapa ett Azure Cosmos-konto när åtgärden har slutförts. du kan se bekräftelse resultatet. När kommandot har slutförts loggar du in på [Azure Portal](https://portal.azure.com/) och kontrollerar att Azure Cosmos-kontot med det angivna namnet finns. Du kan stänga Azure Cloud Shells fönstret när resursen har skapats. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Skapa en ny .NET-app

Skapa ett nytt .NET-program i önskat redigerings program eller IDE. Öppna kommando tolken i Windows eller ett terminalfönster från den lokala datorn. Du kommer att köra alla kommandon i nästa avsnitt från kommando tolken eller terminalen.  Kör följande dotNet New-kommando för att skapa en ny app med namnet `todo` . Parametern--langVersion anger egenskapen LangVersion i den skapade projekt filen.

```console
dotnet new console --langVersion 7.1 -n todo
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
cd todo
dotnet build
```

Förväntade utdata från versionen bör se ut ungefär så här:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installera Azure Cosmos DB-paketet

När du fortfarande är i program katalogen installerar du Azure Cosmos DB klient biblioteket för .NET Core med kommandot dotNet Lägg till paket.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter för Azure Cosmos-kontot från Azure Portal

Exempel programmet måste autentisera till ditt Azure Cosmos-konto. För att autentisera bör du skicka autentiseringsuppgifter för Azure Cosmos-kontot till programmet. Hämta dina autentiseringsuppgifter för Azure Cosmos-kontot genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos-konto.

1. Öppna rutan **nycklar** och kopiera **URI: n** och **primär nyckeln** för ditt konto. Du lägger till värdena URI och nycklar i en miljö variabel i nästa steg.

### <a name="set-the-environment-variables"></a>Ange miljövariabler

När du har kopierat **URI: n** och **primär nyckeln** för ditt konto sparar du dem i en ny miljö variabel på den lokala datorn som kör programmet. Ange miljövariabeln genom att öppna ett konsol fönster och köra följande kommando. Se till att ersätta `<Your_Azure_Cosmos_account_URI>` och `<Your_Azure_Cosmos_account_PRIMARY_KEY>` värden.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objekt modell

Innan du börjar skapa programmet ska vi titta närmare på hierarkin över resurser i Azure Cosmos DB och objekt modellen som används för att skapa och få åtkomst till dessa resurser. Azure Cosmos DB skapar resurser i följande ordning:

* Azure Cosmos-konto 
* Databaser 
* Containers 
* Poster

Mer information om hierarkin för olika entiteter finns i [arbeta med databaser, behållare och objekt i Azure Cosmos DB](account-databases-containers-items.md) artikel. Du kommer att använda följande .NET-klasser för att interagera med dessa resurser:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) – den här klassen tillhandahåller en logisk representation på klient sidan för Azure Cosmos DBS tjänsten. Klient objekt används för att konfigurera och köra begär Anden mot tjänsten.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) – den här metoden skapar (om den inte finns) eller hämtar (om redan finns) en databas resurs som en asynkron åtgärd. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)– den här metoden skapar (om den inte finns) eller hämtar (om den finns redan) en behållare som en asynkron åtgärd. Du kan kontrol lera status koden från svaret för att avgöra om behållaren nyligen skapades (201) eller om en befintlig behållare returnerades (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) – den här metoden skapar ett objekt i behållaren. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) – den här metoden skapar ett objekt i behållaren om det inte redan finns eller ersätter objektet om det redan finns. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) – den här metoden skapar en fråga för objekt under en behållare i en Azure Cosmos-databas med hjälp av ett SQL-uttryck med parametriserade värden. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) – tar bort den angivna databasen från ditt Azure Cosmos-konto. `DeleteAsync` metoden tar bara bort databasen. Avslaget av `Cosmosclient` instansen ska ske separat (vilket sker i DeleteDatabaseAndCleanupAsync-metoden. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Kodexempel

Exempel koden som beskrivs i den här artikeln skapar en familje databas i Azure Cosmos DB. Familje databasen innehåller familje information som namn, adress, plats, associerade föräldrar, barn och hus djur. Definiera egenskaperna för ett familje objekt innan du fyller i data på ditt Azure Cosmos-konto. Skapa en ny klass med namnet `Family.cs` på rot nivån i exempel programmet och Lägg till följande kod i den:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Lägg till using-direktiv & definiera klient objekt

Öppna `Program.cs` filen i redigerings programmet från projekt katalogen och Lägg till följande med hjälp av direktiv överst i programmet:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Till filen **program. cs** lägger du till kod för att läsa de miljövariabler som du har angett i föregående steg. Definiera `CosmosClient` objekten, `Database` och `Container` . Lägg till kod till main-metoden som anropar `GetStartedDemoAsync` metoden där du hanterar Azure Cosmos Account-resurser. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Skapa en databas 

Definiera `CreateDatabaseAsync` metoden i `program.cs` klassen. Den här metoden skapar `FamilyDatabase` om den inte redan finns.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Skapa en container

Definiera `CreateContainerAsync` metoden i `program.cs` klassen. Den här metoden skapar `FamilyContainer` om den inte redan finns. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Skapa ett objekt

Skapa ett familje objekt genom att lägga till- `AddItemsToContainerAsync` metoden med följande kod. Du kan använda `CreateItemAsync` metoderna eller `UpsertItemAsync` för att skapa ett objekt:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Fråga objekten

När du har infogat ett objekt kan du köra en fråga för att hämta information om "Andersen"-serien. Följande kod visar hur du kör frågan med SQL-frågan direkt. SQL-frågan för att hämta "Anderson"-familje information är: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiera `QueryItemsAsync` metoden i `program.cs` klassen och Lägg till följande kod i den:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Ta bort databasen 

Slutligen kan du ta bort databasen genom att lägga till `DeleteDatabaseAndCleanupAsync` metoden med följande kod:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Kör CRUD-åtgärder

När du har definierat alla metoder som krävs kör du dem med i- `GetStartedDemoAsync` metoden. `DeleteDatabaseAndCleanupAsync`Metoden kommenterade i den här koden eftersom du inte ser några resurser om metoden körs. Du kan ta bort kommentaren när du har verifierat att dina Azure Cosmos DB-resurser har skapats i Azure Portal. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

När du har lagt till alla nödvändiga metoder sparar du `Program.cs` filen. 

## <a name="run-the-code"></a>Kör koden

Skapa sedan Azure Cosmos DB-resurserna genom att skapa och köra programmet. Se till att öppna ett nytt kommando tolks fönster, Använd inte samma instans som du har använt för att ange miljövariablerna. Eftersom miljövariablerna inte är inställda i det aktuella öppna fönstret. Du måste öppna en ny kommando tolk för att se uppdateringarna. 

```console
dotnet build
```

```console
dotnet run
```

Följande utdata skapas när du kör programmet. Du kan också logga in på Azure Portal och kontrol lera att resurserna har skapats:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Du kan verifiera att data har skapats genom att logga in på Azure Portal och se vilka objekt som krävs på ditt Azure Cosmos-konto. 

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort Azure Cosmos-kontot och motsvarande resurs grupp. Följande kommando visar hur du tar bort resurs gruppen med hjälp av Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en databas och en behållare med hjälp av en .NET Core-app. Nu kan du importera ytterligare data till ditt Azure Cosmos-konto med hjälp av anvisningarna i följande artikel. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
