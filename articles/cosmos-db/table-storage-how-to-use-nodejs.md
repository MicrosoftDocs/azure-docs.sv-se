---
title: Använd Azure Table Storage eller Azure Cosmos DB Tabell-API från Node.js
description: Lagra strukturerade data i molnet med Azure Table Storage eller Azure Cosmos DB Tabell-API från Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079719"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Använda Azure Table Storage eller Azure Cosmos DB Table-API:et från Node.js
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Den här artikeln visar hur du skapar tabeller, lagrar data och utför CRUD-åtgärder på data. Välj antingen Azure-Table service eller Azure Cosmos DB Tabell-API. Exemplen skrivs i Node.js.

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Skapa ett Azure Storage-konto**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Skapa ett Azure Cosmos DB Table API-konto**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Konfigurera ditt program för åtkomst till Azure Storage eller Azure Cosmos DB Table-API:et

För att använda Azure Storage eller Azure Cosmos DB behöver du Azure Storage SDK för Node.js, som innehåller en uppsättning verktygsbibliotek som kommunicerar med Storage REST-tjänsterna.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)

1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix) och navigera till mappen där du skapade ditt program.
2. Skriv **npm install azure-storage** i kommandofönstret. Kommandot returnerar utdata liknande dem i exemplet nedan.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Du kan kontrollera att mappen **node_modules** har skapats genom att köra kommandot **ls** manuellt. I mappen hittar du paketet **azure storage**, som innehåller de bibliotek som du behöver för att få åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet

Lägg till följande kod överst i filen **server.js** i ditt program:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Lägg till din anslutnings sträng

Du kan antingen ansluta till Azure Storage-kontot eller Azure Cosmos DB Tabell-API-kontot. Hämta anslutnings strängen baserat på vilken typ av konto som du använder.

### <a name="add-an-azure-storage-connection"></a>Lägga till en Azure Storage-anslutning

Azure-modulen läser miljövariablerna AZURE_STORAGE_ACCOUNT och AZURE_STORAGE_ACCESS_KEY eller AZURE_STORAGE_CONNECTION_STRING och letar efter information som behövs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen när du anropar `TableService` . Följande kod skapar till exempel ett- `TableService` objekt:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Lägga till en Azure Cosmos DB-anslutning

Om du vill lägga till en Azure Cosmos DB anslutning skapar du ett- `TableService` objekt och anger ditt konto namn, primär nyckel och slut punkt. Du kan kopiera dessa värden från **Inställningar**  >  **anslutnings sträng** i Azure Portal för ditt Cosmos DB-konto. Exempel:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Skapa en tabell

Följande kod skapar ett- `TableService` objekt och använder det för att skapa en ny tabell.

```javascript
var tableSvc = azure.createTableService();
```

Anropet till `createTableIfNotExists` skapar en ny tabell med det angivna namnet om det inte redan finns. I följande exempel skapas en ny tabell med namnet ”mytable” om den inte redan finns:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

`result.created` är `true` om en ny tabell skapas och `false` om tabellen redan finns. `response` innehåller information om begäran.

### <a name="apply-filters"></a>Använda filter

Du kan använda valfri filtrering för åtgärder som utförs med `TableService` . Filtrerings åtgärder kan omfatta loggning, automatiska återförsök osv. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

Efter den förberedande bearbetningen av alternativen för begäran måste metoden anropa **next** och skicka ett återanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I det här återanropet, och efter bearbetning av `returnObject` (svaret från begäran till servern), måste återanropet antingen anropa `next` om det finns för att fortsätta att bearbeta andra filter eller bara anropa `finalCallback` för att avsluta tjänst anropet.

Två filter som implementerar logik för omprövning ingår i Azure SDK för Node.js `ExponentialRetryPolicyFilter` och `LinearRetryPolicyFilter` . Följande skapar ett- `TableService` objekt som använder `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Du lägger till en entitet genom att först skapa ett objekt som definierar entitetens egenskaper. Alla entiteter måste innehålla en **PartitionKey** och **RowKey**, som är unika identifierare för entiteten.

* **PartitionKey** – Anger i vilken partition entiteten lagras.
* **RowKey** – Identifierar entiteten i partitionen unikt.

Både **PartitionKey** och **RowKey** måste vara strängvärden. Mer information finns i [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Så här fungerar datamodellen för Table Storage).

Följande är ett exempel på hur du definierar en entitet. **DueDate** definieras som en typ av `Edm.DateTime` . Typen är inte obligatorisk, och typer härleds om de inte anges.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Det finns också ett `Timestamp` fält för varje post, som anges av Azure när en entitet infogas eller uppdateras.

Du kan också använda `entityGenerator` för att skapa entiteter. I följande exempel skapas samma entitet för aktiviteter med hjälp av `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Om du vill lägga till en entitet i tabellen skickar du objektet Entity till- `insertEntity` metoden.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Om åtgärden lyckas innehåller `result` den infogade postens [ETag](https://en.wikipedia.org/wiki/HTTP_ETag), och `response` innehåller information om åtgärden.

Exempelsvar:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Som standard `insertEntity` returnerar inte den infogade entiteten som en del av `response` informationen. Om du planerar att utföra andra åtgärder med den här entiteten eller om du vill cachelagra informationen, kan det vara bra att returnera den som en del av `result`. Du kan göra detta genom att aktivera följande `echoContent` :
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Uppdatera en entitet

Du kan uppdatera en befintlig entitet med hjälp av olika metoder:

* `replaceEntity` – Uppdaterar en befintlig entitet genom att ersätta den.
* `mergeEntity` -Uppdaterar en befintlig entitet genom att sammanfoga nya egenskaps värden i den befintliga entiteten.
* `insertOrReplaceEntity` – Uppdaterar en befintlig entitet genom att ersätta den. Om det inte finns någon entitet, infogas en ny.
* `insertOrMergeEntity` -Uppdaterar en befintlig entitet genom att sammanfoga nya egenskaps värden i den befintliga. Om det inte finns någon entitet, infogas en ny.

I följande exempel visas hur du uppdaterar en entitet med hjälp av `replaceEntity` :

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> När du uppdaterar en entitet utförs ingen kontroll som standard för att se om de data som uppdateras har ändrats tidigare av en annan process. Om du vill använda samtidiga uppdateringar:
>
> 1. Hämta ETag för objektet som uppdateras. Värdet returneras som en del av `response` för entitetsrelaterade åtgärder och kan hämtas via `response['.metadata'].etag`.
> 2. När du utför en uppdateringsåtgärd för en entitet lägger du till ETag-informationen som du hämtade till den nya entiteten. Exempel:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Kör uppdateringsåtgärden. Om entiteten har ändrats sedan du hämtade ETag-värdet, till exempel en annan instans av programmet, returneras `error` och anger att uppdateringsvillkoret som angavs i begäran inte uppfylldes.
>
>

Med `replaceEntity` och `mergeEntity` , om den entitet som uppdateras inte finns, Miss lyckas uppdateringen. om du vill lagra en entitet oavsett om den redan finns använder du `insertOrReplaceEntity` eller `insertOrMergeEntity` .

För lyckade uppdateringsåtgärder innehåller `result` den uppdaterade entitetens **Etag**.

## <a name="work-with-groups-of-entities"></a>Arbeta med grupper av entiteter

Ibland är det praktiskt att skicka flera åtgärder tillsammans i en batch för att säkerställa atomisk bearbetning av servern. För att åstadkomma detta använder du `TableBatch` klassen för att skapa en batch och använder sedan `executeBatch` metoden för `TableService` att utföra batch-operationen.

 Exemplet nedan beskriver hur du skickar två entiteter i en batch:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

För lyckade batchåtgärder innehåller `result` information om varje åtgärd i batchen.

### <a name="work-with-batched-operations"></a>Arbeta med batchåtgärder

Du kan inspektera åtgärder som lagts till i en batch genom att visa egenskapen `operations`. Du kan också arbeta med åtgärder med hjälp av följande metoder:

* **clear** – Rensar alla åtgärder från en batch.
* **getOperations** – Hämtar en åtgärd från batchen.
* **hasOperations** – Returnerar true om batchen innehåller åtgärder.
* **removeOperations** – Tar bort en åtgärd.
* **size** – Returnerar antalet åtgärder i batchen.

## <a name="retrieve-an-entity-by-key"></a>Hämta en entitet baserat på nyckel

Om du vill returnera en specifik entitet baserat på **PartitionKey** och **RowKey** använder du metoden **retrieveEntity**.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

När den här åtgärden har slutförts innehåller `result` entiteten.

## <a name="query-a-set-of-entities"></a>Fråga efter en uppsättning entiteter

Om du vill hämta data från en tabell använder du objektet **TableQuery** för att skapa ett frågeuttryck med hjälp av följande satser:

* **select** – Fälten som ska returneras från frågan.
* **where** – where-satsen.

  * **and** – Ett ”`and` where”-villkor.
  * **or** – Ett ”`or` where”-villkor.
* **top** – Antalet objekt som ska hämtas.

I följande exempel skapas en fråga som returnerar de fem översta posterna med partitionsnyckeln (PartitionKey) ”hometasks”.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Eftersom **select** inte används returneras alla fält. Om du vill köra frågan mot en tabell använder du **queryEntities**. I följande exempel används den här frågan för att returnera entiteter från ”mytable”.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Om åtgärden lyckas innehåller `result.entries` en matris med entiteter som matchar frågan. Om frågan inte kan returnera alla entiteter är `result.continuationToken` inte *null* och kan användas som den tredje parametern för **queryEntities** för att hämta fler resultat. I den ursprungliga frågan använder du *null* som den tredje parametern.

### <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

En fråga till en tabell kan bara hämta några fält från en entitet.
Detta minskar bandbredden och kan förbättra frågeprestanda, särskilt för stora entiteter. Använd **select**-satsen och ange namnen på fälten som ska returneras. Följande fråga returnerar till exempel bara **description**- och **dueDate**-fälten.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Du kan ta bort en entitet med hjälp av dess partitions- och radnycklar. I det här exemplet innehåller **task1**-objektet **RowKey**- och **PartitionKey**-värdena för entiteten som ska tas bort. Objektet skickas sedan till metoden **deleteEntity**.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Använd ETags när du tar bort objekt för att säkerställa att de inte har ändrats av en annan process. Information om hur du använder ETags finns i [Uppdatera en entitet](#update-an-entity).
>
>

## <a name="delete-a-table"></a>Ta bort en tabell

Följande kod tar bort en tabell från ett lagringskonto.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Om du är osäker på om tabellen finns använder du **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Använda fortsättningstoken

Leta efter en fortsättningstoken när du frågar tabeller efter stora mängder resultat. Stora mängder data kan finnas tillgängliga för din fråga, som du kanske missar om du inte uppmärksammar en fortsättningstoken.

**results**-objektet som returneras när du kör frågor mot entiteter anger en `continuationToken`-egenskap när den här typen av token finns. Du kan sedan använda denna token när du kör en fråga för att fortsätta att flytta mellan partitionen och tabellentiteterna.

När du frågar kan du lägga till en `continuationToken`-parameter mellan instansen av frågeobjektet och återanropsfunktionen:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Om du granskar `continuationToken`-objektet hittar du egenskaper som `nextPartitionKey`, `nextRowKey` och `targetLocation`, som du kan använda för att gå igenom alla resultat.

Du kan också använda `top` tillsammans med `continuationToken` för att ange sid storlek.

## <a name="work-with-shared-access-signatures"></a>Arbeta med signaturer för delad åtkomst

Signaturer för delad åtkomst (SAS) är ett säkert sätt att ge detaljerad åtkomst till tabeller utan att ange namnet på eller nycklarna för ditt lagringskontot. SAS används ofta för att ge begränsad åtkomst till data, till exempel om du vill tillåta att en mobilapp frågar efter poster.

Ett betrott program, till exempel en molnbaserad tjänst, genererar en SAS med hjälp av **generateSharedAccessSignature** för **TableService**, och delar den sedan med ett program som inte är betrott eller endast delvis betrott, t.ex. en mobilapp. Signaturen för delad åtkomst genereras med hjälp av en princip, som definierar mellan vilket start- och slutdatum signaturen för delad åtkomst är giltig, samt vilken åtkomstnivå SAS-innehavaren beviljas.

I följande exempel genereras en ny SAS-princip som ger innehavaren av signaturen för delad åtkomst tillåtelse att fråga (”r”) tabellen. Den här principen upphör att gälla 100 minuter efter den tidpunkt då den skapas.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Observera att du också måste ange värdinformationen eftersom den krävs när SAS-innehavaren försöker få åtkomst till tabellen.

Klientprogrammet använder sedan signaturen för delad åtkomst med **TableServiceWithSAS** för att köra åtgärder mot tabellen. Koden i följande exempel ansluter till tabellen och kör en fråga. Se [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av SAS-artikel (signatur för delad åtkomst)](../storage/common/storage-sas-overview.md) för tabell formatet.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Eftersom signaturen för delad åtkomst endast genererades med frågebehörighet, returneras ett fel om du försöker infoga, uppdatera eller ta bort entiteter.

### <a name="access-control-lists"></a>Åtkomstkontrollistor

Du kan också använda en åtkomstkontrollista (ACL) för att definiera åtkomstprincipen för en signatur för delad åtkomst. Detta är användbart om du vill att flera klienter ska kunna komma åt tabellen, men du vill använda olika åtkomstprinciper för varje klient.

En åtkomstkontrollista implementeras med hjälp av en matris med åtkomstprinciper, med ett ID som associeras med varje princip. I följande exempel definieras två principer, en för ”user1” och en för ”user2”:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Koden i följande exempel hämtar den aktuella åtkomstkontrollistan för tabellen **hometasks** och lägger sedan till de nya principerna med hjälp av **setTableAcl**. Med den här metoden kan du göra följande:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

När åtkomstkontrollistan har skapats kan du skapa en signatur för delad åtkomst baserat på ID:t för en princip. I följande exempel skapas en ny signatur för delad åtkomst för ”user2”:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i resurserna nedan.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Storage SDK för Node.js](https://github.com/Azure/azure-storage-node)-databas på GitHub.
* [Azure för Node.js-utvecklare](/azure/developer/javascript/)
* [Skapa en Node.js-webbapp i Azure](../app-service/quickstart-nodejs.md)
* [Skapa och distribuera ett Node.js-program till en Azure-molntjänst](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (med hjälp av Windows PowerShell)