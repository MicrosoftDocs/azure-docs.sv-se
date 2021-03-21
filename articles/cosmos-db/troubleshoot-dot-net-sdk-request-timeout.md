---
title: Felsök Azure Cosmos DB HTTP 408 eller begär timeout-problem med .NET SDK
description: Lär dig hur du diagnostiserar och åtgärdar timeout-undantag för .NET SDK-begäran.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440011"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB timeout-undantag för .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 408-felet uppstår om SDK: n inte kunde slutföra begäran innan tids gränsen nåddes.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Anpassa tids gränsen för Azure Cosmos DB .NET SDK

SDK har två olika alternativ för att kontrol lera tids gränser, var och en med olika omfång.

### <a name="requesttimeout"></a>RequestTimeout

Med `CosmosClientOptions.RequestTimeout` konfigurationen (eller `ConnectionPolicy.RequestTimeout` för SDK v2) kan du ange en tids gräns som påverkar varje enskild nätverks förfrågan. En åtgärd som startas av en användare kan omfatta flera nätverks begär Anden (till exempel kan begränsningen kunna begränsas). Den här konfigurationen gäller för varje nätverks förfrågan på nytt försök. Denna timeout är inte en tids gräns för begäran från slut punkt till slut punkt.

### <a name="cancellationtoken"></a>CancellationToken

Alla asynkrona åtgärder i SDK har en valfri CancellationToken-parameter. Denna [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) -parameter används i hela åtgärden, över alla nätverks begär Anden. I mellan nätverks begär Anden kan det hända att token för annullering kontrol leras och en åtgärd avbryts om den relaterade token har upphört att gälla. Token för annullering ska användas för att definiera en ungefärlig förväntad tids gräns i åtgärds omfånget.

> [!NOTE]
> `CancellationToken`Parametern är en mekanism där biblioteket kontrollerar annulleringen när det [inte orsakar ett ogiltigt tillstånd](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Åtgärden kan inte avbrytas exakt när tiden som definierats i annulleringen är upp. När tiden är upp avbryts det när det är säkert att göra det.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för timeout-undantag för begäran.

### <a name="high-cpu-utilization"></a>Hög CPU-belastning
Hög processor användning är det vanligaste fallet. För optimal latens bör CPU-användningen vara ungefär 40 procent. Använd 10 sekunder som intervall för att övervaka maximal processor belastning (inte Genomsnittligt). CPU-toppar är vanligare med frågor över flera partitioner där det kan göra flera anslutningar för en enda fråga.

Om felet innehåller `TransportException` information kan det även innehålla `CPU History` :

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Om CPU-mätningarna är över 70% är tids gränsen troligt vis orsakad av processor belastningen. I det här fallet är lösningen att undersöka källan till hög processoranvändning och minska den, eller skala datorn till en större resursstorlek.
* Om CPU-mätningarna inte sker var tionde sekund (t. ex. luckor eller mätningstider indikerar längre tid mellan måtten) är orsaken trådsvält. I det här fallet är lösningen att undersöka orsaken till trådsvälten (potentiellt låsta trådar) eller skala datorn/datorerna till en större resursstorlek.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp eller ut.

### <a name="socket-or-port-availability-might-be-low"></a>Tillgängligheten för socket eller porten kan vara låg
När du kör i Azure kan klienter som använder .NET SDK nå ut till PAT-port (Azure SNAT).

#### <a name="solution-1"></a>Lösning 1:
Om du kör på virtuella Azure-datorer följer du [guiden för SNAT-portens överbelastnings guide](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Lösning 2:
Om du kör på Azure App Service följer du [fel söknings guiden för anslutnings fel](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) och [använder App Service diagnostik](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Lösning 3:
Om du kör på Azure Functions kontrollerar du att du följer [Azure Functions rekommendationer](../azure-functions/manage-connections.md#static-clients) för att underhålla Singleton eller statiska klienter för alla berörda tjänster (inklusive Azure Cosmos dB). Kontrol lera [tjänst gränserna](../azure-functions/functions-scale.md#service-limits) utifrån typ och storlek för Funktionsapp-värd.

#### <a name="solution-4"></a>Lösning 4:
Om du använder en HTTP-proxy kontrollerar du att den har stöd för det antal anslutningar som kon figurer ATS i SDK `ConnectionPolicy` . I annat fall får du problem med anslutning till ansikte.

### <a name="create-multiple-client-instances"></a>Skapa flera klient instanser
Att skapa flera klient instanser kan leda till problem med anslutningens innehåll och timeout.

#### <a name="solution"></a>Lösning:
Följ [prestanda tipsen](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)och Använd en enda CosmosClient-instans i hela processen.

### <a name="hot-partition-key"></a>Nyckel för snabb partition
Azure Cosmos DB distribuerar det övergripande allokerade data flödet jämnt över fysiska partitioner. När det finns en aktiv partition tar en eller flera logiska partitionsnyckel på en fysisk partition över alla den fysiska partitionens enheter för programbegäran per sekund (RU/s). På samma gång går RU/s på andra fysiska partitioner oanvända. Det totala antalet RU/s som förbrukas är mindre än den totala etablerade RU/s i databasen eller behållaren, men du ser fortfarande begränsning (429s) för begär Anden mot den heta logiska partitionsnyckel. Använd [måttet normaliserad ru-förbrukning](monitor-normalized-request-units.md) för att se om arbets belastningen påträffar en aktiv partition. 

#### <a name="solution"></a>Lösning:
Välj en lämplig partitionsnyckel som jämnt distribuerar begär ande volym och lagring. Lär dig hur du [ändrar partitionsnyckel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Hög grad av samtidighet
Programmet gör en hög nivå av samtidighet, vilket kan leda till konkurrens på kanalen.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp eller ut.

### <a name="large-requests-or-responses"></a>Stora begär Anden eller svar
Stora begär Anden eller svar kan leda till att det finns en rad olika block i kanal-och exacerbate-konkurrens, även med en relativt låg grad av samtidighet.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp eller ut.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>Felfrekvensen ligger inom Azure Cosmos DB SLA
Programmet ska kunna hantera tillfälliga fel och försöka igen när det behövs. Inga 408-undantag görs igen på grund av att det är omöjligt att veta om tjänsten skapade objektet eller inte när den skapas. Om du skickar samma objekt igen för att skapa uppstår ett konflikt undantag. Affärs logik för användar program kan ha anpassad logik för att hantera konflikter, vilket skulle leda till att det finns en konflikt mellan tvetydigheten för ett befintligt objekt jämfört med en konflikt med att skapa nya försök.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Felfrekvensen strider mot Azure Cosmos DB SLA
Kontakta [Azure-supporten](https://aka.ms/azure-support).

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).