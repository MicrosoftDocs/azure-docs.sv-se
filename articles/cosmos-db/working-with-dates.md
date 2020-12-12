---
title: Arbeta med datum i Azure Cosmos DB
description: Lär dig att lagra, indexera och fråga datum/tid-objekt i Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359531"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeta med datum i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ger flexibilitet i schema och omfattande indexering via en intern [JSON](https://www.json.org) -datamodell. Alla Azure Cosmos DB resurser, inklusive databaser, behållare, dokument och lagrade procedurer, modelleras och lagras som JSON-dokument. Som ett krav för portabelt stöder JSON (och Azure Cosmos DB) bara en liten uppsättning grundläggande typer: sträng, tal, boolesk, matris, objekt och null. JSON är dock flexibel och gör det möjligt för utvecklare och ramverk att representera mer komplexa typer med hjälp av dessa primitiver och att skriva dem som objekt eller matriser.

Förutom de grundläggande typerna behöver många program DateTime-typen för att representera datum och tidsstämpel. Den här artikeln beskriver hur utvecklare kan lagra, hämta och fråga datum i Azure Cosmos DB med hjälp av .NET SDK.

## <a name="storing-datetimes"></a>Lagra datum/tid

Azure Cosmos DB stöder JSON-typer som-String, Number, Boolean, null, matris, Object. Det stöder inte direkt en DateTime-typ. För närvarande stöder Azure Cosmos DB inte lokalisering av datum. Så du måste lagra datum och tid som strängar. Det rekommenderade formatet för DateTime-strängar i Azure Cosmos DB är `yyyy-MM-ddTHH:mm:ss.fffffffZ` som följer ISO 8601 UTC-standarden. Vi rekommenderar att du lagrar alla datum i Azure Cosmos DB som UTC. Om du konverterar datum strängarna till det här formatet kan sorterings datumen lexicographically. Om icke-UTC-datum lagras måste logiken hanteras på klient sidan. Om du vill konvertera en lokal datum-DateTime till UTC måste förskjutningen vara känd/lagrad som en egenskap i JSON och klienten kan använda förskjutningen för att beräkna UTC-slutdatum svärdet.

Intervall frågor med DateTime-strängar som filter stöds bara om DateTime-strängarna är alla i UTC och samma längd. I Azure Cosmos DB Returnerar system funktionen [GetCurrentDateTime](sql-query-getcurrentdatetime.md) det aktuella UTC-datumet och tiden, ISO 8601-strängvärdet i formatet: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

De flesta program kan använda standard sträng representationen för DateTime av följande anledningar:

* Strängar kan jämföras och den relativa ordningen på DateTime-värdena bevaras när de omvandlas till strängar.
* Den här metoden kräver ingen anpassad kod eller attribut för JSON-konvertering.
* Datumen som lagras i JSON är läsliga.
* Den här metoden kan dra nytta av Azure Cosmos DBs index för snabba frågor.

Följande fragment lagrar till exempel ett- `Order` objekt som innehåller två datetime-egenskaper – `ShipDate` och `OrderDate` som ett dokument med hjälp av .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Det här dokumentet lagras i Azure Cosmos DB på följande sätt:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Du kan också lagra datum och tid som UNIX-tidsstämplar, det vill säga ett tal som representerar antalet förflutna sekunder sedan den 1 januari 1970. Azure Cosmos DB s interna timestamp ( `_ts` )-egenskap följer den här metoden. Du kan använda klassen [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) för att serialisera datetime-värden som tal.

## <a name="querying-datetimes-in-linq"></a>Fråga DateTimes i LINQ

SQL .NET SDK stöder automatiskt frågor mot data som lagras i Azure Cosmos DB via LINQ. I följande kodfragment visas till exempel en LINQ-fråga som filtrerar order som har levererats under de senaste tre dagarna:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Översatt till följande SQL-uttryck och körs på Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Du kan lära dig mer om SQL-frågespråket i Azure Cosmos DB och LINQ-providern vid [frågor Cosmos db i LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexera datum/tid för intervall frågor

Frågor är vanliga med DateTime-värden. Om du vill köra dessa frågor effektivt måste du ha ett index definierat för alla egenskaper i frågans filter.

Du kan lära dig mer om hur du konfigurerar indexerings principer på [Azure Cosmos DB indexerings principer](index-policy.md). 

## <a name="next-steps"></a>Nästa steg

* Hämta och kör [kod exemplen på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)
* Läs mer om [SQL-frågor](sql-query-getting-started.md)
* Läs mer om [Azure Cosmos DB indexerings principer](index-policy.md)