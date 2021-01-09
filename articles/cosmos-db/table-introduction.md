---
title: Introduktion till tabell-API:et för Azure Cosmos DB
description: Läs hur du kan använda Azure Cosmos DB för att lagra och ställa frågor till massiva volymer av nyckelvärdesdata med kort svarstid med hjälp av Azure Tables API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 01/08/2021
ms.author: sngun
ms.openlocfilehash: 1cf3bf30b37a09b5dfe94bf1e754a7f8e9dcd82c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045673"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduktion till Azure Cosmos DB | Tabell-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Microsoft Azure Cosmos-DB](introduction.md) tillhandahåller tabell-API:t för program som är skrivna för Azure-tabellagring och som behöver premiumfunktioner som:

* [Nyckel färdig global distribution](distribute-data-globally.md).
* [Dedikerat data flöde](partitioning-overview.md) över hela världen (när du använder ett allokerat data flöde).
* Latensvärden på enstaka millisekunder vid 99:e percentilen.
* Garanterat hög tillgänglighet.
* Automatisk sekundär indexering.

Program som är skrivna för Azure Table Storage kan migrera till Azure Cosmos DB med hjälp av ett tabell-API och på så sätt dra nytta av premiumfunktioner, utan kodändringar. Tabell-API:t har tillgängliga klient-SDK:er för .NET, Java, Python och Node.js.

> [!NOTE]
> [Läget för Server lös kapacitet](serverless.md) är nu tillgängligt på Azure Cosmos DB tabell-API.

> [!IMPORTANT]
> .NET Framework SDK [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) är i underhålls läge och kommer snart att vara inaktuell. Uppgradera till det nya .NET standard-biblioteket [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) för att fortsätta hämta de senaste funktionerna som stöds av tabell-API.

## <a name="table-offerings"></a>Tabellerbjudanden
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s tabell-API:

| Funktion | Azure Table Storage | Tabell-API för Azure Cosmos DB |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid. | Enkel svars tid i millisekunder för läsningar och skrivningar, med <10 MS svars tid för läsningar och skrivningar i 99 percentilen, i valfri skala, var som helst i världen. |
| Dataflöde | Modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s. | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](request-units.md) som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell. |
| Global distribution | En enda region med en valfri läsbar sekundär läsregion för hög tillgänglighet. | [Nyckel färdig global distribution](distribute-data-globally.md) från en till ett valfritt antal regioner. Stöd för [automatisk och manuell redundans](high-availability.md) när som helst och var som helst i världen. Flera Skriv regioner för att låta en region godkänna Skriv åtgärder. |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index. | Automatisk och fullständig indexering för alla egenskaper som standard, utan index hantering. |
| Söka i data | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. |
| Konsekvens | Stark inom primär region. Eventuell inom sekundär region. | [Fem väldefinierade konsekvens nivåer](consistency-levels.md) för att handla om tillgänglighet, svars tid, data flöde och konsekvens baserat på dina program behov. |
| Prissättning | Förbruknings-baserade. | Tillgängligt i både [konsumtions-](serverless.md) och [etablerade kapacitets](set-throughput.md) lägen. |
| Serviceavtal | 99,9% till 99,99% tillgänglighet, beroende på strategi för replikering. | 99,999% Läs tillgänglighet, 99,99% Skriv tillgänglighet för ett konto med en region och 99,999% Skriv tillgänglighet på flera region konton. [Omfattande service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) som täcker tillgänglighet, svars tid, data flöde och konsekvens. |

## <a name="get-started"></a>Kom igång

Skapa ett nytt Azure Cosmos DB-konto på [Azure-portalen](https://portal.azure.com). Kom sedan igång med vår [Snabbstart för tabell-API med hjälp av .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:
* [Skapa en .NET-app med tabell-API:t](create-table-dotnet.md)
* [Utveckla med tabell-API:t i .NET](tutorial-develop-table-dotnet.md)
* [Fråga tabelldata med tabell-API](tutorial-query-table.md)
* [Lär dig konfigurera global distribution i Azure Cosmos DB med tabell-API:t](tutorial-global-distribution-table.md)
* [Azure Cosmos DB tabell .NET standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB tabell .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB tabell Java SDK](table-sdk-java.md)
* [Azure Cosmos DB tabell Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB-tabell SDK för Python](table-sdk-python.md)
