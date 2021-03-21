---
title: Felsök Azure Cosmos DB HTTP 408 eller begär timeout-problem med Java v4 SDK
description: Lär dig hur du diagnostiserar och åtgärdar timeout-undantag i Java SDK med Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411294"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB timeout-undantag för Java v4 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 408-felet uppstår om SDK: n inte kunde slutföra begäran innan tids gränsen nåddes.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för timeout-undantag för begäran.

### <a name="existing-issues"></a>Befintliga problem
Om du ser begär Anden som fastnat för längre varaktighet eller tids gräns oftare, kan du uppgradera Java v4 SDK till den senaste versionen. Obs: Vi rekommenderar starkt att du använder version 4.7.0 och senare. Kolla in [viktigt om Java v4 SDK](sql-api-sdk-java-v4.md) för mer information.

### <a name="high-cpu-utilization"></a>Hög CPU-belastning
Hög processor användning är det vanligaste fallet. För optimal latens bör CPU-användningen vara ungefär 40 procent. Använd 10 sekunder som intervall för att övervaka maximal processor belastning (inte Genomsnittligt). CPU-toppar är vanligare med frågor över flera partitioner där det kan göra flera anslutningar för en enda fråga.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp eller ut.

### <a name="connection-throttling"></a>Anslutnings begränsning
Anslutnings begränsning kan inträffa på grund av en anslutnings gräns på en värddator eller Azure SNAT-port (PAT).

### <a name="connection-limit-on-a-host-machine"></a>Anslutnings gräns på en värddator
Vissa Linux-system, till exempel Red Hat, har en övre gräns för det totala antalet öppna filer. Sockets i Linux implementeras som filer, så det här antalet begränsar även det totala antalet anslutningar. Kör följande kommando.

```bash
ulimit -a
```

#### <a name="solution"></a>Lösning:
Antalet tillåtna öppna filer, som identifieras som "nofile", måste vara minst 10 000 eller mer. Mer information finns i [prestanda tips](performance-tips-java-sdk-v4-sql.md)för Azure Cosmos DB Java SDK v4.

### <a name="socket-or-port-availability-might-be-low"></a>Tillgängligheten för socket eller porten kan vara låg
När du kör i Azure kan klienter som använder Java SDK nå ut från PAT-port (Azure SNAT).

#### <a name="solution-1"></a>Lösning 1:
Om du kör på virtuella Azure-datorer följer du [guiden för SNAT-portens överbelastnings guide](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Lösning 2:
Om du kör på Azure App Service följer du [fel söknings guiden för anslutnings fel](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) och [använder App Service diagnostik](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Lösning 3:
Om du kör på Azure Functions kontrollerar du att du följer [Azure Functions rekommendationer](../azure-functions/manage-connections.md#static-clients) för att underhålla Singleton eller statiska klienter för alla berörda tjänster (inklusive Azure Cosmos dB). Kontrol lera [tjänst gränserna](../azure-functions/functions-scale.md#service-limits) utifrån typ och storlek för Funktionsapp-värd.

#### <a name="solution-4"></a>Lösning 4:
Om du använder en HTTP-proxy kontrollerar du att den har stöd för det antal anslutningar som kon figurer ATS i SDK `GatewayConnectionConfig` . I annat fall får du problem med anslutning till ansikte.

### <a name="create-multiple-client-instances"></a>Skapa flera klient instanser
Att skapa flera klient instanser kan leda till problem med anslutningens innehåll och timeout.

#### <a name="solution-1"></a>Lösning 1:
Följ [prestanda tipsen](performance-tips-java-sdk-v4-sql.md#sdk-usage)och Använd en enda CosmosClient-instans i hela programmet.

#### <a name="solution-2"></a>Lösning 2:
Om singleton-CosmosClient inte är möjligt att ha i ett program, rekommenderar vi att du använder anslutnings delning över flera Cosmos-klienter via detta API `connectionSharingAcrossClientsEnabled(true)` i CosmosClient. När du har flera instanser av Cosmos-klienten i samma JVM som interagerar med flera Cosmos-konton, aktiverar detta Tillåt anslutnings delning i direkt läge om det är möjligt mellan instanser av Cosmos-klienten. Observera att när du anger det här alternativet används anslutnings konfigurationen (t. ex. timeout-konfiguration för socket, timeout-konfiguration för inaktivitet) för den första instansierade klienten för alla andra klient instanser.

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
* [Diagnostisera och Felsök](troubleshoot-java-sdk-v4-sql.md) problem när du använder Azure Cosmos DB Java v4 SDK.
* Lär dig mer om prestanda rikt linjer för [Java v4](performance-tips-java-sdk-v4-sql.md).
