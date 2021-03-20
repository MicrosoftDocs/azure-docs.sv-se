---
title: Migrera ditt program från Amazon DynamoDB till Azure Cosmos DB
description: Lär dig hur du migrerar ditt .NET-program från Amazons DynamoDB till Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334233"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migrera ditt program från Amazon DynamoDB till Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB är en skalbar, globalt distribuerad, fullständigt hanterad databas. Den ger garanterad låg latens åtkomst till dina data. Mer information om Azure Cosmos DB finns i [översikts](introduction.md) artikeln. I den här artikeln beskrivs hur du migrerar ditt .NET-program från DynamoDB till Azure Cosmos DB med minimala kod ändringar.

## <a name="conceptual-differences"></a>Konceptuella skillnader

Här följer de viktiga koncept skillnaderna mellan Azure Cosmos DB och DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Inte tillämpligt|  Databas |
|Tabell      |  Samling |
|  Objekt |  Dokument |
|Attribut|Fält|
|Sekundärt index|Sekundärt index|
|Primär nyckel – partitionsnyckel|Partition Key (Partitionsnyckel)|
|Primär nyckel – sorterings nyckel| Krävs inte |
|Stream|ChangeFeed|
|Skriv beräknings enhet|Enhet för begäran (flexibel, kan användas för läsningar eller skrivningar)|
|Läs beräknings enhet    |Enhet för begäran (flexibel, kan användas för läsningar eller skrivningar)|
|Globala tabeller| Krävs inte. Du kan välja region direkt när du konfigurerar Azure Cosmos-kontot (du kan ändra region senare)|

## <a name="structural-differences"></a>Strukturella skillnader

Azure Cosmos DB har en enklare JSON-struktur jämfört med den av DynamoDB. I följande exempel visas skillnaderna

**DynamoDB**:

Följande JSON-objekt representerar data formatet i DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

Följande JSON-objekt representerar data formatet i Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrera dina data

Det finns flera tillgängliga alternativ för att migrera dina data till Azure Cosmos DB. Mer information finns i [alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB](cosmosdb-migrationchoices.md) artikel.

## <a name="migrate-your-code"></a>Migrera din kod

Den här artikeln är begränsad till att migrera ett programs kod till Azure Cosmos DB, vilket är den viktigaste aspekten av migreringen av databasen. För att hjälpa dig att minska inlärnings kurvan, innehåller följande avsnitt en jämförelse av kod sida vid sida mellan Amazon DynamoDB och Azure Cosmos DB motsvarande kodfragment.

För att ladda ned käll koden, klona följande lagrings platsen:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Förutsättningar

- .NET Framework 4.7.2
- Visual Studio 2019
- Åtkomst till Azure Cosmos DB SQL API-konto
- Lokal installation av Amazon DynamoDB
- Java 8
- Kör den nedladdnings bara versionen av Amazon DynamoDB på port 8000 (du kan ändra och konfigurera koden)

### <a name="set-up-your-code"></a>Konfigurera din kod

Lägg till följande "NuGet-paket" i projektet:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Upprätta anslutning

**DynamoDB**:

I Amazon-DynamoDB används följande kod för att ansluta:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Om du vill ansluta Azure Cosmos DB uppdaterar du koden till:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optimera anslutningen i Azure Cosmos DB**

Med Azure Cosmos DB kan du använda följande alternativ för att optimera anslutningen:

* **ConnectionMode** – Använd direkt anslutnings läge för att ansluta till datanoderna i Azure Cosmos DBS tjänsten. Använd endast Gateway-läge för att initiera och cachelagra logiska adresser och uppdatera uppdateringar. Se artikeln [anslutnings lägen](sql-sdk-connection-modes.md) för mer information.

* **ApplicationRegion** – det här alternativet används för att ange önskad geo-replikerad region som används för att interagera med Azure Cosmos dB. Mer information finns i den [globala distributions](distribute-data-globally.md) artikeln.

* **ConsistencyLevel** – det här alternativet används för att åsidosätta standard konsekvens nivån. Mer information finns i artikeln [konsekvens nivåer](consistency-levels.md) .

* **BulkExecutionMode** – det här alternativet används för att köra Mass åtgärder genom att ange egenskapen *AllowBulkExecution* till true. Mer information finns i artikeln [Mass import](tutorial-sql-api-dotnet-bulk-import.md) .

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Etablera behållaren

**DynamoDB**:

För att lagra data i Amazon-DynamoDB måste du först skapa tabellen. I den här processen definierar du schemat, nyckel typen och attributen som visas i följande kod:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

I Amazon DynamoDB måste du etablera de Läs beräknings enheterna & skriva beräknings enheter. I Azure Cosmos DB du ange data flödet som [begär ande enheter (ru/s)](request-units.md)som kan användas för alla åtgärder dynamiskt. Data ordnas som databas--> container--> objekt. Du kan ange data flödet på databas nivå eller på samlings nivå eller både och.

Så här skapar du en databas:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Skapa behållaren:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Läsa in data

**DynamoDB**:

Följande kod visar hur du läser in data i Amazon-DynamoDB. MoviesArray består av en lista med JSON-dokument och du måste sedan iterera genom och läsa in JSON-dokumentet till Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

I Azure Cosmos DB kan du välja att strömma och skriva med `moviesContainer.CreateItemStreamAsync()` . I det här exemplet deserialiseras dock JSON till *MovieModel* -typen för att demonstrera typ data typs funktionen. Koden är flertrådad, vilket kommer att använda Azure Cosmos DBs distribuerade arkitektur och påskynda inläsningen:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Skapa ett dokument

**DynamoDB**:

Att skriva ett nytt dokument i Amazon DynamoDB är inte av typen säker. i följande exempel används newItem som dokument typ:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB anger säkerheten via data modellen. Vi använder data modellen "MovieModel":

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

I Azure Cosmos DB newItem kommer att vara MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Läsa ett dokument

**DynamoDB**:

För att läsa in Amazon-DynamoDB måste du definiera primitiver:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Men med Azure Cosmos DB frågan är naturlig (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

Dokument samlingen i exemplet ovan blir:

- typ säker
- Ange ett alternativ för naturlig fråga.

### <a name="update-an-item"></a>Uppdatera ett objekt

**DynamoDB**: uppdatera objektet i Amazon-DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

I Azure Cosmos DB behandlas uppdateringen som upsert-åtgärd som innebär Infoga dokumentet om det inte finns:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Ta bort ett dokument

**DynamoDB**:

Om du vill ta bort ett objekt i Amazon-DynamoDB måste du använda primitiver igen:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

I Azure Cosmos DB kan vi hämta dokumentet och ta bort dem asynkront:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Köra en fråga mot dokument

**DynamoDB**:

I Amazon DynamoDB krävs API-funktioner för att skicka frågor till data:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

I Azure Cosmos DB kan du göra projektion och filter i en enkel SQL-fr åga:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

För intervall åtgärder, till exempel ' mellan ', måste du göra en genomsökning i Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

I Azure Cosmos DB kan du använda SQL-fråga och ett enda rad uttryck:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Ta bort en container

**DynamoDB**:

Om du vill ta bort tabellen i Amazon-DynamoDB kan du ange:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Om du vill ta bort samlingen i Azure Cosmos DB kan du ange:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Ta sedan bort databasen för om du behöver:

```csharp
await cosmosDatabase.DeleteAsync();
```

Som du kan se, Azure Cosmos DB stöder naturliga frågor (SQL), är åtgärder asynkrona och mycket enklare. Du kan enkelt migrera din komplexa kod till Azure Cosmos DB, vilket blir enklare efter migreringen.

### <a name="next-steps"></a>Nästa steg

- Lär dig mer om [prestanda optimering](performance-tips.md).
- Lär dig mer om att [optimera läsningar och skrivningar](key-value-store-cost.md)
- Läs mer om [övervakning i Cosmos DB](monitor-cosmos-db.md)

