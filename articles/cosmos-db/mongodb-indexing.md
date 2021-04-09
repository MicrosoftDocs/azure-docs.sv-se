---
title: Hantera indexering i Azure Cosmos DB-API för MongoDB
description: Den här artikeln innehåller en översikt över Azure Cosmos DB indexerings funktioner med Azure Cosmos DB s API för MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/02/2021
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 8d19a5dadffdfa26ccb2d84e6dab278ad272c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658054"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Hantera indexering i Azure Cosmos DB-API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DBs API för MongoDB drar nytta av kärn funktionerna för hantering av Azure Cosmos DB. Den här artikeln fokuserar på hur du lägger till index med Azure Cosmos DB s API för MongoDB. Du kan också läsa en [Översikt över indexering i Azure Cosmos DB](index-overview.md) som är relevant för alla API: er.

## <a name="indexing-for-mongodb-server-version-36-and-higher"></a>Indexering för MongoDB Server version 3,6 och högre

Azure Cosmos DB s API för MongoDB Server version 3.6 + indexerar automatiskt `_id` fältet som inte går att släppa. Det tillämpar automatiskt `_id` fältets unikhet per Shard-nyckel. I Azure Cosmos DB s API för MongoDB är horisontell partitionering och indexering separata begrepp. Du behöver inte indexera din Shard-nyckel. Men precis som med andra egenskaper i ditt dokument, rekommenderar vi att du indexerar Shard-nyckeln om den här egenskapen är ett gemensamt filter i dina frågor.

Om du vill indexera fler fält kan du använda MongoDB-kommandona för indexhantering. Precis som i MongoDB indexerar Azure Cosmos DBs-API för MongoDB automatiskt `_id` fältet. Den här indexeringspolicyn skiljer sig från Azure Cosmos DB SQL-API:et som standardmässigt indexerar alla fält.

Om du vill tillämpa en sortering på en fråga måste du skapa ett index för fälten som används i sorterings åtgärden.

### <a name="editing-indexing-policy"></a>Redigera indexerings princip

Vi rekommenderar att du redigerar din indexerings princip i Datautforskaren i Azure Portal.
. Du kan lägga till enstaka fält och jokertecken i index princip redigeraren i Datautforskaren:

:::image type="content" source="./media/mongodb-indexing/indexing-policy-editor.png" alt-text="Indexerings princip redigeraren":::

> [!NOTE]
> Du kan inte skapa sammansatta index med hjälp av index princip redigeraren i Datautforskaren.

## <a name="index-types"></a>Indextyper

### <a name="single-field"></a>Enskilt fält

Du kan skapa index för ett enskilt fält. Sorterings ordningen för det enskilda fält indexet spelar ingen roll. Följande kommando skapar ett index i fältet `name` :

`db.coll.createIndex({name:1})`

Du kan skapa samma fält index `name` i Azure Portal:

:::image type="content" source="./media/mongodb-indexing/add-index.png" alt-text="Lägg till namn index i indexerings princip redigeraren":::

En fråga använder flera enstaka fält index där det är tillgängligt. Du kan skapa upp till 500 enstaka fält index per behållare.

### <a name="compound-indexes-mongodb-server-version-36"></a>Sammansatta index (MongoDB Server version 3.6 +)

Azure Cosmos DBs API för MongoDB stöder sammansatta index för konton som använder sig av kabel protokollet version 3,6 och 4,0. Du kan inkludera upp till åtta fält i ett sammansatt index. Till skillnad från MongoDB bör du endast skapa ett sammansatt index om frågan behöver sortera effektivt på flera fält samtidigt. För frågor med flera filter som inte behöver sorteras, skapar du flera fält index i stället för ett enda sammansatt index. 

> [!NOTE]
> Du kan inte skapa sammansatta index för kapslade egenskaper eller matriser.

Följande kommando skapar ett sammansatt index för fälten `name` och `age` :

`db.coll.createIndex({name:1,age:1})`

Du kan använda sammansatta index för att sortera effektivt på flera fält samtidigt, som du ser i följande exempel:

`db.coll.find().sort({name:1,age:1})`

Du kan också använda föregående sammansatta index för att på ett effektivt sätt sortera efter en fråga med den motsatta sorterings ordningen i alla fält. Här är ett exempel:

`db.coll.find().sort({name:-1,age:-1})`

Sekvensen för Sök vägarna i det sammansatta indexet måste dock exakt matcha frågan. Här är ett exempel på en fråga som kräver ett ytterligare sammansatt index:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Sammansatta index används endast i frågor som sorterar resultat. För frågor som har flera filter som inte behöver sorteras skapar du Multipe enstaka fält index.

### <a name="multikey-indexes"></a>MultiKey-index

Azure Cosmos DB skapar MultiKey-index för index innehåll som lagras i matriser. Om du indexerar ett fält med ett mat ris värde indexeras Azure Cosmos DB automatiskt varje element i matrisen.

### <a name="geospatial-indexes"></a>Geospatiala index

Många geospatiala operatörer kommer att dra fördel av geospatiala index. För närvarande stöder Azure Cosmos DB s API för MongoDB `2dsphere` index. API: t har ännu inte stöd för `2d` index.

Här är ett exempel på hur du skapar ett geospatialt index i `location` fältet:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Text index

Azure Cosmos DBs API för MongoDB stöder för närvarande inte text index. För texts öknings frågor på strängar bör du använda [Azure kognitiv sökning](../search/search-howto-index-cosmosdb.md) integration med Azure Cosmos dB. 

## <a name="wildcard-indexes"></a>Jokertecken index

Du kan använda jokertecken för att stödja frågor mot okända fält. Anta att du har en samling som innehåller data om familjer.

Här är en del av ett exempel dokument i samlingen:

```json
"children": [
   {
     "firstName": "Henriette Thaulow",
     "grade": "5"
   }
]
```

Här är ett annat exempel, den här gången med en något annorlunda uppsättning egenskaper i `children` :

```json
"children": [
    {
     "familyName": "Merriam",
     "givenName": "Jesse",
     "pets": [
         { "givenName": "Goofy" },
         { "givenName": "Shadow" }
         ]
   },
   {
     "familyName": "Merriam",
     "givenName": "John",
   }
]
```

I den här samlingen kan dokument ha många olika möjliga egenskaper. Om du vill indexera alla data i `children` matrisen har du två alternativ: skapa separata index för varje enskild egenskap eller skapa ett Wildcard-index för hela `children` matrisen.

### <a name="create-a-wildcard-index"></a>Skapa ett Wildcard-index

Följande kommando skapar ett Wildcard-index för alla egenskaper i `children` :

`db.coll.createIndex({"children.$**" : 1})`

**Till skillnad från i MongoDB kan jokertecken indexeras ha stöd för flera fält i frågesyntaxen**. Det kommer inte att finnas någon skillnad i frågans prestanda om du använder ett enda index i ett jokertecken i stället för att skapa ett separat index för varje egenskap.

Du kan skapa följande index typer med syntaxen jokertecken:

* Enskilt fält
* Geospatial

### <a name="indexing-all-properties"></a>Indexera alla egenskaper

Så här kan du skapa ett Wildcard-index i alla fält:

`db.coll.createIndex( { "$**" : 1 } )`

Du kan också skapa wildcard-index med hjälp av Datautforskaren i Azure Portal:

:::image type="content" source="./media/mongodb-indexing/add-wildcard-index.png" alt-text="Lägg till index för jokertecken i index princip redigeraren":::

> [!NOTE]
> Om du bara börjar utveckla **rekommenderar vi att du startar** med ett Wildcard-index i alla fält. Detta kan förenkla utvecklingen och göra det enklare att optimera frågor.

Dokument med många fält kan ha en RU-avgift (High Request Unit) för skrivningar och uppdateringar. Om du har en Skriv intensiv arbets belastning bör du därför välja enskilda index Sök vägar i stället för att använda index med jokertecken.

### <a name="limitations"></a>Begränsningar

Jokertecken index stöder inte någon av följande index typer eller egenskaper:

* Sammansättning
* TTL
* Unik

Till **skillnad från i MongoDB**, i Azure Cosmos DB s API för MongoDB, **kan du inte** använda jokertecken index för:

* Skapa ett jokerteckenindex som inkluderar flera specifika fält

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection " :
          {
             "children.givenName" : 1,
             "children.grade" : 1
          }
      }
  )
  ```

* Skapa ett jokerteckenindex som exkluderar flera specifika fält

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection" :
          {
             "children.givenName" : 0,
             "children.grade" : 0
          }
      }
  )
  ```

Alternativt kan du skapa flera jokerteckenindex.

## <a name="index-properties"></a>Index egenskaper

Följande åtgärder är vanliga för konton som hanterar Wire Protocol version 4,0 och konton som hanterar tidigare versioner. Du kan läsa mer om [index som stöds och indexerade egenskaper](mongodb-feature-support-40.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Unika index

[Unika index](unique-keys.md) är användbara för att tvinga fram att två eller flera dokument inte innehåller samma värde för indexerade fält.

> [!IMPORTANT]
> Det går bara att skapa unika index när samlingen är tom (innehåller inga dokument).

Följande kommando skapar ett unikt index i fältet `student_id` :

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

För shardade-samlingar måste du ange Shard-nyckeln (partition) för att skapa ett unikt index. Med andra ord så utgör alla unika index i en fragmenterad samling alltså sammansatta index där ett av fälten är en partitionsnyckel.

Följande kommandon skapar en shardade-samling ```coll``` (Shard-nyckeln är ```university``` ) med ett unikt index för fälten `student_id` och `university` :

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 3,
    "numIndexesAfter" : 4
}
```

I föregående exempel ```"university":1``` returnerar-satsen ett fel med följande meddelande:

*Det går inte att skapa ett unikt index över {student_id: 1,0} med Shard-nyckel mönster {University: 1,0}*

### <a name="ttl-indexes"></a>TTL-index

Om du vill aktivera dokumentets förfallo datum i en viss samling måste du skapa ett [TTL-index (Time-to-Live)](../cosmos-db/time-to-live.md). Ett TTL-index är ett index i `_ts` fältet med ett `expireAfterSeconds` värde.

Exempel:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Föregående kommando tar bort alla dokument i ```db.coll``` samlingen som inte har ändrats under de senaste 10 sekunderna.

> [!NOTE]
> **_Tss** fältet är särskilt för Azure Cosmos DB och kan inte nås från MongoDB-klienter. Det är en reserverad (system) egenskap som innehåller tidsstämpeln för dokumentets senaste ändring.

## <a name="track-index-progress"></a>Spåra index förlopp

Version 3.6 + av Azure Cosmos DB s API för MongoDB stöder `currentOp()` kommandot för att spåra indexerings förloppet på en databas instans. Det här kommandot returnerar ett dokument som innehåller information om pågående åtgärder på en databas instans. Du kan använda `currentOp` kommandot för att spåra alla pågående åtgärder i interna MongoDB. I Azure Cosmos DB s API för MongoDB stöder det här kommandot endast spårning av index åtgärden.

Här följer några exempel som visar hur du använder `currentOp` kommandot för att spåra indexerings förlopp:

* Hämta index förloppet för en samling:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Hämta index förloppet för alla samlingar i en databas:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Hämta index förloppet för alla databaser och samlingar i ett Azure Cosmos-konto:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Exempel på utdata för index förlopp

Informationen om indexets förlopp visar procent andelen förloppet för den aktuella index åtgärden. Här är ett exempel som visar utdatafilens format för olika steg i index förloppet:

* En index åtgärd för en "foo"-samling och "stapel"-databasen som är 60 procent slutförd kommer att ha följande utdata-dokument. I `Inprog[0].progress.total` fältet visas 100 som målets slut för ande procent.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

* Om en index åtgärd precis har startats i en "foo"-samling och "stapel"-databasen kan utmatnings dokumentet Visa 0 procents förlopp tills det når en mätbar nivå.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

* När den pågående index åtgärden har slutförts, visar utmatnings dokumentet tomma `inprog` åtgärder.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Uppdateringar av bakgrunds index

Oavsett vilket värde som angetts för egenskapen för **bakgrunds** index görs alltid index uppdateringar i bakgrunden. Eftersom index uppdateringar använder ru: er-enheter () med lägre prioritet än andra databas åtgärder, resulterar index ändringar inte i någon nedtid för skrivningar, uppdateringar eller borttagningar.

Det finns ingen inverkan på Läs tillgänglighet när du lägger till ett nytt index. Frågor använder bara nya index när index omvandlingen har slutförts. Vid omvandlingen av index fortsätter Frågeredigeraren att använda befintliga index, så du ser liknande Läs prestanda under indexerings omvandlingen till det du hade observerat innan du påbörjar indexerings ändringen. När du lägger till nya index är det inte heller någon risk för ofullständiga eller inkonsekventa frågeresultat.

När du tar bort index och omedelbart kör frågor, så kan resultaten vara inkonsekventa och ofullständiga tills index omvandlingen har slutförts. Om du tar bort index ger inte frågespråket några konsekventa eller fullständiga resultat när frågor filtrerar på dessa nyligen borttagna index. De flesta utvecklare släpper inte indexen och försöker sedan omedelbart fråga dem så, i praktiken är den här situationen osannolik.

> [!NOTE]
> Du kan [spåra index förloppet](#track-index-progress).

## <a name="reindex-command"></a>Kommandot indexera om

`reIndex`Kommandot kommer att återskapa alla index i en samling. I de flesta fall är detta onödigt onödigt. Men i vissa sällsynta fall kan frågor om prestanda förbättras efter körning av `reIndex` kommandot.

Du kan köra `reIndex` kommandot med hjälp av följande syntax:

`db.runCommand({ reIndex: <collection> })`

Du kan använda nedanstående syntax för att kontrol lera om du behöver köra `reIndex` kommandot:

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

Exempel på utdata:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

Om `reIndex` är nödvändigt kommer **requiresReIndex** att vara sant. Om `reIndex` detta inte är nödvändigt utelämnas den här egenskapen.

## <a name="migrate-collections-with-indexes"></a>Migrera samlingar med index

För närvarande kan du bara skapa unika index när samlingen inte innehåller några dokument. Populära Migreringsverktyg för MongoDB försöker skapa unika index när du har importerat data. För att kringgå det här problemet kan du skapa motsvarande samlingar och unika index manuellt i stället för att tillåta att migrations verktyget försöker. (Du kan få det här beteendet ```mongorestore``` genom att använda- `--noIndexRestore` flaggan på kommando raden.)

## <a name="indexing-for-mongodb-version-32"></a>Indexering för MongoDB-version 3,2

Tillgängliga indexerings funktioner och standardvärden skiljer sig åt för Azure Cosmos-konton som är kompatibla med version 3,2 av MongoDB-Wire-protokollet. Du kan [kontrol lera kontots version](mongodb-feature-support-36.md#protocol-support) och [uppgradera till version 3,6](mongodb-version-upgrade.md).

Om du använder version 3,2 beskriver det här avsnittet viktiga skillnader med version 3.6 +.

### <a name="dropping-default-indexes-version-32"></a>Släpper standard index (version 3,2)

Till skillnad från 3,6 +-versionerna av Azure Cosmos DBs API för MongoDB, är version 3,2 index för varje egenskap som standard. Du kan använda följande kommando för att ta bort dessa standard index för en samling ( ```coll``` ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

När du har släppt standard indexen kan du lägga till fler index som du skulle göra i version 3.6 +.

### <a name="compound-indexes-version-32"></a>Sammansatta index (version 3,2)

Sammansatta index innehåller referenser till flera fält i ett dokument. Om du vill skapa ett sammansatt index [uppgraderar du till version 3,6 eller 4,0](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Jokertecken index (version 3,2)

Om du vill skapa ett Wildcard-index [uppgraderar du till version 4,0 eller 3,6](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Nästa steg

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
* Information om relationen mellan partitionering och indexering finns i så här frågar du [en Azure Cosmos container](how-to-query-container.md) -artikel.
