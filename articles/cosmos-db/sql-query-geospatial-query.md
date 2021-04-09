---
title: Fråga geospatiala data med Azure Cosmos DB
description: Fråga om spatialdata med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336137"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Fråga geospatiala data med Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du frågar geospatiala data i Azure Cosmos DB att använda SQL och LINQ. För närvarande stöds endast att lagra och komma åt geospatiala data i Azure Cosmos DB SQL API-konton. Azure Cosmos DB stöder följande Open Geospatial Consortium (OGC) inbyggda funktioner för geospatiala frågor. Mer information om den fullständiga uppsättningen inbyggda funktioner i SQL-språket finns [i fråga system funktioner i Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Inbyggda SQL-funktioner för avstånd

Här är en lista över geospatiala systemfunktioner som är användbara för frågor i Azure Cosmos DB:

|**Användning**|**Beskrivning**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken.|
|ST_WITHIN (spatial_expr, spatial_expr) | Returnerar ett booleskt uttryck som anger huruvida det första GeoJSON-objektet (Point, Polygon eller LineString) finns i det andra GeoJSON-objektet (Point, Polygon eller LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Returnerar ett booleskt uttryck som anger huruvida de två angivna GeoJSON-objekten (Point, Polygon eller LineString) överlappar varandra.|
|ST_ISVALID| Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.|
| ST_ISVALIDDETAILED| Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna lin Est ring-uttrycket är giltigt. Om detta är ogiltigt returneras orsaken som ett sträng värde.|

Spatiella funktioner kan användas för att köra närhetsfrågor mot rumsliga data. Här är ett exempel på en fråga som returnerar alla familje dokument som ligger inom 30 km från den angivna platsen med hjälp av den `ST_DISTANCE` inbyggda funktionen.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Resultat**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Om du inkluderar rums indexering i din indexerings princip kommer "avstånds frågor" att hanteras effektivt genom indexet. Mer information om spatial indexering finns i [Geospatial indexering](sql-query-geospatial-index.md). Om du inte har ett rums index för de angivna Sök vägarna gör frågan en genomsökning av behållaren.

`ST_WITHIN` kan användas för att kontrol lera om en punkt ligger inom en polygon. Vanliga polygoner används för att representera gränser som post nummer, tillstånds gränser eller naturliga formulär. Återigen om du inkluderar rums indexering i din indexerings princip, kommer frågor i frågor att hanteras effektivt genom indexet.

Polygon-argument i `ST_WITHIN` får bara innehålla en enda ring, det vill säga polygonerna får inte innehålla hål i dem.

**Query**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultat**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> På samma sätt som för fel matchnings typer fungerar i Azure Cosmos DBs fråga, om det plats värde som anges i båda argumenten är felaktigt eller ogiltigt, utvärderas det som **odefinierat** och det utvärderade dokumentet som ska hoppas över från frågeresultaten. Om frågan inte returnerar några resultat kan `ST_ISVALIDDETAILED` du köra för att felsöka varför den spatiala typen är ogiltig.
>
>

Azure Cosmos DB också stöd för att utföra inverterade frågor, det vill säga du kan indexera polygoner eller rader i Azure Cosmos DB och sedan fråga efter de områden som innehåller en angiven punkt. Det här mönstret används ofta i logistik för att identifiera, till exempel när en Last bil ansluts eller lämnar ett visst utrymme.

**Query**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Resultat**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` och `ST_ISVALIDDETAILED` kan användas för att kontrol lera om ett spatial objekt är giltigt. Följande fråga kontrollerar till exempel giltigheten för en punkt med ett out-of-Range-värde för Latitude (-132,8). `ST_ISVALID` returnerar bara ett booleskt värde och `ST_ISVALIDDETAILED` returnerar det booleska värdet och en sträng som innehåller orsaken till att den anses vara ogiltig.

**Query**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultat**

```json
    [{
      "$1": false
    }]
```

Dessa funktioner kan också användas för att validera polygoner. Här använder vi `ST_ISVALIDDETAILED` till exempel för att validera en polygon som inte är stängd.

**Query**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Resultat**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-frågor i .NET SDK

SQL .NET SDK tillhandahåller även stub-metoder `Distance()` och `Within()` för användning i LINQ-uttryck. SQL LINQ-providern översätter den här metoden anrop till motsvarande SQL-inbyggda funktions anrop (ST_DISTANCE respektive ST_WITHIN).

Här är ett exempel på en LINQ-fråga som hittar alla dokument i Azure Cosmos-behållaren vars `location` värde är inom en radie på 30 km från den angivna punkten med LINQ.

**LINQ-fråga för avstånd**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

På samma sätt är det här en fråga för att hitta alla dokument vars `location` befinner sig inom den angivna rutan/polygonen.

**LINQ-fråga för inom**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du göra följande:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Läs mer om [geospatiala data om geospatiala och geospatiala JSON-platser i Azure Cosmos DB](sql-query-geospatial-intro.md)
* Läs mer om [index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)
