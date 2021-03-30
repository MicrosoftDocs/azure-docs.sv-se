---
title: Fråga data med Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du frågar efter data från Azure Cosmos DBs API för MongoDB med hjälp av MongoDB Shell-kommandon
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074326"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Fråga data med Azure Cosmos DB:s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB-API:et för MongoDB](mongodb-introduction.md) stöder [MongoDB-frågor](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Köra frågor mot data som lagras i din Cosmos-databas med MongoDB-gränssnittet

Du kan sätta igång med hjälp av exemplen i det här dokumentet och titta på videon [Query Azure Cosmos DB with MongoDB shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) (Hämta data från Azure Cosmos DB med MongoDB-gränssnittet).

## <a name="sample-document"></a>Exempeldokument

Frågorna i den här artikeln använder följande exempeldokument.

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
        "gender": "female", "grade": 1,
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
## <a name="example-query-1"></a><a id="examplequery1"></a> Exempelfråga 1 

Med exemplet på familjedokumentet ovan returnerar följande fråga dokument där ID-fältet matchar `WakefieldFamily`.

**Query**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Resultat**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
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
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Exempelfråga 2 

Nästa fråga returnerar alla underordnade i familjen. 

**Query**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Resultat**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
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
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>Exempelfråga 3 

Nästa fråga returnerar alla familjer som är registrerade. 

**Query**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Resultat**

Inget dokument kommer att returneras. 

## <a name="example-query-4"></a><a id="examplequery4"></a>Exempelfråga 4

Nästa fråga returnerar alla familjer som inte är registrerade. 

**Query**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Resultat**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>Exempelfråga 5

Nästa fråga returnerar alla familjer med statusen NY som inte är registrerade. 

**Query**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Resultat**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>Exempelfråga 6

Nästa fråga returnerar alla familjer där värdet för underordnade klasser är 8.

**Query**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Resultat**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>Exempelfråga 7

Nästa fråga returnerar alla familjer där den underordnade matrisens storlek är 3.

**Query**

```bash
db.Family.find( {children: { $size:3} } )
```

**Resultat**

Inga resultat returneras eftersom det inte finns några familjer med fler än två underordnade. Endast när parametern är 2 kommer frågan att lyckas och returnera det fullständiga dokumentet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågar med Cosmos DB-API:et för MongoDB

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-sql-api.md)

