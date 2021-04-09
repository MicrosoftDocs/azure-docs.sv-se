---
title: MongoDB-tilläggs kommandon för att hantera data i Azure Cosmos DB s API för MongoDB
description: Den här artikeln beskriver hur du använder MongoDB-tilläggs kommandon för att hantera data som lagras i Azure Cosmos DB s API för MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: deba6696eb71287902fa3970ed2d83d0b09ac08d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658494"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB-tilläggs kommandon för att hantera data som lagras i Azure Cosmos DB s API för MongoDB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Följande dokument innehåller de anpassade åtgärds kommandon som är speciella för Azure Cosmos DB s API för MongoDB. Dessa kommandon kan användas för att skapa och hämta databas resurser som är speciella för [Azure Cosmos DB kapacitets modellen](account-databases-containers-items.md).

Genom att använda Azure Cosmos DBs API för MongoDB kan du dra nytta av fördelarna Cosmos DB till exempel global distribution, automatisk horisontell partitionering, hög tillgänglighet, fördröjnings garantier, automatisk, kryptering i vila, säkerhets kopiering och många fler, samtidigt som du behåller dina investeringar i din MongoDB-app. Du kan kommunicera med Azure Cosmos DBens API för MongoDB genom att använda någon av [MongoDB-klient driv rutinerna](https://docs.mongodb.org/ecosystem/drivers)med öppen källkod. Azure Cosmos DBens API för MongoDB gör det möjligt att använda befintliga klient driv rutiner genom att följa [MongoDB-Wire-protokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Stöd för MongoDB-protokoll

Azure Cosmos DBs API för MongoDB är kompatibelt med MongoDB Server version 4,0, 3,6 och 3,2. Mer information finns i avsnittet om funktioner och syntax som stöds i [4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)och [3,2](mongodb-feature-support.md) artiklar. 

Följande tilläggs kommandon ger möjlighet att skapa och ändra Azure Cosmos DB-/regionsspecifika resurser via databas begär Anden:

* [Skapa databas](#create-database)
* [Uppdatera databas](#update-database)
* [Hämta databas](#get-database)
* [Skapa samling](#create-collection)
* [Uppdatera samling](#update-collection)
* [Hämta samling](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Skapa databas

Kommandot Skapa databas tillägg skapar en ny MongoDB-databas. Databas namnet kan användas från den databas kontext som anges av `use database` kommandot. I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Namnet på det anpassade kommandot, det måste vara "CreateDatabase".      |
| `offerThroughput` | `int`  | Tillhandahållet data flöde som du har angett för databasen. Den här parametern är valfri. |
| `autoScaleSettings` | `Object` | Krävs för [autoskalning-läge](provision-throughput-autoscale.md). Det här objektet innehåller inställningarna som är associerade med läget för autoskalning-kapacitet. Du kan ställa in `maxThroughput` värdet, som beskriver det högsta antalet enheter för programbegäran som samlingen ökar till dynamiskt. |

### <a name="output"></a>Utdata

Om kommandot lyckas returneras följande svar:

```javascript
{ "ok" : 1 }
```

Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="create-a-database"></a>Skapa en databas

Använd följande kommando för att skapa en databas med namnet `"test"` som använder alla standardvärden:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Med det här kommandot skapas en databas utan data flöde på databas nivå. Det innebär att samlingarna i den här databasen måste ange den mängd data flöde som du behöver använda.

#### <a name="create-a-database-with-throughput"></a>Skapa en databas med data flöde

Använd följande kommando för att skapa en databas med namnet `"test"` och ange ett allokerat data flöde på [databas nivå](set-throughput.md#set-throughput-on-a-database) på 1000 ru: er:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Detta skapar en databas och anger ett data flöde. Alla samlingar i den här databasen kommer att dela det angivna data flödet, om inte samlingarna skapas med [en angiven data flödes nivå](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Skapa en databas med autoskalning av data flöde

Använd följande kommando för att skapa en databas med namnet `"test"` och för att ange ett maximalt data flöde för autoskalning på 20 000 ru/s på [databas nivå](set-throughput.md#set-throughput-on-a-database):

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Uppdatera databas

Kommandot uppdatera databas tillägg uppdaterar de egenskaper som är associerade med den angivna databasen. Det går bara att ändra databasen från etablerade data flöden till autoskalning och vice versa i Azure-portalen. I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Namnet på det anpassade kommandot. Måste vara "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nytt tillhandahållet data flöde som du vill ange i databasen om databasen använder [data flöde på databas nivå](set-throughput.md#set-throughput-on-a-database)  |
| `autoScaleSettings` | `Object` | Krävs för [autoskalning-läge](provision-throughput-autoscale.md). Det här objektet innehåller inställningarna som är associerade med läget för autoskalning-kapacitet. Du kan ställa in `maxThroughput` värdet, som beskriver det högsta antalet enheter för programbegäran som databasen kommer att höjas till dynamiskt. |

Det här kommandot använder den databas som anges i sessionens kontext. Det här är den databas som du använde i `use <database>` kommandot. För tillfället går det inte att ändra databas namnet med det här kommandot.

### <a name="output"></a>Utdata

Om kommandot lyckas returneras följande svar:

```javascript
{ "ok" : 1 }
```

Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Uppdatera det etablerade data flödet som är associerat med en databas

Använd följande kommando för att uppdatera det etablerade data flödet för en databas med namnet `"test"` 1200 ru: er:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Uppdatera det automatiska skalnings data flöde som är associerat med en databas

Om du vill uppdatera det etablerade data flödet för en databas med namnet `"test"` till 20 000 ru: er, eller om du vill omvandla den till en [data flödes nivå med autoskalning](provision-throughput-autoscale.md), använder du följande kommando:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Hämta databas

Kommandot Hämta databas tillägg returnerar databasobjektet. Databas namnet används från databas kontexten som kommandot körs mot.

```javascript
{
  customAction: "GetDatabase"
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Namnet på det anpassade kommandot. Måste vara "GetDatabase"|
        
### <a name="output"></a>Utdata

Om kommandot lyckas innehåller svaret ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `database`    |    `string`        |   Namnet på databasen.      |
|   `provisionedThroughput`  |    `int`      |    Tillhandahållet data flöde som har ställts in på databasen om databasen använder ett  [manuellt data flöde på databas nivå](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Det här objektet innehåller de kapacitets parametrar som är kopplade till databasen om den använder [autoskalning-läget](provision-throughput-autoscale.md). `maxThroughput`Värdet beskriver det högsta antalet enheter för programbegäran som databasen kommer att höjas till dynamiskt. |

Om kommandot Miss lyckas returneras ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="get-the-database"></a>Hämta databasen

Använd följande kommando för att hämta databasobjektet för en databas med namnet `"test"` :

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Om databasen inte har något associerat data flöde skulle utdata bli:

```javascript
{ "database" : "test", "ok" : 1 }
```

Om databasen har ett [manuellt data flöde för databas nivå](set-throughput.md#set-throughput-on-a-database) som är associerat med den, visar utdata `provisionedThroughput` värdena:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Om databasen har ett [data flöde för autoskalning på databas nivå](provision-throughput-autoscale.md) som är associerat med den, visar utdata `provisionedThroughput` , som beskriver de lägsta ru/s för databasen och `autoScaleSettings` objektet inklusive `maxThroughput` , som beskriver det maximala antalet ru/s för databasen.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Skapa samling

Kommandot Skapa samlings tillägg skapar en ny MongoDB-samling. Databas namnet används från den databas kontext som anges av `use database` kommandot. Formatet för CreateCollection-kommandot är följande:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

I följande tabell beskrivs parametrarna i kommandot:

| **Fält** | **Typ** | **Obligatoriskt** | **Beskrivning** |
|---------|---------|---------|---------|
| `customAction` | `string` | Obligatorisk | Namnet på det anpassade kommandot. Måste vara "CreateCollection".|
| `collection` | `string` | Obligatorisk | Samlingens namn. Inga specialtecken eller mellanslag tillåts.|
| `offerThroughput` | `int` | Valfritt | Tillhandahållet data flöde som ska anges för-databasen. Om den här parametern inte anges kommer den att vara standard den lägsta, 400 RU/s. * För att ange data flöde utöver 10 000 RU/s `shardKey` krävs parametern.|
| `shardKey` | `string` | Krävs för samlingar med stort data flöde | Sökvägen till Shard-nyckeln för shardade-samlingen. Den här parametern krävs om du anger mer än 10 000 RU/s i `offerThroughput` .  Om den är angiven, kommer alla dokument som infogas kräva denna nyckel och värde. |
| `autoScaleSettings` | `Object` | Krävs för [autoskalning-läge](provision-throughput-autoscale.md) | Det här objektet innehåller inställningarna som är associerade med läget för autoskalning-kapacitet. Du kan ställa in `maxThroughput` värdet, som beskriver det högsta antalet enheter för programbegäran som samlingen ökar till dynamiskt. |

### <a name="output"></a>Utdata

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Skapa en samling med minsta konfiguration

Om du vill skapa en ny samling med namn `"testCollection"` och standardvärden använder du följande kommando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Detta leder till en ny fast, unsharded, samling med 400RU/s och ett index i fältet som `_id` skapas automatiskt. Den här typen av konfiguration kommer också att gälla när du skapar nya samlingar via `insert()` funktionen. Exempel: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Skapa en unsharded-samling

Om du vill skapa en unsharded-samling med namn `"testCollection"` och tillhandahållet data flöde på 1000 ru: er använder du följande kommando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Du kan skapa en samling med upp till 10 000 RU/s som den `offerThroughput` utan att behöva ange en Shard nyckel. För samlingar med större data flöde, se nästa avsnitt.

#### <a name="create-a-sharded-collection"></a>Skapa en shardade-samling

Om du vill skapa en shardade-samling med namn `"testCollection"` och tillhandahållet data flöde på 11 000 ru: er och en `shardkey` egenskap "a. b", använder du följande kommando:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Det här kommandot kräver nu `shardKey` parametern, eftersom mer än 10 000 ru/s anges i `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Skapa en unsharded AutoScale-samling

Använd följande kommando för att skapa en unsharded-samling med namnet med `'testCollection'` [autoskalning av data flödes kapacitet](provision-throughput-autoscale.md) som är inställd på 4 000 ru/s:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

För `autoScaleSettings.maxThroughput` värdet kan du ange ett intervall från 4 000 ru/s till 10 000 ru/s utan en Shard-nyckel. För högre data flöde för autoskalning måste du ange `shardKey` parametern.

#### <a name="create-a-sharded-autoscale-collection"></a>Skapa en shardade AutoScale-samling

Använd följande kommando för att skapa en shardade-samling med namnet `'testCollection'` med en Shard-nyckel med namnet `'a.b'` , och som använder [kapacitet för autoskalning av data flöde](provision-throughput-autoscale.md) som är inställd på 20 000 ru/s:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Uppdatera samling

Kommandot för att uppdatera samlings tillägg uppdaterar egenskaperna som är associerade med den angivna samlingen. Det går bara att ändra samlingen från etablerade data flöden till autoskalning och vice versa i Azure-portalen.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Namnet på det anpassade kommandot. Måste vara "UpdateCollection".      |
|  `collection`   |   `string`      |   Samlingens namn.       |
| `offerThroughput` | `int` |   Tillhandahållet data flöde som ska anges för samlingen.|
| `autoScaleSettings` | `Object` | Krävs för [autoskalning-läge](provision-throughput-autoscale.md). Det här objektet innehåller inställningarna som är associerade med läget för autoskalning-kapacitet. `maxThroughput`Värdet beskriver det högsta antalet enheter för programbegäran som samlingen kommer att höjas till dynamiskt. |

## <a name="output"></a>Utdata

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Uppdatera det allokerade data flödet som är associerat med en samling

Använd följande kommando för att uppdatera det etablerade data flödet för en samling med namnet `"testCollection"` 1200 ru: er:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Hämta samling

Det anpassade kommandot Hämta samling returnerar objektet Collection.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Namnet på det anpassade kommandot. Måste vara "GetCollection".      |
| `collection`    |    `string`     |    Samlingens namn.     |

### <a name="output"></a>Utdata

Om kommandot lyckas innehåller svaret ett dokument med följande fält


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `database`    |    `string`     |   Namnet på databasen.      |
| `collection`    |    `string`     |    Samlingens namn.     |
|  `shardKeyDefinition`   |   `document`      |  Index Specifikations dokument som används som en Shard nyckel. Detta är en valfri svars parameter.       |
|  `provisionedThroughput`   |   `int`      |    Tillhandahållet data flöde som ska anges för samlingen. Detta är en valfri svars parameter.     |
| `autoScaleSettings` | `Object` | Det här objektet innehåller de kapacitets parametrar som är kopplade till databasen om den använder [autoskalning-läget](provision-throughput-autoscale.md). `maxThroughput`Värdet beskriver det högsta antalet enheter för programbegäran som samlingen kommer att höjas till dynamiskt. |

Om kommandot Miss lyckas returneras ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

#### <a name="get-the-collection"></a>Hämta samlingen

Använd följande kommando för att hämta samlings objekt för en samling med namnet `"testCollection"` :

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Om samlingen har en associerad data flödes kapacitet till den, kommer den att innehålla `provisionedThroughput` värdet och utdata skulle vara:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Om samlingen har ett associerat data flöde för autoskalning kommer den att innehålla `autoScaleSettings` objektet med `maxThroughput` parametern, som definierar det maximala data flöde som samlingen ökar till dynamiskt. Dessutom innehåller den även `provisionedThroughput` värdet som definierar det lägsta data flöde som den här samlingen kommer att minska till om det inte finns några begär anden i samlingen: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Om samlingen delar [data flöde på databas nivå](set-throughput.md#set-throughput-on-a-database), antingen i autoskalning eller manuell, blir utdata:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Standardutdata för ett anpassat kommando

Om inget anges innehåller ett anpassat svar ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `code`    |   `int`      |   Returneras endast om kommandot misslyckades (t. ex. OK = = 0). Innehåller fel koden för MongoDB. Detta är en valfri svars parameter.      |
|  `errMsg`   |  `string`      |    Returneras endast om kommandot misslyckades (t. ex. OK = = 0). Innehåller ett användarvänligt fel meddelande. Detta är en valfri svars parameter.      |

Exempel:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig följande Azure Cosmos DB koncept: 

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
