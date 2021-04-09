---
title: Översikt över massexecutor-biblioteket i Azure Cosmos DB
description: 'Utför Mass åtgärder i Azure Cosmos DB via Mass import och Mass uppdaterings-API: er som erbjuds av bulk utförar-biblioteket.'
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100481"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Översikt över massexecutor-biblioteket i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure Cosmos DB är en snabb, flexibel och globalt distribuerad databastjänst som är utformad för att elastiskt skala ut och stödja: 

* Stora läs- och skrivdataflöden (flera miljoner åtgärder per sekund).  
* Lagra stora mängder (hundratals terabyte eller mer) transaktions- och åtgärdsdata med förutsägbara millisekundssvarstider.  

Massexecutor-biblioteket hjälper dig att dra nytta av det omfattande dataflödet och lagringsutrymmet. Med massexecutor-biblioteket kan du utföra massåtgärder i Azure Cosmos DB med hjälp av API:er för massimport och massuppdatering. Du kan läsa mer om funktionerna i massexecutor-biblioteket i följande avsnitt. 

> [!NOTE] 
> För närvarande stöder bulk utförar-biblioteket import-och uppdaterings åtgärder och det här biblioteket stöds endast av Azure Cosmos DB SQL API-och Gremlin API-konton.

> [!IMPORTANT]
> Bulk utförar-biblioteket stöds inte för närvarande på [Server](serverless.md) lösta konton. I .NET rekommenderar vi att du använder [Mass stödet](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) som finns i v3-versionen av SDK.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Huvud funktioner i bulk utförar-biblioteket  
 
* Det minskar avsevärt de beräknings resurser på klient sidan som krävs för att fylla data flödet som allokerats till en behållare. Ett enda trådat program som skriver data med hjälp av Mass import-API: n uppnår 10 gånger större Skriv data flöde jämfört med ett program med flera trådar som skriver parallella data samtidigt som nätverket överbelastas klient datorns processor.  

* Den sammanfattar de omständliga uppgifterna i att skriva program logik för att hantera hastighets begränsning av begäran, begär tids gränser och andra tillfälliga undantag genom att effektivt hantera dem i biblioteket.  

* Det ger en förenklad mekanism för program som utför Mass åtgärder för att skala ut. En enskild utförar-instans som körs på en virtuell Azure-dator kan förbruka mer än 500 000 RU/s och du kan uppnå en högre data flödes hastighet genom att lägga till ytterligare instanser på enskilda virtuella klient datorer.  
 
* Den kan Mass import av mer än en terabyte data inom en timme med hjälp av en skalbar arkitektur.  

* Det kan Mass uppdatera befintliga data i Azure Cosmos-behållare som korrigeringar. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hur fungerar bulk-utförar? 

När en Mass åtgärd för import eller uppdatering av dokument utlöses med en batch med entiteter, grupperas de först i buckets som motsvarar deras Azure Cosmos DB partitionsnyckel. I varje Bucket som motsvarar ett nyckel intervall är de uppdelade i mini-batchar och varje mini-batch fungerar som en nytto last som bekräftas på Server sidan. Bulk utförar-biblioteket har byggts i optimeringar för samtidig körning av dessa mini-batchar både inom och över partitionerings nyckel intervall. Följande bild illustrerar hur Mass utförar batcherar data i olika partitionsnyckel:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Mass utförar-arkitektur" :::

I bulk utförar-biblioteket ser du till att maximally använder det data flöde som allokerats till en samling. Den använder en [AIMD för överbelastnings kontroll](https://tools.ietf.org/html/rfc5681) för varje Azure Cosmos DB partitionsnyckel för att effektivt hantera hastighets begränsning och tids gränser. 

## <a name="next-steps"></a>Nästa steg 
  
* Lär dig mer genom att testa exempel programmen som använder bulk utförar-biblioteket i [.net](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md).  
* Kolla in utförar SDK-information och viktig information i [.net](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md).
* Bulk utförar-biblioteket är integrerat i Cosmos DB Spark-anslutningsprogrammet för mer information finns i artikeln [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Bulk utförar-biblioteket är också integrerat i en ny version av [Azure Cosmos DB Connector](../data-factory/connector-azure-cosmos-db.md) för Azure Data Factory för att kopiera data.
