---
title: Migrera från CouchBase till Azure Cosmos DB SQL API
description: Steg för steg-anvisningar för att migrera från CouchBase till Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606206"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrera från CouchBase till Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB är en skalbar, globalt distribuerad, fullständigt hanterad databas. Den ger garanterad låg latens åtkomst till dina data. Mer information om Azure Cosmos DB finns i [översikts](introduction.md) artikeln. Den här artikeln innehåller anvisningar för att migrera Java-program som är anslutna till Couchbase till ett SQL API-konto i Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Skillnader i nomenklaturen

Följande är viktiga funktioner som fungerar annorlunda i Azure Cosmos DB jämfört med Couchbase:

| Couchbase | Azure Cosmos DB |
|--|--|
| Couchbase-Server | Konto |
| Bucket | Databas |
| Bucket | Container/samling |
| JSON-dokument | Objekt/dokument |

## <a name="key-differences"></a>Viktiga skillnader

* Azure Cosmos DB har ett "ID"-fält i dokumentet medan Couchbase har ID som en del av Bucket. Fältet "ID" är unikt i hela partitionen.

* Azure Cosmos DB skalas med hjälp av partitionering eller horisontell partitionering-teknik. Det innebär att data delas upp i flera Shards/partitioner. Dessa partitioner/Shards skapas utifrån den partitionsnyckel som du anger. Du kan välja partitionsnyckel för att optimera Läs-och skriv åtgärder, eller läsa/skriva optimerat. Mer information finns i [partitionerings](./partitioning-overview.md) artikeln.

* I Azure Cosmos DB krävs det inte för att hierarkin på den översta nivån ska beteckna samlingen, eftersom samlings namnet redan finns. Den här funktionen gör JSON-strukturen mycket enklare. Följande är ett exempel som visar skillnader i data modellen mellan Couchbase och Azure Cosmos DB:

   **Couchbase**: dokument-ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: se "ID" i dokumentet på det sätt som visas nedan

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK-stöd

Azure Cosmos DB har följande SDK: er som stöder olika Java-ramverk:

* Asynkron SDK
* SDK för våren Boot

I följande avsnitt beskrivs när du ska använda var och en av dessa SDK: er. Överväg ett exempel där vi har tre typer av arbets belastningar:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase som dokument lagring & våren databaserade anpassade frågor

Om arbets belastningen som du migrerar baseras på våren Boot-baserad SDK kan du använda följande steg:

1. Lägg till överordnad till POM.xml-filen:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Lägg till egenskaper i POM.xml-filen:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Lägg till beroenden i POM.xml-filen:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Lägg till program egenskaper under resurser och ange följande. Ersätt parametrarna för URL, nyckel och databas namn:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definiera namnet på samlingen i modellen. Du kan också ange ytterligare anteckningar. Till exempel, ID, partitionsnyckel för att beteckna dem uttryckligen:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Följande är kodfragmenten för CRUD-åtgärder:

### <a name="insert-and-update-operations"></a>Infoga och uppdatera åtgärder

Där *_repo* är objektet för databasen och *dokument* är POJO-klassens objekt. Du kan använda `.save` för att infoga eller upsert (om dokument med angivet ID hittades). Följande kodfragment visar hur du infogar eller uppdaterar ett dokument objekt:

```_repo.save(doc);```

### <a name="delete-operation"></a>Ta bort åtgärd

Överväg följande kodfragment, där dokument-objekt har ID och partitionsnyckel måste anges för att hitta och ta bort objektet:

```_repo.delete(doc);```

### <a name="read-operation"></a>Läs åtgärd

Du kan läsa dokumentet med eller utan att ange partitionsnyckel. Om du inte anger partitionsnyckel, behandlas den som en fråga över partitioner. Överväg följande kod exempel, den första åtgärden utför åtgärden med hjälp av ID-och partitionsnyckel. I andra exemplet används ett vanligt fält & utan att ange fältet partitionsnyckel.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Nu kan du använda ditt program med Azure Cosmos DB. Fullständigt kod exempel för det exempel som beskrivs i det här dokumentet finns i [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) GitHub lagrings platsen.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase som en dokument lagrings plats & att använda N1QL-frågor

N1QL-frågor är ett sätt att definiera frågor i Couchbase.

|N1QL-fråga | Azure CosmosDB-fråga|
|-------------------|-------------------|
|Välj META ( `TravelDocument` ). ID som ID, `TravelDocument` . * från `TravelDocument` WHERE `_type` = "com. xx. xx. xx. xxx. xxx. xxxx" och Country = "Indien" och alla m i viseringar uppfyller m. type = = ' Multi-Entry ' och m. Country i [' Indien ', Bhutan '] order by ` Validity` DESC Limit 25 offset 0 | Välj c. ID, c från c JOIN m i c. Country = "Indien" där c._type = "com. xx. xx. xx. xxx. xxx. xxxx" och c. Country = "Indien" och m. type = ' Multi-Entry ' och m. Country i (' Indien ', ' Bhutan ') ORDER BY c |

Du kan observera följande ändringar i dina N1QL-frågor:

* Du behöver inte använda nyckelordet META eller referera till dokumentet på den första nivån. I stället kan du skapa en egen referens till behållaren. I det här exemplet har vi bedömt det som "c" (det kan vara vad som helst). Den här referensen används som ett prefix för alla fält på den första nivån. Fr. ex. c.id, c. land osv.

* I stället för "alla" kan du göra en koppling i under dokument och referera till det med ett dedikerat alias, till exempel "m". När du har skapat ett alias för ett under dokument måste du använda alias. Till exempel m. Country.

* FÖRSKJUTNINGs ordningen är annorlunda i Azure Cosmos DB fråga, först måste du ange förskjutning och sedan begränsa. Vi rekommenderar att du inte använder våren data SDK om du använder maximalt anpassade, definierade frågor eftersom det kan ha onödig till gång på klient sidan när du skickar frågan till Azure Cosmos DB. I stället har vi en direkt asynkron Java SDK, som kan utnyttjas mycket effektivt i det här fallet.

### <a name="read-operation"></a>Läs åtgärd

Använd asynkron Java SDK med följande steg:

1. Konfigurera följande beroende till POM.xml-filen:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Skapa ett anslutnings objekt för Azure Cosmos DB med hjälp av `ConnectionBuilder` metoden som visas i följande exempel. Se till att du har placerat den här deklarationen i bönor så att följande kod bara ska köras en gång:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Om du vill köra frågan måste du köra följande kodfragment:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nu kan du med hjälp av metoden ovan skicka flera frågor och köra dem utan besvär. Om du har krav på att köra en stor fråga som kan delas upp i flera frågor kan du prova följande kodfragment i stället för föregående:

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

Med föregående kod kan du köra frågor parallellt och öka de distribuerade körningarna för att optimera. Du kan även köra åtgärderna INSERT och Update för:

### <a name="insert-operation"></a>Infoga åtgärd

Om du vill infoga dokumentet kör du följande kod:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Prenumerera sedan på mono som:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert-åtgärd

Upsert-åtgärden kräver att du anger det dokument som behöver uppdateras. Om du vill hämta hela dokumentet kan du använda kodfragmentet som anges under rubriken Läs åtgärd och sedan ändra de obligatoriska fälten. I följande kodfragment upsertar dokumentet:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Prenumerera sedan på mono. Se det svartvita prenumerations avsnittet i infognings åtgärden.

### <a name="delete-operation"></a>Ta bort åtgärd

Följande kodfragment tar bort:

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Prenumerera sedan på svartvit, se avsnittet mono-prenumeration i INSERT-åtgärden. Det fullständiga kod exemplet är tillgängligt i [CouchbaseToCosmosDB-AsyncInSpring GitHub-](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) lagrings platsen.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase som ett nyckel/värde-par

Det här är en enkel typ av arbets belastning där du kan utföra sökningar i stället för frågor. Använd följande steg för nyckel/värde-par:

1. Överväg att använda "/ID" som primär nyckel, vilket ser till att du kan utföra söknings åtgärder direkt i den angivna partitionen. Skapa en samling och ange "/ID" som partitionsnyckel.

1. Stäng av indexeringen helt. Eftersom du kommer att köra söknings åtgärder finns det ingen punkt för att utföra indexerings kostnader. Om du vill stänga av indexeringen loggar du in på Azure Portal, goto Azure Cosmos DB-konto. Öppna **datautforskaren**, välj din **databas** och **behållaren**. Öppna fliken **skalnings & inställningar** och välj  **indexerings princip**. Index princip som är för närvarande ser ut så här:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Ersätt index principen ovan med följande princip:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Använd följande kodfragment för att skapa objektet anslutning. Anslutnings objekt (som ska placeras i @Bean eller vara statiskt):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Nu kan du köra CRUD-åtgärderna på följande sätt:

### <a name="read-operation"></a>Läs åtgärd

Använd följande kodfragment för att läsa objektet:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Infoga åtgärd

Om du vill infoga ett objekt kan du utföra följande kod:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Prenumerera sedan på mono som:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert-åtgärd

Om du vill uppdatera värdet för ett objekt, referera till kodfragmentet nedan:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Prenumerera sedan på svartvit, se avsnittet mono-prenumeration i INSERT-åtgärden.

### <a name="delete-operation"></a>Ta bort åtgärd

Använd följande kodfragment för att köra borttagnings åtgärden:

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Prenumerera sedan på svartvit, se avsnittet mono-prenumeration i INSERT-åtgärden. Det fullständiga kod exemplet är tillgängligt i [CouchbaseToCosmosDB-AsyncKeyValue GitHub-](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) lagrings platsen.

## <a name="data-migration"></a>Datamigrering

Det finns två sätt att migrera data.

* **Använd Azure Data Factory:** Detta är den mest rekommenderade metoden för att migrera data. Konfigurera källan som Couchbase och Sink som Azure Cosmos DB SQL API, se artikeln Azure [Cosmos DB Data Factory Connector](../data-factory/connector-azure-cosmos-db.md) för detaljerade anvisningar.

* **Använd verktyget Azure Cosmos db data import:** Det här alternativet rekommenderas för att migrera med virtuella datorer med mindre mängd data. Detaljerade anvisningar finns i artikeln om [data import](./import-data.md) .

## <a name="next-steps"></a>Nästa steg

* För att utföra prestanda testning, se [prestanda-och skalnings testning med Azure Cosmos DB](./performance-testing.md) artikel.
* Information om hur du optimerar koden finns i [prestanda tips för Azure Cosmos DB](./performance-tips-async-java.md) artikel.
* Utforska Java async v3 SDK, [SDK Reference](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub lagrings platsen.
