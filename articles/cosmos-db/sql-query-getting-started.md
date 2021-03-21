---
title: Komma igång med SQL-frågor i Azure Cosmos DB
description: Lär dig hur du använder SQL-frågor för att fråga efter data från Azure Cosmos DB. Du kan ladda upp exempel data till en behållare i Azure Cosmos DB och fråga den.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: d5d5bc0a108cd08283ea29ce3bdc2de49310c5aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499568"
---
# <a name="getting-started-with-sql-queries"></a>Komma igång med SQL-frågor
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I Azure Cosmos DB SQL API-konton finns det två sätt att läsa data:

**Punkt läsningar** – du kan göra en nyckel/värde-sökning på ett enskilt *objekt-ID* och en partitionsnyckel. Kombinationen av *objekt-ID* och partitionsnyckel är nyckeln och själva objektet är värdet. För ett dokument med 1 KB läser vi vanligt vis kostnad 1 [begär ande enhet](request-units.md) med en svars tid under 10 MS. Punkt läsningar returnerar ett enskilt objekt.

Här följer några exempel på hur du gör **punkt läsningar** med varje SDK:

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**SQL-frågor** – du kan fråga efter data genom att skriva frågor med hjälp av STRUCTURED Query Language (SQL) som ett JSON-frågespråk. Frågor kostar alltid att vara minst 2,3 enheter för programbegäran och i allmänhet har de en högre och mer varierande svars tid än punkt läsningar. Frågor kan returnera många objekt.

De flesta Läs tunga arbets belastningar på Azure Cosmos DB använda en kombination av både punkt läsningar och SQL-frågor. Om du bara behöver läsa ett enskilt objekt är punkt läsningarna billigare och snabbare än frågor. Punkt läsningar behöver inte använda frågemotor för att få åtkomst till data och kan läsa data direkt. Naturligtvis är det inte möjligt för alla arbets belastningar att enbart läsa data med hjälp av punkt läsningar, så stöd för SQL som frågespråk och [schema-oberoende indexering](index-overview.md) ger ett mer flexibelt sätt att komma åt dina data.

Här följer några exempel på hur du gör **SQL-frågor** med varje SDK:

- [.NET SDK](./sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](./sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](./sql-api-nodejs-samples.md#item-examples)
- [Python SDK](./sql-api-python-samples.md#item-examples)

Resten av det här dokumentet visar hur du kommer igång med att skriva SQL-frågor i Azure Cosmos DB. SQL-frågor kan köras via antingen SDK eller Azure Portal.

## <a name="upload-sample-data"></a>Ladda upp exempel data

I ditt SQL API Cosmos DB-konto öppnar du [datautforskaren](./data-explorer.md) för att skapa en behållare med namnet `Families` . När den har skapats använder du data struktur listan för att hitta och öppna den. I din `Families` behållare visas `Items` alternativet direkt under namnet på behållaren. Öppna det här alternativet så visas en knapp i meny raden i mitten av skärmen för att skapa ett nytt objekt. Du kommer att använda den här funktionen för att skapa JSON-objekten nedan.

### <a name="create-json-items"></a>Skapa JSON-objekt

Följande 2 JSON-objekt är dokument om Andersen-och Wakefield-familjer. De omfattar föräldrar, barn och deras hus djur, adress och registrerings information. 

Det första objektet har strängar, siffror, booleska värden, matriser och kapslade egenskaper:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Det andra objektet använder `givenName` och `familyName` i stället för `firstName` och `lastName` :

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Fråga JSON-objekten

Prova några frågor mot JSON-data för att förstå några av de viktigaste aspekterna i Azure Cosmos DB SQL-frågespråket.

Följande fråga returnerar de objekt där `id` fältet matchar `AndersenFamily` . Eftersom det är en `SELECT *` fråga är resultatet av frågan det fullständiga JSON-objektet. Mer information om SELECT-syntax finns i [Select Statement](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Frågeresultatet är:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Följande fråga formaterar om JSON-utdata till en annan form. Frågan Projects ett nytt JSON `Family` -objekt med två markerade fält, `Name` och `City` när adressens ort är samma som status. "NY, NY" matchar det här ärendet.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Frågeresultatet är:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Följande fråga returnerar alla namn på underordnade objekt i den familj vars `id` matchningar `WakefieldFamily` beställs efter ort.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Resultatet är:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Kommentarer

I föregående exempel visas flera aspekter av Cosmos DB frågespråket:  

* Eftersom SQL API fungerar med JSON-värden, hanterar den Tree-formade entiteter i stället för rader och kolumner. Du kan referera till trädnoden i godtyckligt djup, som `Node1.Node2.Node3…..Nodem` liknar referensen för två delar av `<table>.<column>` i ANSI SQL.

* Eftersom frågespråket fungerar med schemabaserade data, måste typ systemet bindas dynamiskt. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde, men det är inte säkert att det är ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Typsystem och uttryck är begränsade till att endast hantera JSON-typer. Mer information finns i JSON- [specifikationen](https://www.json.org/).  

* En Cosmos-behållare är en schema fri samling med JSON-objekt. Relationerna i och över behållar objekt samlas in implicit genom inne slutning, inte av primär nyckel och sekundär nyckel relationer. Den här funktionen är viktig för kopplingar inom objekt som beskrivs i [kopplingar i Azure Cosmos DB](sql-query-join.md).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-sats](sql-query-select.md)
