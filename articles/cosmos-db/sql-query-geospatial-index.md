---
title: Indexera geospatiala data med Azure Cosmos DB
description: Indexera spatialdata med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336222"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexera geospatiala data med Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vi utformade Azure Cosmos DB databas motorn så att den verkligen är schema-oberoende och ger första klass stöd för JSON. Den Write-optimerade databas motorn i Azure Cosmos DB internt förstår spatialdata som representeras i standarden för polyjson.

I en kortfattat så Jenkins projiceras geometrin från Geodetic-koordinater till ett 2D-plan och delas sedan progressivt i cellerna med hjälp av en **quadtree**. Dessa celler mappas till 1D baserat på cellens position inom en **Fyllnings kurva för Hilbert utrymme**, som bevarar platsens plats. När plats data indexeras går de igenom en process som kallas **mosaik**, det vill säga att alla celler som korsar en plats identifieras och lagras som nycklar i Azure Cosmos DB indexet. Vid fråge tillfället är argument som punkter och polygoner också tessellated för att extrahera relevanta cell-ID-intervall och sedan använda för att hämta data från indexet.

Om du anger en indexerings princip som innehåller ett rums index för `/*` (alla sökvägar) indexeras alla data som finns i behållaren för effektiva rums frågor.

> [!NOTE]
> Azure Cosmos DB stöder indexering av punkter, lin Est rings, polygoner och multipolygoner. Om du har indexerat någon av dessa typer, kommer vi automatiskt att indexera alla andra typer. Med andra ord, om du indexerar polygoner, kommer vi också att indexera punkter, lin Est rings och multipolygoner. Indexering av en ny spatial typ påverkar inte avgiften för Skriv in-eller index storlek om du inte har giltiga geospatiala JSON-data av den typen.

## <a name="modifying-geospatial-configuration"></a>Ändrar Geospatial konfiguration

I din behållare anger den **geospatiala konfigurationen** hur spatial data ska indexeras. Ange en **Geospatial konfiguration** per container: geografi eller geometri.

Du kan växla mellan **geografi** -och **geometrins** spatiala typ i Azure Portal. Det är viktigt att du skapar en [giltig princip för avstånds index med en avgränsnings ruta](#geometry-data-indexing-examples) innan du växlar till en geometri spatial typ.

Så här ställer du in den **geospatiala konfigurationen** i **datautforskaren** i Azure Portal:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Ställer in Geospatial konfiguration":::

Du kan också ändra `geospatialConfig` i .NET SDK för att justera den **geospatiala konfigurationen**:

Om inget `geospatialConfig` värde anges används geografi data typen som standard. När du ändrar `geospatialConfig` , kommer alla befintliga geospatiala data i behållaren att indexeras om.

Här är ett exempel på hur du ändrar den geospatiala data typen till `geometry` genom att ange `geospatialConfig` egenskapen och lägga till en **boundingBox**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Exempel på geografi data indexering

Följande JSON-kodfragment visar en indexerings princip där spatial indexering har Aktiver ATS för **geografi** data typen. Den är giltig för spatialdata med data typen geografi och kommer att indexera alla typer av JSON-punkter, polygoner, multipolygoner eller lin Est ring som finns i dokument för spatial fråga. Om du ändrar indexerings principen med hjälp av Azure Portal kan du ange följande JSON för indexerings principen för att aktivera rums indexering i din behållare:

**Behållar indexerings princip JSON med geografisk rums indexering**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Om platsens värde för JSON-värdet i dokumentet är felaktigt eller ogiltigt, kommer det inte att indexeras för spatial fråga. Du kan verifiera plats värden med ST_ISVALID och ST_ISVALIDDETAILED.

Du kan också [ändra indexerings principen](how-to-manage-indexing-policy.md) med hjälp av Azure CLI, POWERSHELL eller SDK.

## <a name="geometry-data-indexing-examples"></a>Exempel på geometri data indexering

Med data typen **Geometry** , som liknar data typen geografi, måste du ange relevanta sökvägar och typer att indexera. Dessutom måste du också ange en `boundingBox` i indexerings principen för att ange det önskade områden som ska indexeras för den angivna sökvägen. Varje Geospatial sökväg kräver en egen `boundingBox` .

Avgränsnings rutan består av följande egenskaper:

- **xMin**: den minsta indexerade x-koordinaten
- **yMin**: den minsta indexerade y-koordinaten
- **Xmax**: den maximalt indexerade x-koordinaten
- **yMax**: den maximalt indexerade y-koordinaten

En avgränsnings ruta krävs eftersom geometriska data upptar ett plan som kan vara oändligt. Rums index kräver dock ett begränsat utrymme. För **geografi** data typen är jordens kant linjen och du behöver inte ange någon avgränsnings ruta.

Skapa en avgränsnings ruta som innehåller alla (eller de flesta) data. Endast åtgärder som beräknas för de objekt som är helt inne i avgränsnings rutan kommer att kunna använda rums indexet. Att göra den begränsade avgränsnings rutan större än nödvändigt påverkar frågans prestanda negativt.

Här är ett exempel på en indexerings princip som indexerar **geometri** data med **geospatialConfig** inställt på `geometry` :

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
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Index principen ovan har en **boundingBox** av (-10, 10) för x-koordinater och (-20, 20) för y-koordinater. Behållaren med index principen ovan kommer att indexera alla punkter, polygoner, multipolygoner och lin Est rings som är helt inom den här regionen.

> [!NOTE]
> Om du försöker lägga till en indexerings princip med en **boundingBox** till en behållare med `geography` data typen, kommer den att Miss Förslut. Du bör ändra behållarens **geospatialConfig** `geometry` innan du lägger till en **boundingBox**. Du kan lägga till data och ändra resten av din indexerings princip (t. ex. sökvägar och typer) antingen före eller efter att du väljer den geospatiala data typen för behållaren.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du göra följande:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Lär dig mer om att [fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [geospatiala data om geospatiala och geospatiala JSON-platser i Azure Cosmos DB](sql-query-geospatial-intro.md)
