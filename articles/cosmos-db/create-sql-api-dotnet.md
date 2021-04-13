---
title: Snabbstart – Skapa en .NET-konsolapp för att hantera Azure Cosmos DB SQL API-resurser
description: Lär dig hur du skapar en .NET-konsolapp för att hantera Azure Cosmos DB SQL API-kontoresurser i den här snabbstarten.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: c7dc3a230e2cd3f990771c297de29891de347089
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363418"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Snabbstart: Skapa en .NET-konsolapp för att hantera Azure Cosmos DB SQL API-resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Spark v3-anslutning](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Kom igång med Azure Cosmos DB SQL API-klientbiblioteket för .NET. Följ stegen i det här dokumentet för att installera .NET-paketet, skapa en app och prova exempelkoden för grundläggande CRUD-åtgärder på data som lagras i Azure Cosmos DB. 

Azure Cosmos DB är Microsofts snabba NoSQL-databas med öppna API:er för alla skalning. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel-/värde-, dokument- och grafdatabaser. Använd Azure Cosmos DB SQL API-klientbiblioteket för .NET för att:

* Skapa en Azure Cosmos-databas och en container
* Lägga till exempeldata i containern
* Fråga efter data 
* Ta bort databasen

[API-referensdokumentation](/dotnet/api/microsoft.azure.cosmos)  |  [Bibliotekskällkod](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– skapa en](https://azure.microsoft.com/free/) kostnadsfritt eller så kan du prova Azure Cosmos DB [kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden. 
* [.NET Core 2.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Inrätta

Det här avsnittet vägled dig genom att skapa ett Azure Cosmos-konto och konfigurera ett projekt som använder Azure Cosmos DB SQL API-klientbibliotek för .NET för att hantera resurser. Exempelkoden som beskrivs i den här artikeln skapar `FamilyDatabase` en databas och familjemedlemmar (varje medlem i familjen är ett objekt) i databasen. Varje medlem i familjen har egenskaper som `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . Egenskapen `LastName` används som partitionsnyckel för containern. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Skapa ett Azure Cosmos-konto

Om du använder alternativet [Prova Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) kostnadsfritt för att skapa ett Azure Cosmos-konto måste du skapa ett Azure Cosmos DB-konto av typen **SQL API.** Ett Azure Cosmos DB testkonto har redan skapats åt dig. Du behöver inte skapa kontot uttryckligen, så du kan hoppa över det här avsnittet och gå vidare till nästa avsnitt.

Om du har en egen Azure-prenumeration eller om du har skapat en prenumeration kostnadsfritt bör du uttryckligen skapa ett Azure Cosmos-konto. Följande kod skapar ett Azure Cosmos-konto med sessionskonsekvens. Kontot replikeras i `South Central US` och `North Central US` .  

Du kan använda Azure Cloud Shell för att skapa Azure Cosmos-kontot. Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell. För den här snabbstarten väljer du **Bash-läge.** Azure Cloud Shell kräver ett lagringskonto kan du skapa ett när du uppmanas att göra det.

Välj knappen **Prova bredvid** följande kod, välj **Bash-läge,** välj Skapa ett **lagringskonto** och logga in på Cloud Shell. Kopiera och klistra in följande kod för att Azure Cloud Shell och köra den. Azure Cosmos-kontonamnet måste vara globalt unikt. Se till att uppdatera `mysqlapicosmosdb` värdet innan du kör kommandot.

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

Det tar en stund att skapa Azure Cosmos-kontot. När åtgärden har lyckats kan du se bekräftelseutdata. När kommandot har slutförts loggar du in på [Azure Portal](https://portal.azure.com/) och kontrollerar att Azure Cosmos-kontot med det angivna namnet finns. Du kan stänga Azure Cloud Shell-fönstret när resursen har skapats. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Skapa en ny .NET-app

Skapa ett nytt .NET-program i önskad redigerare eller IDE. Öppna Kommandotolken i Windows eller ett terminalfönster från den lokala datorn. Du kommer att köra alla kommandon i nästa avsnitt från kommandotolken eller terminalen.  Kör följande nya dotnet-kommando för att skapa en ny app med namnet `todo` . Parametern --langVersion anger egenskapen LangVersion i den skapade projektfilen.

```console
dotnet new console --langVersion 7.1 -n todo
```

Ändra din katalog till den nyligen skapade appmappen. Du kan skapa programmet med:

```console
cd todo
dotnet build
```

Förväntade utdata från bygget bör se ut ungefär så här:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installera Azure Cosmos DB paketet

När du fortfarande är i programkatalogen installerar du Azure Cosmos DB för .NET Core med hjälp av kommandot dotnet add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopiera autentiseringsuppgifterna för ditt Azure Cosmos-konto från Azure Portal

Exempelprogrammet måste autentisera till ditt Azure Cosmos-konto. För att autentisera bör du skicka autentiseringsuppgifterna för Azure Cosmos-kontot till programmet. Hämta autentiseringsuppgifterna för ditt Azure Cosmos-konto genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Gå till ditt Azure Cosmos-konto.

1. Öppna fönstret **Nycklar** och kopiera **URI och** **PRIMÄRNYCKEL för** ditt konto. Du lägger till URI:en och nycklar i en miljövariabel i nästa steg.

### <a name="set-the-environment-variables"></a>Ange miljövariablerna

När du har kopierat **URI och** **PRIMÄRNYCKEL** för ditt konto sparar du dem i en ny miljövariabel på den lokala dator som kör programmet. Ange miljövariabeln genom att öppna ett konsolfönster och köra följande kommando. Se till att ersätta `<Your_Azure_Cosmos_account_URI>` värdena `<Your_Azure_Cosmos_account_PRIMARY_KEY>` och .

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

 ## <a name="object-model"></a><a id="object-model"></a>Objektmodell

Innan du börjar skapa programmet ska vi titta på hierarkin med resurser i Azure Cosmos DB och objektmodellen som används för att skapa och komma åt dessa resurser. Den Azure Cosmos DB skapar resurser i följande ordning:

* Azure Cosmos-konto 
* Databaser 
* Containers 
* Poster

Mer information om hierarkin för olika entiteter finns i artikeln Arbeta med [databaser,](account-databases-containers-items.md) containrar och objekt Azure Cosmos DB entiteter. Du kommer att använda följande .NET-klasser för att interagera med dessa resurser:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) – den här klassen tillhandahåller en logisk representation på klientsidan för Azure Cosmos DB tjänsten. Klientobjektet används för att konfigurera och köra begäranden mot tjänsten.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) – Den här metoden skapar (om det inte finns) eller hämtar (om det redan finns) en databasresurs som en asynkron åtgärd. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)– – Den här metoden skapar (om den inte finns) eller hämtar (om den redan finns) en container som en asynkron åtgärd. Du kan kontrollera statuskoden från svaret för att avgöra om containern skapades nyligen (201) eller om en befintlig container returnerades (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) – Den här metoden skapar ett objekt i containern. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) – Den här metoden skapar ett objekt i containern om det inte redan finns eller ersätter objektet om det redan finns. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) – Den här metoden skapar en fråga för objekt under en container i en Azure Cosmos-databas med hjälp av en SQL-instruktion med parametriserade värden. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) – Tar bort den angivna databasen från ditt Azure Cosmos-konto. `DeleteAsync` -metoden tar bara bort databasen. Borttagning av `Cosmosclient` instansen bör ske separat (vilket den gör i metoden DeleteDatabaseAndCleanupAsync. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Kodexempel

Exempelkoden som beskrivs i den här artikeln skapar en familjedatabas i Azure Cosmos DB. Familjedatabasen innehåller familjeinformation som namn, adress, plats, associerade föräldrar, barn och husdjur. Definiera egenskaperna för ett familjeobjekt innan du fyller i data till ditt Azure Cosmos-konto. Skapa en ny klass med `Family.cs` namnet på rotnivån för exempelprogrammet och lägg till följande kod i den:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Lägg till using-direktiv & definiera klientobjektet

Öppna filen i redigeringsprogrammet `Program.cs` från projektkatalogen och lägg till följande using-direktiv överst i programmet:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

I filen **Program.cs** lägger du till kod för att läsa de miljövariabler som du har angett i föregående steg. Definiera `CosmosClient` `Database` objekten , `Container` och . Lägg sedan till kod till main-metoden som anropar metoden `GetStartedDemoAsync` där du hanterar Azure Cosmos-kontoresurser. 

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

Definiera `CreateDatabaseAsync` metoden i `program.cs` klassen . Den här metoden `FamilyDatabase` skapar om den inte redan finns.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Skapa en container

Definiera `CreateContainerAsync` metoden i `program.cs` klassen . Den här metoden `FamilyContainer` skapar om den inte redan finns. 

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

Skapa ett familjeobjekt genom att lägga `AddItemsToContainerAsync` till metoden med följande kod. Du kan använda metoderna `CreateItemAsync` eller för att skapa ett `UpsertItemAsync` objekt:

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

När du har infogat ett objekt kan du köra en fråga för att få information om "Andersen"-familjen. Följande kod visar hur du kör frågan direkt med SQL-frågan. SQL-frågan för att hämta information om "Anderson"-familjen är: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Definiera metoden `QueryItemsAsync` i klassen och lägg till följande kod i `program.cs` den:


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

Slutligen kan du ta bort databasen genom att `DeleteDatabaseAndCleanupAsync` lägga till metoden med följande kod:

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

### <a name="execute-the-crud-operations"></a>Köra CRUD-åtgärderna

När du har definierat alla nödvändiga metoder kör du dem med i `GetStartedDemoAsync` metoden . Metoden `DeleteDatabaseAndCleanupAsync` kommenterades ut i den här koden eftersom du inte ser några resurser om den metoden körs. Du kan avkommentera den när du har verifierat att Azure Cosmos DB har skapats i Azure Portal. 

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

När du har lagt till alla metoder som krävs sparar du `Program.cs` filen. 

## <a name="run-the-code"></a>Kör koden

Skapa och kör sedan programmet för att skapa Azure Cosmos DB resurser. Se till att öppna ett nytt kommandotolkfönster. Använd inte samma instans som du använde för att ange miljövariablerna. Eftersom miljövariablerna inte anges i det aktuella öppna fönstret. Du måste öppna en ny kommandotolk för att se uppdateringarna. 

```console
dotnet build
```

```console
dotnet run
```

Följande utdata genereras när du kör programmet. Du kan också logga in Azure Portal och verifiera att resurserna har skapats:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Du kan verifiera att data skapas genom att logga in på Azure Portal och se de nödvändiga objekten i ditt Azure Cosmos-konto. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort Azure Cosmos-kontot och motsvarande resursgrupp. Följande kommando visar hur du tar bort resursgruppen med hjälp av Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en databas och en container med hjälp av en .NET Core-app. Du kan nu importera ytterligare data till ditt Azure Cosmos-konto med hjälp av anvisningarna i följande artikel. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
