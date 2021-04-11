---
title: Använda Azure Cosmos DB Tabell-API och Azure Table Storage med python
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage eller Azure Cosmos DB Tabell-API med hjälp av python.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 03/23/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 19a73f17fcb1f6f51dd2ed80b9e68a51d0d7ceb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044710"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Komma igång med Azure Table Storage och Azure Cosmos DB Table-API:et med hjälp av Python
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage och Azure Cosmos DB är tjänster som lagrar strukturerade NoSQL-data i molnet, vilket ger ett nyckel-attributarkiv med en schema lös design. Eftersom Table Storage och Azure Cosmos DB är schemalösa kan du enkelt anpassa dina data baserat på hur ditt program utvecklas. Åtkomst till tabellerna Storage och table API data är snabb och kostnads effektiv för många typer av program och är vanligt vis lägre än traditionell SQL för liknande data volymer.

Du kan använda Table Storage eller Azure Cosmos DB för att lagra flexibla data uppsättningar som användar data för webb program, adress böcker, enhets information eller andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

### <a name="about-this-sample"></a>Om det här exemplet

Det här exemplet beskriver hur du använder [Azure Cosmos DB Table SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) i vanliga Azure Table Storage-scenarier. SDK-paketets namn indikerar att det ska användas med Azure Cosmos DB, men det fungerar med både Azure Cosmos DB och Azure Table Storage. Enda skillnaden är att tjänsterna har unika slutpunkter. De olika scenarierna utforskas med hjälp av Python-baserade exempel som beskriver hur du:

* Skapar och tar bort tabeller
* Infogar och kör frågor mot entiteter
* Ändrar entiteter

Vi rekommenderar att du använder [referensen för Azure Cosmos DB SDK för Python API](/python/api/overview/azure/cosmosdb) när du går igenom scenarierna i det här exemplet.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna följa med i det här exemplet:

* [Python](https://www.python.org/downloads/) 2,7 eller 3.6 +.
* [Azure Cosmos DB Table SDK för python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Detta SDK fungerar med både Azure Table Storage och Azure Cosmos DB Table-API:et.
* [Azure Storage konto](../storage/common/storage-account-create.md) eller [Azure Cosmos DB konto](https://azure.microsoft.com/try/cosmosdb/).

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Skapa ett Azure Storage-konto**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Skapa ett Azure Cosmos DB Table API-konto**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Installera Azure Cosmos DB Table SDK för Python

När du har skapat ett lagringskonto är nästa steg att installera [Microsoft Azure Cosmos DB Table SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Mer information om hur du installerar SDK-paketet finns i filen [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) i databasen för Cosmos DB Table SDK för Python på GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importera TableService- och Entity-klasserna

När du arbetar med entiteter i Azure Table-tjänsten i Python använder du klasserna [TableService][py_TableService] och [Entity][py_Entity]. Importera båda klasserna genom att lägga till följande kod överst i Python-filen:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Ansluta till Azure Table Storage

Du ansluter till tjänsten Table Storage i Azure Storage genom att skapa ett [TableService][py_TableService]-objekt som innehåller namnet på ditt lagringskonto och din kontonyckel. Ersätt `myaccount` och `mykey` med kontonamnet och nyckeln.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Du ansluter till Azure Cosmos DB genom att kopiera den primära anslutningssträngen från Azure-portalen och skapar sedan ett [TableService][py_TableService]-objekt genom att använda den kopierade anslutningssträngen:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Skapa en tabell

Skapa tabellen genom att anropa [create_table][py_create_table].

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Du lägger till en entitet genom att först skapa ett objekt som representerar entiteten. Därefter skickar du objektet till [metoden TableService.insert_entity][py_TableService]. Entitetsobjektet kan vara en ordlista eller ett objekt av typen [Entity][py_Entity] och definierar entitetens egenskapsnamn och värden. Varje entitet måste innehålla de obligatoriska [PartitionKey- och RowKey](#partitionkey-and-rowkey)-egenskaperna, utöver andra egenskaper som du definierar för entiteten.

I det här exemplet skapas ett ordlisteobjekt som representerar en entitet. Objektet skickas sedan till metoden [insert_entity][py_insert_entity] som lägger till det i tabellen:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

I det här exemplet skapas ett [Entity][py_Entity]-objekt. Objektet skickas sedan till metoden [insert_entity][py_insert_entity] som lägger till det i tabellen:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey och RowKey

Du måste ange både en **PartitionKey**- och en **RowKey**-egenskap för varje entitet. Dessa är entiteternas unika identifierare eftersom de tillsammans bildar primärnyckeln för en entitet. Du kan fråga mycket snabbare med dessa värden än du kan fråga andra entitetsegenskaper eftersom endast dessa egenskaper indexeras.

Table Storage använder tabellen **PartitionKey** för att effektivt distribuera tabellentiteter mellan lagringsnoder. Entiteter som har samma **PartitionKey** lagras på samma nod. **RowKey** är det unika ID:t för entiteten i den partition som egenskapen hör till.

## <a name="update-an-entity"></a>Uppdatera en entitet

Du kan uppdatera alla egenskapsvärden för en entitet genom att anropa metoden [update_entity][py_update_entity]. Det här exemplet beskriver hur du ersätter en befintlig entitet med en uppdaterad version:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Uppdateringen misslyckas om entiteten som ska uppdateras inte redan finns. Om du vill lagra en entitet oavsett om den finns eller inte använder du [insert_or_replace_entity][py_insert_or_replace_entity]. I följande exempel ersätter det första anropet den befintliga entiteten. Det andra anropet infogar en ny entitet eftersom det inte finns någon entitet med angiven partitionsnyckel (PartitionKey) och radnyckel (RowKey) i tabellen.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Metoden [update_entity][py_update_entity] ersätter alla egenskaper och värden för en befintlig entitet, och kan också användas för att ta bort egenskaper från en befintlig entitet. Du kan använda metoden [merge_entity][py_merge_entity] om du vill uppdatera en befintlig entitet med nya eller ändrade egenskapsvärden utan att helt ersätta entiteten.

## <a name="modify-multiple-entities"></a>Ändra flera entiteter

Du kan skicka flera åtgärder tillsammans i en batch för att säkerställa atomisk bearbetning av en begäran i Table Storage. Börja med att lägga till flera åtgärder i en batch med hjälp av klassen [TableBatch][py_TableBatch]. Skicka sedan åtgärderna i en atomisk åtgärd genom att anropa [TableService][py_TableService].[commit_batch][py_commit_batch]. Alla entiteter som ska ändras tillsammans i en batch måste finnas i samma partition.

I det här exemplet läggs två entiteter till i en batch:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Batchar kan också användas med kontexthanterarsyntaxen:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Fråga efter en entitet

Du kan hämta en entitet i en tabell genom att skicka entitetens PartitionKey och RowKey till metoden [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Fråga efter en uppsättning entiteter

Du kan hämta en uppsättning entiteter genom att ange en filtersträng med parametern **filter**. Det här exemplet hämtar alla aktiviteter i Seattle genom att tillämpa ett filter på PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

Du kan också begränsa vilka egenskaper som returneras för varje entitet i en fråga. Den här tekniken, kallad *projektion*, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter eller resultatuppsättningar. Använd parametern **select** och ange namnen på egenskaperna som ska returneras till klienten.

Frågan i följande kod returnerar bara beskrivningarna av entiteter i tabellen.

> [!NOTE]
> Följande kodfragment fungerar bara mot Azure Storage. Den stöds inte av Storage-emulatorn.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="query-for-an-entity-without-partition-and-row-keys"></a>Fråga efter en entitet utan partition och rad nycklar

Du kan också fråga efter entiteter i en tabell utan att använda partitions-och rad nycklar. Använd `table_service.query_entities` metoden utan parametrarna "filter" och "Välj" som visas i följande exempel:

```python
print("Get the first item from the table")
tasks = table_service.query_entities(
    'tasktable')
lst = list(tasks)
print(lst[0])
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Du kan ta bort en entitet genom att ange entitetens **PartitionKey** och **RowKey** i metoden [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Ta bort en tabell

Om du inte längre behöver en tabell eller någon av entiteterna i den kan du ta bort tabellen permanent från Azure Storage genom att anropa metoden [delete_table][py_delete_table].

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar – Utveckla med Table-API:et](./faq.md)
* [Referens för Azure Cosmos DB SDK för Python API](/python/api/overview/azure/cosmosdb)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): Ett kostnadsfritt, plattformsoberoende program som låter dig arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Arbeta med Python i Visual Studio (Windows)](/visualstudio/python/overview-of-python-tools-for-visual-studio)



[py_commit_batch]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_create_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_delete_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_get_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_insert_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_insert_or_replace_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_Entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity
[py_merge_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_update_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_delete_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_TableService]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
