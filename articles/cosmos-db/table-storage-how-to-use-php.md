---
title: Använda Azure Storage Table service eller Azure Cosmos DB Tabell-API från PHP
description: Lagra strukturerade data i molnet med Azure Table Storage eller Azure Cosmos DB Tabell-API från PHP.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079639"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Använda tjänsten Azure Storage Table eller Azure Cosmos DB Table-API:et från PHP
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Den här artikeln visar hur du skapar tabeller, lagrar data och utför CRUD-åtgärder på data. Välj antingen Azure-Table service eller Azure Cosmos DB Tabell-API. Exemplen är skrivna i PHP och använder [PHP-klientbiblioteket för Azure Storage Table][download]. Guiden innehåller scenarier som beskriver hur du **skapar och tar bort en tabell** och hur du **infogar, tar bort och kör frågor mot entiteter i en tabell**. Mer information om Azure Table-tjänsten finns under [Nästa steg](#next-steps).

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Skapa ett Azure Storage-konto**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Skapa ett Azure Cosmos DB Table API-konto**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Skapa ett PHP-program

Det enda kravet när du skapar ett PHP-program för att få åtkomst till Storage Table-tjänsten eller Azure Cosmos DB Table-API:et är att du måste referera till klasser i azure-storage-table-SDK:t för PHP från din kod. Du kan använda valfritt utvecklingsverktyg för att skapa programmet, inklusive Anteckningar.

I den här guiden använder du Storage Table-tjänsten eller Azure Cosmos DB-funktioner som kan anropas lokalt från ett PHP-program, eller i kod som körs med en Azure-baserad webbroll, arbetarroll eller webbplats.

## <a name="get-the-client-library"></a>Hämta klientbiblioteket

1. Skapa en fil med namnet composer.json i roten av projektet och lägg till följande kod i filen:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Ladda ned [composer.phar](https://getcomposer.org/composer.phar) till roten. 
3. Öppna en kommandotolk och kör följande kommando i projektroten:
   ```
   php composer.phar install
   ```
   Du kan också gå till [PHP-klientbiblioteket för Azure Storage Table](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) på GitHub och klona källkoden.

## <a name="add-required-references"></a>Lägga till nödvändiga referenser

För att kunna använda Storage Table-tjänsten eller Azure Cosmos DB-API:er måste du:

* Referera till autoloader-filen med hjälp av instruktionen [require_once][require_once] och
* Referera till de klasser som du använder.

Följande exempel beskriver hur du lägger till autoloader-filen och refererar till klassen **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

I exemplen nedan visas alltid `require_once`-instruktionen, men vi refererar endast till de klasser som behövs för att köra exemplet.

## <a name="add-your-connection-string"></a>Lägg till din anslutnings sträng

Du kan antingen ansluta till Azure Storage-kontot eller Azure Cosmos DB Tabell-API-kontot. Hämta anslutnings strängen baserat på vilken typ av konto som du använder.

### <a name="add-a-storage-table-service-connection"></a>Lägga till en anslutning till Storage Table-tjänsten

För att instantiera en Storage Table-tjänstklient behöver du en giltig anslutningssträng. Anslutningssträngar för Storage Table-tjänsten har följande format:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Lägg till en Lagringsreplik-anslutning

Så här kommer du åt emulatorlagring:

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Lägga till en Azure Cosmos DB-anslutning

För att instantiera en Azure Cosmos DB Table-klient behöver du en giltig anslutningssträng. Anslutningssträngar för Azure Cosmos DB har följande format:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

När du skapar en Azure Table Storage-klient eller Azure Cosmos DB-klient måste du använda klassen **TableRestProxy**. Du kan:

* Ange anslutningssträngen direkt i klassen eller
* Hämta anslutningssträngen från externa källor med hjälp av **CloudConfigurationManager (CCM)**:
  * Som standard finns stöd för en extern källa – miljövariabler.
  * Du kan lägga till nya källor genom att utöka `ConnectionStringSource`-klassen.

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Skapa en tabell

Med **TableRestProxy**-objektet kan du skapa en tabell med metoden **createTable**. När du skapar en tabell kan du ange ett timeout-värde för Table Storage-tjänsten. (Mer information om tidsgränser för Table Storage finns i [Setting Timeouts for Table Service Operations][table-service-timeouts] (Konfigurera tidsgränser för åtgärder i Table Storage).)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Mer information om begränsningar för tabellnamn finns i [Understanding the Table Service Data Model][table-data-model] (Så här fungerar datamodellen för Table Storage).

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Du lägger till en entitet i en tabell genom att skapa ett nytt **Entity**-objekt och skicka det till **TableRestProxy->insertEntity**. Observera att du måste ange `PartitionKey` och `RowKey` när du skapar en entitet. Det här är entitetens unika identifierare. Du kan fråga dessa värden mycket snabbare än andra entitetsegenskaper. `PartitionKey` används för att automatiskt distribuera tabellens entiteter mellan flera Storage-noder. Entiteter med samma `PartitionKey` lagras på samma nod. (Åtgärder på flera entiteter som lagras på samma nod fungerar bättre än för entiteter som lagras på olika noder.) `RowKey` Är det unika ID: t för en entitet i en partition.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Information om tabellegenskaper och tabelltyper finns i [Understanding the Table Service Data Model][table-data-model] (Så här fungerar datamodellen för Table Storage).

Klassen **TableRestProxy** har två alternativa metoder som kan användas för att infoga entiteter: **insertOrMergeEntity** och **insertOrReplaceEntity**. Om du vill använda dessa metoder skapar du en ny **entitet** och skickar den som en parameter till någon av metoderna. Båda metoderna infogar entiteten om den inte finns. Om entiteten redan finns uppdaterar **insertOrMergeEntity** egenskapsvärdena om egenskaperna redan finns och lägger till nya egenskaper om de inte finns, medan **insertOrReplaceEntity** helt ersätter en befintlig entitet. Följande exempel visar hur du använder **insertOrMergeEntity**. Om entiteten med `PartitionKey` "tasksSeattle" och `RowKey` "1" inte redan finns, infogas den. Om den däremot redan har infogats tidigare (som i exemplet ovan), uppdateras egenskapen `DueDate` och egenskapen `Status` läggs till. Egenskaperna `Description` och `Location` uppdateras också, men med värden som inte ändrar dem. Om dessa sista två egenskaper inte läggs till (som i exemplet), men de finns på målentiteten, så ändras inte deras befintliga värden.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet

Med metoden **TableRestProxy->getEntity** kan du hämta en enskild entitet genom att fråga efter dess `PartitionKey` och `RowKey`. I exemplet nedan anges partitionsnyckeln `tasksSeattle` och radnyckeln `1` i metoden **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Entitetsfrågor skapas med hjälp av filter (mer information finns i [Querying Tables and Entities][filters] (Köra frågor mot tabeller och entiteter)). Om du vill hämta alla entiteter i en partition använder du filtret ”PartitionKey eq *partitionens_namn*”. I följande exempel beskriver vi hur du hämtar alla entiteter i partitionen `tasksSeattle` genom att ange ett filter i metoden **queryEntities**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Hämta en deluppsättning entiteter i en partition

Samma mönster som används i föregående exempel kan användas för att hämta en deluppsättning entiteter i en partition. Hur stor deluppsättning som hämtas beror på vilket filter du använder (mer information finns i [Querying Tables and Entities
][filters] (Köra frågor mot tabeller och entiteter)). Följande exempel visar hur du använder ett filter som hämtar alla entiteter med en specifik `Location` och ett `DueDate` som är mindre än det angivna datumet.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Hämta en deluppsättning entitetsegenskaper

En fråga kan hämta en deluppsättning entitetsegenskaper. Den här tekniken kallas *projektion*, minskar bandbredden och kan förbättra frågans prestanda, särskilt för stora entiteter. Du anger en egenskap som ska hämtas genom att skicka namnet på egenskapen till metoden **Query->addSelectField**. Du kan anropa den här metoden flera gånger om du vill lägga till fler egenskaper. När du har kört **TableRestProxy->queryEntities** har entiteterna som returneras endast de valda egenskaperna. (Om du vill returnera en deluppsättning tabellentiteter använder du ett filter som i frågorna ovan.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Uppdatera en entitet

Du kan uppdatera en befintlig entitet genom att använda metoderna **Entity->setProperty** och **Entity->addProperty** för entiteten och sedan anropa **TableRestProxy->updateEntity**. Koden i följande exempel hämtar en entitet, ändrar en egenskap, tar bort en annan egenskap och lägger till en ny egenskap. Observera att du kan ta bort en egenskap genom att ange dess värde till **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Om du vill ta bort en entitet anger du tabellnamnet och entitetens `PartitionKey` och `RowKey` i metoden **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

För samtidighetskontroller kan du ange Etag för en entitet som ska tas bort med hjälp av metoden **DeleteEntityOptions->setEtag** och skicka **DeleteEntityOptions**-objektet till **deleteEntity** som en fjärde parameter.

## <a name="batch-table-operations"></a>Tabellåtgärder i batchar

Med metoden **TableRestProxy->batch** kan du köra flera åtgärder i en enda begäran. Med det här mönstret lägger du till åtgärder i **BatchRequest**-objektet och lägger sedan till **BatchRequest**-objektet i metoden **TableRestProxy->batch**. Du lägger till en åtgärd i ett **BatchRequest**-objekt genom att anropa någon av följande metoder flera gånger:

* **addInsertEntity** (lägger till en insertEntity-åtgärd)
* **addUpdateEntity** (lägger till en updateEntity-åtgärd)
* **addMergeEntity** (lägger till en mergeEntity-åtgärd)
* **addInsertOrReplaceEntity** (lägger till en insertOrReplaceEntity-åtgärd)
* **addInsertOrMergeEntity** (lägger till en insertOrMergeEntity-åtgärd)
* **addDeleteEntity** (lägger till en deleteEntity-åtgärd)

Följande exempel visar hur du kör **insertEntity**- och **deleteEntity**-åtgärder i en enda begäran. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mer information om batchbearbetning av tabellåtgärder finns i [Utföra entitetsgrupptransaktioner][entity-group-transactions].

## <a name="delete-a-table"></a>Ta bort en tabell

Om du vill ta bort en tabell anger du tabellnamnet i metoden **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig grunderna om Azure Table Storage och Azure Cosmos DB. Om du vill lära dig mer följer du länkarna nedan.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
