---
title: Prestanda-och skalnings testning med Azure Cosmos DB
description: Lär dig hur du kan skala och testa prestanda med Azure Cosmos DB. Du kan sedan utvärdera funktionerna i Azure Cosmos DB för högpresterande program scenarier.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cb0dd8eb879c5511da3c79a3dee6a715fb1275e4
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358681"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestanda-och skalnings testning med Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Prestanda-och skalnings testning är ett viktigt steg i program utveckling. För många program har databas nivån en betydande inverkan på övergripande prestanda och skalbarhet. Därför är det en viktig komponent i prestanda testningen. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är syfte-byggt för elastisk skalning och förutsägbar prestanda. Dessa funktioner gör att den passar bra för program som behöver en databas nivå med hög prestanda. 

Den här artikeln är en referens för utvecklare som implementerar prestanda test paket för Azure Cosmos DB arbets belastningar. Det kan också användas för att utvärdera Azure Cosmos DB för program scenarier med hög prestanda. Den fokuserar främst på isolerade prestanda testningar av databasen, men innehåller också bästa praxis för produktions program.

När du har läst den här artikeln kan du svara på följande frågor: 

* Var kan jag hitta ett exempel på ett .NET-klient program för prestanda testning av Azure Cosmos DB? 
* Hur gör jag för att uppnå höga data flödes nivåer med Azure Cosmos DB från mitt klient program?

Kom igång med kod genom att ladda ned projektet från [Azure Cosmos DB exempel på prestandatest](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Syftet med det här programmet är att demonstrera hur du får bästa möjliga prestanda från Azure Cosmos DB med ett litet antal klient datorer. Syftet med exemplet är inte att uppnå den högsta data flödes kapaciteten för Azure Cosmos DB (som kan skalas utan gränser).

Om du letar efter konfigurations alternativ på klient sidan för att förbättra Azure Cosmos DB prestanda, se [Azure Cosmos DB prestanda tips](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Kör programmet för prestanda testning
Det snabbaste sättet att komma igång är att kompilera och köra .NET-exemplet, vilket beskrivs i följande steg. Du kan också granska käll koden och implementera liknande konfigurationer i dina egna klient program.

**Steg 1:** Hämta projektet från [Azure Cosmos DB exempel på prestandatest](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)eller förgrening av GitHub-lagringsplatsen.

**Steg 2:** Ändra inställningarna för EndpointUrl, AuthorizationKey, CollectionThroughput och DocumentTemplate (valfritt) i App.config.

> [!NOTE]
> Innan du etablerar samlingar med högt data flöde, se [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) för att beräkna kostnaderna per samling. Azure Cosmos DB räkningar lagring och data flöde oberoende av varje timme. Du kan spara kostnader genom att ta bort eller minska genomflödet för dina Azure Cosmos-behållare efter testning.
> 
> 

**Steg 3:** Kompilera och kör-konsol programmet från kommando raden. Du bör se utdata enligt följande:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**Steg 4 (om det behövs):** Data flödet som rapporterades (RU/s) från verktyget ska vara samma eller högre än det etablerade data flödet för samlingen eller en uppsättning samlingar. Om du inte gör det kan du öka gränsen genom att öka DegreeOfParallelism i små steg. Om data flödet från klient appen plateaus startar du flera instanser av appen på ytterligare klient datorer. Om du behöver hjälp med den här steg filen som ett support ärende från [Azure Portal](https://portal.azure.com).

När du har kört appen kan du prova olika [indexerings principer](index-policy.md) och [konsekvens nivåer](consistency-levels.md) för att förstå deras påverkan på data flöde och svars tid. Du kan också granska käll koden och implementera liknande konfigurationer i dina egna test serier eller produktions program.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi tittat på hur du kan utföra prestanda-och skalnings testning med Azure Cosmos DB med hjälp av en .NET-konsol program. Mer information finns i följande artiklar:

* [Exempel på Azure Cosmos DB prestanda testning](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)
* [Alternativ för klient konfiguration för att förbättra Azure Cosmos DB prestanda](performance-tips.md)
* [Partitionering på Server sidan i Azure Cosmos DB](partitioning-overview.md)


