---
title: Geospatiala och geospatiala JSON-plats data i Azure Cosmos DB
description: Lär dig hur du skapar spatiala objekt med Azure Cosmos DB och SQL API.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692138"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Geospatiala och geospatiala JSON-plats data i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln är en introduktion till geospatiala funktioner i Azure Cosmos DB. När du har läst vår dokumentation om Geospatial indexering kan du svara på följande frågor:

* Hur gör jag för att lagra spatialdata i Azure Cosmos DB?
* Hur kan jag fråga efter spatialdata i Azure Cosmos DB i SQL och LINQ?
* Hur gör jag för att aktivera eller inaktivera spatial indexering i Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Användnings fall för spatialdata

Geospatiala data innefattar ofta närhets frågor, till exempel "hitta alla Café butiker nära min nuvarande plats". Vanliga användnings fall är:

* Analys av geolokalisering, med en detaljerad marknads initiativ.
* Plats baserad anpassning, för flera branscher som detalj handel och sjukvård.
* Logistik förbättring, för transport optimering.
* Riskanalys, särskilt för försäkrings-och finans företag.
* Situations medvetenhet, för aviseringar och meddelanden.

## <a name="introduction-to-spatial-data"></a>Introduktion till spatialdata

Spatialdata beskriver position och form för objekt i utrymme. I de flesta program motsvarar dessa objekt på jorden och geospatiala data. Spatialdata kan användas för att representera en persons plats, en intressant plats eller en stads gränser.

Azure Cosmos DB SQL API har stöd för två spatiala data typer: data typen **Geometry** och **geografi** data typen.

- **Geometri** typen representerar data i ett Euclidean (flat)-koordinatsystem
- **Geografi** typen representerar data i ett system för Round-jordens koordinater.

## <a name="supported-data-types"></a>Datatyper som stöds

Azure Cosmos DB stöder indexering och frågor om geospatiala punkt data som representeras med hjälp av den geospatiala [JSON-specifikationen](https://tools.ietf.org/html/rfc7946). Data strukturer för interjson är alltid giltiga JSON-objekt, så de kan lagras och frågas med Azure Cosmos DB utan specialiserade verktyg eller bibliotek.

Azure Cosmos DB stöder följande spatiala data typer:

- Pekaren
- Lin Est ring
- Polygonlasso
- MultiPolygon

### <a name="points"></a>Punkter

En **punkt** anger en enda position i rymden. I geospatiala data representerar en punkt den exakta platsen, som kan vara en gatuadress till ett livsmedels lager, en hel skärm, en bil eller en stad.  En punkt representeras i polyjson (och Azure Cosmos DB) med dess koordinatnummer eller longitud och latitud.

Här är ett exempel-JSON för en punkt:

**Punkter i Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Spatiala data typer kan bäddas in i ett Azure Cosmos DB-dokument som visas i det här exemplet på en användar profil som innehåller plats data:

**Använd profil med platsen lagrad i Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punkter i ett geometri koordinat system

För data typen **Geometry** anger netjson-specifikationen den vågräta axeln först och den lodräta axeln.

### <a name="points-in-a-geography-coordinate-system"></a>Punkter i ett geografiskt koordinatsystem

För geografi data typen anger **geografi** specifikation longitud först och Latitude Second. Precis som i andra mappnings program, longitud och Latitude är vinklar och visas i grader. Longitud-värden mäts från den primära Meri Dian och är mellan-180 grader och 180,0 grader, och Latitude-värden mäts från den likställare och är mellan-90,0-grader och 90,0 grader.

Azure Cosmos DB tolkar koordinaterna enligt vad som visas i referens systemet WGS-84. Se nedan för mer information om referens system för koordinater.

### <a name="linestrings"></a>Lin Est rings

**Lin Est rings** representerar en serie med två eller flera punkter i utrymmet och de linje segment som kopplar dem. I geospatiala data används lin Est rings ofta för att representera motorväg eller floder.

**Lin Est rings i interjson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygoner

En **polygon** är en kant linje med anslutna punkter som utgör en stängd lin Est ring. Polygoner används ofta för att representera naturliga formulär som sjöar eller politiska jurisdiktioner som städer och stater. Här är ett exempel på en polygon i Azure Cosmos DB:

**Polygoner i polyjson**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Den interjson-specifikationen kräver att för giltiga polygoner, ska det sista koordinat paret vara detsamma som det första, för att skapa en stängd form.
>
> Punkter inom en polygon måste anges i en motsols ordning. En polygon som anges i medsols ordning representerar inversen för regionen i den.
>
>

### <a name="multipolygons"></a>Multipolygoner

En **multipolygon** är en matris med noll eller flera polygoner. **Multipolygoner** får inte överlappa sidor eller ha några gemensamma områden. De kan röra sig vid en eller flera punkter.

**Multipolygoner i polyjson**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Koordinera referens system

Eftersom jordens form är oregelbunden, representeras koordinaterna för geospatiala geografiska data i många koordinatsystem, var och en med sina egna referens ramar och mått enheter. Till exempel är "National Grid of Storbritannien" ett referens system som är korrekt för Förenade kungariket, men inte utanför det.

Det mest populära BOKNINGs systemet som används idag är världens Geodetic System  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS-enheter och många mappnings tjänster, inklusive Google Maps och Bing Maps-API: er använder WGS-84. Azure Cosmos DB stöder indexering och frågor om geospatiala geografi data med endast WGS-84-boknings system.

## <a name="creating-documents-with-spatial-data"></a>Skapa dokument med spatialdata
När du skapar dokument som innehåller geospatiala JSON-värden, indexeras de automatiskt med ett rums index i enlighet med behållarens indexerings princip. Om du arbetar med ett Azure Cosmos DB SDK på ett dynamiskt inskrivet språk som python eller Node.js måste du skapa en giltig interjson.

**Skapa dokument med geospatiala data i Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Om du arbetar med SQL-API: erna kan du använda `Point` `LineString` `Polygon` klasserna,, och `MultiPolygon` i `Microsoft.Azure.Cosmos.Spatial` namn området för att bädda in plats information i dina program objekt. Dessa klasser bidrar till att förenkla serialiseringen och deserialiseringen av spatialdata till en interjson.

**Skapa dokument med geospatiala data i .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Om du inte har information om latitud och longitud, men har fysiska adresser eller plats namn som stad eller land/region, kan du leta upp de faktiska koordinaterna genom att använda en tjänst som Bing Maps REST Services. Lär dig mer om Bing Maps-geografi [här](/bingmaps/rest-services/).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du göra följande:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Lär dig mer om att [fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)