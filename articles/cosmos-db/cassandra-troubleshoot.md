---
title: Felsök vanliga fel i Azure Cosmos DB API för Cassandra
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstår i Azure Cosmos DB API för Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658579"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Felsök vanliga problem i Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB är ett kompatibilitetsläge som tillhandahåller stöd för [Wire-protokoll](cassandra-support.md) för den populära Apache Cassandra-databasen med öppen källkod och drivs av [Azure Cosmos DB](./introduction.md). Som en helt hanterad molnbaserad tjänst Azure Cosmos DB ger [garantier avseende tillgänglighet, data flöde och konsekvens](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) för API för Cassandra. Dessa garantier är inte möjliga i äldre implementeringar av Apache Cassandra. API för Cassandra fören klar också plattforms åtgärder utan drift, och uppdatering utan avbrott. Därför skiljer sig många av IT-backend-åtgärder från Apache Cassandra, så vi rekommenderar särskilda inställningar och metoder för att undvika vanliga fel. 

I den här artikeln beskrivs vanliga fel och lösningar för program som konsumerar Azure Cosmos DB API för Cassandra. Om felet inte finns med i listan nedan och ett fel uppstår när du kör en åtgärd som [stöds i API för Cassandra](cassandra-support.md), där felet *inte finns när du använder ursprunglig Apache-Cassandra*, skapar du [en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Detta är ett omslutnings undantag på översta nivån med ett stort antal möjliga orsaker och inre undantag, som många kan vara klient-relaterade. 
### <a name="solution"></a>Lösning
Några populära orsaker och lösningar är följande: 
- Tids gräns för inaktivitet i Azure belastningsutjämnare: Detta kan även manifesta som `ClosedConnectionException` . Lös detta genom att ange Keep Alive-inställningen i driv rutinen (se [nedan](#enable-keep-alive-for-java-driver)) och öka inställningarna för Keep-Alive i operativ systemet, eller [Justera inaktiv tids gräns i Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Belastning för klient program resurs:** kontrol lera att klient datorerna har tillräckligt med resurser för att slutföra begäran. 

## <a name="cannot-connect-to-host"></a>Det går inte att ansluta till värden
Du kanske ser det här felet: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Lösning
Detta kan vara SNAT-belastning på klient sidan. Följ stegen i [SNAT för utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) för att ta bort det här problemet. Detta kan också vara ett problem med tids gräns för inaktivitet där Azure Load Balancer har 4 minuters inaktivitet som standard. Se dokumentationen vid [belastnings utjämning för inaktivitet](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Aktivera TCP-Keep Alive från driv rutins inställningarna (se [nedan](#enable-keep-alive-for-java-driver)) och ange `keepAlive` intervallet för operativ systemet till mindre än 4 minuter.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
Det totala antalet förbrukade enheter för förbrukad enhet är fler än de begärda enheterna som har allokerats på tecken utrymmet eller tabellen. Så att förfrågningarna är begränsade.
### <a name="solution"></a>Lösning
Överväg att skala data flödet som är kopplat till ett tecken utrymme eller en tabell från Azure Portal (se [här](manage-scale-cassandra.md) för skalnings åtgärder i API för Cassandra) eller så kan du implementera en princip för återförsök. För Java, se exempel på nya försök för [v3. x driv rutin](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) och [v4. x-drivrutin](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Se även [Azure Cosmos Cassandra-tillägg för Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException även med tillräckligt data flöde 
Systemet förefaller vara begränsnings begär Anden trots att det finns tillräckligt med data flöde som tillhandahålls för begär ande volym och/eller förbrukad enhets kostnad. Det finns två möjliga orsaker till oväntad hastighets begränsning:
- **Åtgärder på schema nivå:** API för Cassandra implementerar system data flödes budgeten för åtgärder på schema nivå (CREATE TABLE, ALTER TABLE, DROP TABLE). Den här budgeten bör vara tillräckligt för schema åtgärder i ett produktions system. Men om du har ett stort antal åtgärder på schema nivå är det möjligt att du överskrider den här gränsen. Eftersom den här budgeten inte är användarvänlig, behöver du överväga att minska antalet schema åtgärder som ska köras. Om den här åtgärden inte löser problemet, eller om det inte är möjligt för din arbets belastning, kan du [skapa en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Data skevning:** när data flödet är etablerad i API för Cassandra delas det jämnt mellan fysiska partitioner och varje fysisk partition har en övre gräns. Om du har en hög mängd data som infogas eller efter frågas från en viss partition, är det möjligt att vara avgiftsbelagd trots att en stor mängd data flöde (enheter för programbegäran) har skapats för tabellen. Granska din data modell och se till att du inte har en alltför stor förvrängning som kan orsaka varma partitioner. 

## <a name="intermittent-connectivity-errors-java"></a>Tillfälligt anslutnings fel (Java) 
Anslutningen tappas bort eller nådde tids gränsen.

### <a name="solution"></a>Lösning 
Apache Cassandra-drivrutinerna för Java innehåller två principer för intern åter anslutning: `ExponentialReconnectionPolicy` och `ConstantReconnectionPolicy` . Standardvärdet är `ExponentialReconnectionPolicy`. Men för Azure Cosmos DB API för Cassandra rekommenderar vi att du `ConstantReconnectionPolicy` har en fördröjning på 2 sekunder. Se [driv rutins dokumentationen](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  för Java v4. x-drivrutinen och [här](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) för Java 3. x-vägledning se även [Konfigurera ReconnectionPolicy för Java-drivrutins](#configuring-reconnectionpolicy-for-java-driver) exempel nedan.

## <a name="error-with-load-balancing-policy"></a>Fel vid princip för belastnings utjämning

Om du har implementerat en princip för belastnings utjämning i v3. x av Java DataStax-drivrutinen, med kod som liknar följande:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Om värdet för inte `withLocalDc()` matchar kontakt punktens Data Center kan det uppstå ett mycket återkommande fel: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Lösning 
Implementera [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (du kan behöva uppgradera DataStax minor-version för att det ska fungera):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Count misslyckades i stor tabell
Vid körning `select count(*) from table` eller liknande för ett stort antal rader, tids gränsen för servern.

### <a name="solution"></a>Lösning 
Om du använder en lokal CQLSH-klient kan du försöka ändra `--connect-timeout` `--request-timeout` inställningarna eller (se mer information [här](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Om detta inte är tillräckligt och antalet fortfarande finns kvar, kan du få antalet poster från Azure Cosmos DB server dels telemetri genom att gå till fliken mått i Azure Portal, välja måttet och `document count` sedan lägga till ett filter för databasen eller samlingen (den analoga tabellen i Azure Cosmos dB). Du kan sedan hovra över det resulterande diagrammet för den tidpunkt då du vill att antalet poster ska räknas.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="vyn mått":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Konfigurera ReconnectionPolicy för Java-drivrutin

### <a name="version-3x"></a>Version 3. x

För version 3. x av Java-drivrutinen konfigurerar du åter anslutnings principen när du skapar ett kluster objekt:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Version 4. x

För version 4. x av Java-drivrutinen konfigurerar du åter anslutnings principen genom att åsidosätta inställningarna i `reference.conf` filen:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Aktivera Keep-Alive för Java-drivrutin

### <a name="version-3x"></a>Version 3. x

För version 3. x av Java-drivrutinen ställer du in Keep-Alive när du skapar ett kluster objekt och kontrollerar att Keep-Alive är [aktiverat i operativ systemet](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Version 4. x

För version 4. x av Java-drivrutinen ställer du in Keep-Alive genom att åsidosätta inställningarna i `reference.conf` och se till att Keep-Alive är [aktiverat i operativ systemet](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [funktioner som stöds](cassandra-support.md) i Azure Cosmos DB API för Cassandra.
- Lär dig hur du [migrerar från Native Apache Cassandra till Azure Cosmos DB API för Cassandra](cassandra-migrate-cosmos-db-databricks.md)
