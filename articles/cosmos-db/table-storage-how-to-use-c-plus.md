---
title: Använda Azure Table Storage och Azure Cosmos DB Tabell-API med C++
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage eller Azure Cosmos DB Tabell-API med hjälp av C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489887"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Använda Azure Table Storage och Azure Cosmos DB Table-API:et med C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Den här guiden visar vanliga scenarier med hjälp av Azure Table Storage-tjänsten eller Azure Cosmos DB Tabell-API. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Den här artikeln beskriver följande scenarier:

* Skapa och ta bort en tabell
* Arbeta med tabell enheter

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är 2.2.0 för lagrings klient bibliotek, som är tillgänglig med hjälp av [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Skapa konton

### <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program

I den här hand boken använder du lagrings funktioner från ett C++-program. Det gör du genom att installera Azure Storage klient biblioteket för C++.

Om du vill installera Azure Storage klient bibliotek för C++ använder du följande metoder:

* **Linux:** Följ anvisningarna i avsnittet [Azure Storage klient bibliotek för C++ viktigt: komma igång på Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) -sidan.
* **Windows:** Använd [vcpkg](https://github.com/microsoft/vcpkg) som beroende hanterare i Windows. Följ [snabb starten](https://github.com/microsoft/vcpkg#quick-start) för att initiera vcpkg. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du hittar en guide för hur du skapar käll koden och exporterar till NuGet i [README](https://github.com/Azure/azure-storage-cpp#download--install) -filen.

### <a name="configure-access-to-the-table-client-library"></a>Konfigurera åtkomst till klientbiblioteket för Table Storage

Om du vill använda Azure Storage-API: er för att komma åt tabeller lägger du till följande- `include` instruktioner överst i C++-filen:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage- och Cosmos DB-klienter använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till datahanteringstjänster. När du kör ett klient program måste du ange lagrings anslutnings strängen eller Azure Cosmos DB anslutnings sträng i lämpligt format.

### <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure Storage-anslutningssträng

Det här exemplet visar hur du deklarerar ett statiskt fält som innehåller Azure Storage anslutnings strängen:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Använd namnet på ditt lagrings konto för `<your_storage_account>` . För <your_storage_account_key> använder du åtkomst nyckeln för lagrings kontot som anges i [Azure Portal](https://portal.azure.com). Information om lagrings konton och åtkomst nycklar finns i [skapa ett lagrings konto](../storage/common/storage-account-create.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurera en Azure Cosmos DB-anslutningssträng

Det här exemplet visar hur du deklarerar ett statiskt fält som innehåller Azure Cosmos DB anslutnings strängen:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Använd namnet på ditt Azure Cosmos DB konto för `<your_cosmos_db_account>` . Ange din primär nyckel för `<your_cosmos_db_account_key>` . Ange slut punkten som anges i [Azure Portal](https://portal.azure.com) för `<your_cosmos_db_endpoint>` .

Om du vill testa programmet på din lokala Windows-baserade dator kan du använda den Azure Storage emulatorn som är installerad med [Azure SDK](https://azure.microsoft.com/downloads/). Storage-emulatorn är ett verktyg som simulerar Azure Blob-, Queue-och table-tjänsterna som finns på din lokala utvecklings dator. I följande exempel visas hur du deklarerar ett statiskt fält för att lagra anslutnings strängen till din lokala lagrings-emulator:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Starta Azure Storage-emulatorn från Windows-skrivbordet genom att välja **Start** -knappen eller Windows-tangenten. Ange och kör *Microsoft Azure Storage-emulator*. Mer information finns i [använda Azure Storage emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen

Du kan använda- `cloud_storage_account` klassen för att representera din lagrings konto information. Använd metoden för att hämta information om lagrings kontot från lagrings anslutnings strängen `parse` .

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Hämta sedan en referens till en `cloud_table_client` klass. Med den här klassen kan du hämta referens objekt för tabeller och entiteter som lagras i tabellen Storage-tjänst. Följande kod skapar ett- `cloud_table_client` objekt med hjälp av det lagrings konto objekt som du hämtade tidigare:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Skapa och lägga till entiteter i en tabell

### <a name="create-a-table"></a>Skapa en tabell

`cloud_table_client`Med ett objekt kan du hämta referens objekt för tabeller och entiteter. Följande kod skapar ett- `cloud_table_client` objekt och använder det för att skapa en ny tabell.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Skapa ett nytt `table_entity` objekt och skicka det till om du vill lägga till en entitet i en tabell `table_operation::insert_entity` . I följande kod används kundens förnamn som radnyckel och efternamnet som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entiteter med samma partitionsnyckel kan efter frågas snabbare än entiteter med olika partitionsnyckel. Genom att använda olika partitionstyper kan du öka skalbarheten för parallella åtgärder. Mer information finns i [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/blobs/storage-performance-checklist.md).

Följande kod skapar en ny instans av `table_entity` med viss kund information att lagra. Koden nästa anrop `table_operation::insert_entity` för att skapa ett `table_operation` objekt för att infoga en entitet i en tabell och kopplar den nya tabellen entiteten till den. Slutligen anropar koden `execute` metoden i `cloud_table` objektet. Den nya `table_operation` skickar en begäran till Table service att infoga den nya kundentiteten i `people` tabellen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter

Du kan infoga en batch med entiteter i Table Storage i samma skrivåtgärd. Följande kod skapar ett `table_batch_operation` -objekt och lägger sedan till tre infognings åtgärder i det. Varje infognings åtgärd läggs till genom att skapa ett nytt enhets objekt, ange dess värden och sedan anropa `insert` metoden för `table_batch_operation` objektet för att associera entiteten med en ny infognings åtgärd. Sedan anropar koden `cloud_table.execute` för att köra åtgärden.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Några saker att tänka på när du använder batchåtgärder:

* Du kan utföra upp till 100-,-,-, `insert` `delete` `merge` `replace` `insert-or-merge` -och- `insert-or-replace` åtgärder i valfri kombination i en enda batch.  
* En batch-åtgärd kan ha en Hämta-åtgärd, om det är den enda åtgärden i batchen.  
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.  
* Batchåtgärder är begränsade till en nyttolast på 4 MB.  

## <a name="query-and-modify-entities"></a>Fråga och ändra entiteter

### <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Om du vill fråga en tabell efter alla entiteter i en partition använder du ett `table_query` objekt. I följande kod exempel anges ett filter för entiteter där `Smith` är partitionsnyckel. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.  

> [!NOTE]
> Dessa metoder stöds inte för närvarande för C++ i Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Frågan i det här exemplet returnerar alla entiteter som matchar filter kriterierna. Om du har stora tabeller och ofta behöver hämta tabellentiteterna rekommenderar vi att du lagrar dina data i Azure Storage-blobbar i stället.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition

Om du inte vill fråga alla entiteter i en partition kan du ange ett intervall. Kombinera nyckel filtret med ett rad nyckel filter. I följande kod exempel används två filter för att hämta alla entiteter i partitionen `Smith` där rad nyckeln (förnamn) börjar med en bokstav som `E` är tidigare än i alfabetet och sedan skriver ut frågeresultaten.  

> [!NOTE]
> Dessa metoder stöds inte för närvarande för C++ i Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Hämta en enda entitet

Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används `table_operation::retrieve_entity` för att ange kunden `Jeff Smith` . Den här metoden returnerar bara en entitet i stället för en samling och det returnerade värdet är i `table_result` . Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Ersätta en entitet

Om du vill ersätta en entitet hämtar du den från Table Storage, ändrar entitetsobjektet och sparar sedan ändringarna till Table Storage igen. Följande kod ändrar en befintlig kunds telefonnummer och e-postadress. I stället för att anropa `table_operation::insert_entity` används den här koden `table_operation::replace_entity` . Den här metoden gör att entiteten ersätts helt på servern, om inte entiteten på servern har ändrats sedan den hämtades. Om den har ändrats, Miss lyckas åtgärden. Det här felet förhindrar att ditt program skriver över en ändring som gjorts mellan hämtningen och uppdateringen av en annan komponent. En korrekt hantering av det här felet är att hämta entiteten igen, göra dina ändringar, om de fortfarande är giltiga, och sedan utföra en annan `table_operation::replace_entity` åtgärd.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Infoga eller ersätta en entitet

`table_operation::replace_entity` Det går inte att utföra åtgärder om entiteten har ändrats sedan den hämtades från servern. Dessutom måste du hämta entiteten från servern först för `table_operation::replace_entity` att lyckas. Ibland vet du inte om entiteten finns på servern. De aktuella värdena som lagras i det är irrelevanta, eftersom din uppdatering ska skriva över alla. Använd en åtgärd för att utföra det här resultatet `table_operation::insert_or_replace_entity` . Den här åtgärden infogar entiteten om den inte finns. Åtgärden ersätter entiteten om den finns. I följande kod exempel hämtas entiteten kund för `Jeff Smith` fortfarande, men den sparas sedan tillbaka till servern med hjälp av `table_operation::insert_or_replace_entity` . Eventuella entitetsuppdateringar som görs mellan hämtnings- och uppdateringsåtgärden skrivs över.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

En fråga till en tabell kan bara hämta några få egenskaper från en entitet. Frågan i följande kod använder `table_query::set_select_columns` metoden för att endast returnera e-postadresserna för entiteter i tabellen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Det är effektivare att köra frågor mot några få egenskaper från en entitet än att hämta alla egenskaper.
>

## <a name="delete-content"></a>Ta bort innehåll

### <a name="delete-an-entity"></a>Ta bort en entitet

Du kan ta bort en entitet när du har hämtat den. När du har hämtat en entitet anropar du `table_operation::delete_entity` den entitet som ska tas bort. Anropa sedan `cloud_table.execute` metoden. Följande kod hämtar och tar bort en entitet med en partitionsnyckel `Smith` och en rad nyckel i `Jeff` .

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Ta bort en tabell

I det sista kodexemplet tas en tabell bort från ett lagringskonto. En tabell som har tagits bort är inte tillgänglig för att skapas på nytt under en tid efter borttagningen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Felsökning

För Visual Studio Community Edition, om ditt projekt får build-fel på grund av include-filerna *storage_account. h* och *Table. h*, tar du bort växeln **/permissive-** compiler:

1. Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
1. Expandera **Konfigurationsegenskaper** i dialogrutan **Egenskapssidor**, expandera **C/C++** och välj **Språk**.
1. Ändra **Conformance mode** (Konformitetsläge) till **Nej**.

## <a name="next-steps"></a>Nästa steg

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

Följ dessa länkar om du vill lära dig mer om Azure Storage och Azure Cosmos DB Table-API:et:

* [Introduktion till Table-API:et](table-introduction.md)
* [Lista över Azure Storage-resurser i C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referens för Azure Storage-klientbiblioteket för C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentation om Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
