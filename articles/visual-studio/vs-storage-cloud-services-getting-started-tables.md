---
title: Kom igång med Table Storage med hjälp av Visual Studio (Cloud Services)
description: Komma igång med Azure Table Storage i ett moln tjänst projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545792"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure Table Storage och anslutna Visual Studio-tjänster (Cloud Services-projekt)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Table Storage i Visual Studio när du har skapat eller refererat till ett Azure Storage-konto i ett Cloud Services-projekt med hjälp av dialog rutan **Lägg till anslutna tjänster** i Visual Studio. Åtgärden **Lägg till anslutna tjänster** installerar rätt NuGet-paket för att komma åt Azure Storage i projektet och lägger till anslutnings strängen för lagrings kontot i dina projekt konfigurationsfiler.

Med tjänsten Azure Table Storage kan du lagra stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

För att komma igång måste du först skapa en tabell i ditt lagrings konto. Vi visar dig hur du skapar en Azure-tabell i kod och hur du utför grundläggande tabell-och entitets åtgärder, till exempel att lägga till, ändra, läsa och läsa tabell enheter. Exemplen skrivs i C- \# kod och använder [Microsoft Azure Storage klient bibliotek för .net](/previous-versions/azure/dn261237(v=azure.100)).

**Obs:** Några av de API: er som utför anrop till Azure Storage är asynkrona. Se [asynkron programmering med async och await](/previous-versions/hh191443(v=vs.140)) för mer information. Koden nedan förutsätter att asynkrona programmerings metoder används.

* Se [Kom igång med Azure Table Storage med hjälp av .net](../cosmos-db/tutorial-develop-table-dotnet.md) om du vill ha mer information om program mässigt manipulera tabeller.
* Se [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.
* Se [Cloud Services dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/) för allmän information om Azure Cloud Services.
* Mer information om programmering av ASP.NET-program finns i [ASP.net](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Komma åt tabeller i kod
För att få åtkomst till tabeller i moln tjänst projekt måste du inkludera följande objekt i alla C#-källfiler som har åtkomst till Azure Table Storage.

1. Se till att namn områdes deklarationerna överst i C#-filen inkluderar dessa **using** -instruktioner.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Hämta ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänstekonfigurationen.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Använd all ovanstående kod framför koden i följande exempel.
   > 
   > 
3. Hämta ett **CloudTableClient** -objekt för att referera till tabell objekt i ditt lagrings konto.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Hämta ett **CloudTable** -referens objekt för att referera till en speciell tabell och entiteter.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Skapa en tabell i kod
Om du vill skapa Azure-tabellen lägger du bara till ett anrop till **CreateIfNotExistsAsync** i när du har skaffat ett **CloudTable** -objekt enligt beskrivningen i avsnittet "åtkomst tabeller i kod".

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitets klass med namnet **CustomerEntity** som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Tabell åtgärder som involverar entiteter görs med **CloudTable** -objektet som du skapade tidigare i "Access-tabeller in Code". Objektet **TableOperation** representerar den åtgärd som ska utföras. I följande kod exempel visas hur du skapar ett **CloudTable** -objekt och ett **CustomerEntity** -objekt. För att förbereda åtgärden skapas en **TableOperation** för att infoga entiteten kund i tabellen. Slutligen körs åtgärden genom att anropa **CloudTable.ExecuteAsync**.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```


## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga flera entiteter i en tabell i en enda Skriv åtgärd. Följande kod exempel skapar två entiteter ("Jeff Svensson" och "ben Svensson"), lägger till dem i ett **TableBatchOperation** -objekt med hjälp av metoden INSERT och startar sedan åtgärden genom att anropa **CloudTable.ExecuteBatchAsync**.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell efter alla entiteter i en partition använder du ett **TableQuery** -objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);

return View();
```


## <a name="get-a-single-entity"></a>Hämta en enskild entitet
Du kan skriva en fråga för att hämta en enskild, speciell entitet. I följande kod används ett **TableOperation** -objekt för att ange en kund med namnet "Ben Smith". Den här metoden returnerar bara en entitet i stället för en samling och det returnerade värdet i **TableResult. result** är ett **CustomerEntity** -objekt. Att ange både partitions-och rad nycklar i en fråga är det snabbaste sättet att hämta en enskild entitet från **tabell** tjänsten.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan ta bort en entitet när du har hittat den. Följande kod söker efter en kundentitet som heter "ben Svensson", och om den hittar den tas den bort.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    await peopleTable.ExecuteAsync(deleteOperation);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]