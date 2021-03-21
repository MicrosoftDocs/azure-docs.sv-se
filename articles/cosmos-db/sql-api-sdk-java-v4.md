---
title: Viktig information och resurser för Azure Cosmos DB Java SDK v4 för SQL API
description: Lär dig allt om Azure Cosmos DB Java SDK v4 för SQL API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e23a1ed348f2a627181e0e4e4c20477ee9ba1ff8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210554"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: viktig information och resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Spring-data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring-data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutningsprogram](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core (SQL) kombinerar ett asynkront API och ett Sync-API till en maven-artefakt. V4 SDK ger förbättrade prestanda, nya API-funktioner och asynkront stöd baserat på projekt reaktor och det [Netta biblioteket](https://netty.io/). Användare kan förväntas förbättra prestanda med Azure Cosmos DB Java SDK v4 jämfört med [Azure Cosmos DB asynkron Java SDK v2](sql-api-sdk-async-java.md) och [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Den här viktig information gäller endast Azure Cosmos DB Java SDK v4. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>
> Här är tre steg för att komma igång snabbt!
> 1. Installera den [lägsta Java-körningen som stöds, JDK 8,](/java/azure/jdk/) så att du kan använda SDK: n.
> 2. Arbeta via [snabb starts guiden för Azure Cosmos DB Java SDK v4 som ger](./create-sql-api-java.md) dig till gång till maven-artefakten och går igenom grundläggande Azure Cosmos DB förfrågningar.
> 3. Läs [prestanda tipsen](performance-tips-java-sdk-v4-sql.md) för Azure Cosmos DB Java SDK v4 och [fel söknings](troubleshoot-java-sdk-v4-sql.md) guider för att optimera SDK för ditt program.
>
> [Azure Cosmos DB workshops och Labs](https://aka.ms/cosmosworkshop) är en annan fantastisk resurs för att lära dig hur du använder Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Länk |
|---|---|
|**SDK-nedladdning**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentation** | [Referens dokumentation för Java API](/java/api/overview/azure/cosmosdb/client) |
|**Bidra till SDK** | [Azure SDK för Java Central lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Komma igång** | [Snabb start: bygga en Java-app för att hantera Azure Cosmos DB SQL API-data](./create-sql-api-java.md) <br> [GitHub lagrings platsen med snabb starts kod](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Grundläggande kod exempel** | [Azure Cosmos DB: Java-exempel för SQL API](sql-api-java-sdk-samples.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konsol program med ändrings flöde**| [Ändra feed-Java SDK v4-exempel](create-sql-api-java-changefeed.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exempel på webbapp**| [Bygg en webbapp med Java SDK v4](sql-api-java-application.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Prestandatips**| [Prestandatips för Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java-SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrera till v4 från en äldre SDK** | [Migrera till Java V4-SDK](migrate-java-v4-sdk.md) |
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB workshops och labb** |[Start sida för Cosmos DB workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Vanliga frågor
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Nästa steg
Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).