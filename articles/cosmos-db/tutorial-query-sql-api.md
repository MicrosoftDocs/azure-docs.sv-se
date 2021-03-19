---
title: 'Självstudie: så här frågar du med SQL i Azure Cosmos DB?'
description: 'Självstudie: Lär dig hur du frågar med SQL-frågor i Azure Cosmos DB att använda frågan Playground'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: c206b68495b09988e49f37433ccd5616acd29cb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337786"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Självstudie: Fråga Azure Cosmos DB med hjälp av SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB [SQL API](./introduction.md) stöder frågedokument med hjälp av SQL. Den här artikeln innehåller ett dokumentexempel och två exempel på SQL-frågor och resultat.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Fråga efter data med SQL

## <a name="sample-document"></a>Exempeldokument

SQL-frågorna i artikeln använder följande exempeldokument.

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

## <a name="where-can-i-run-sql-queries"></a>Var kan jag köra SQL-frågor?

Du kan köra frågor med Datautforskaren i Azure-portalen via [REST-API och SDK](sql-api-sdk-dotnet.md), och även [Query Playground](https://www.documentdb.com/sql/demo) som kör frågor på en befintlig uppsättning exempeldata.

Mer information om SQL-frågor finns i:
* [SQL-fråga och SQL-syntax](sql-query-getting-started.md)

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du har ett konto för Azure Cosmos DB och en samling. Har du inga av dessa resurser? Slutför [Snabbstart på 5 minuter](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Exempelfråga 1

Med exempel seriens dokument ovan returnerar följande SQL-fråga dokument där ID-fältet matchar `WakefieldFamily` . Eftersom det är en `SELECT *`-instruktion är utdatan från frågan ett komplett JSON-dokument:

**Query**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Resultat**

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

## <a name="example-query-2"></a>Exempelfråga 2

Nästa fråga returnerar alla namn på underordnade objekt i den familj vars ID matchar `WakefieldFamily` .

**Query**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Resultat**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågar med SQL  

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-sql-api.md)