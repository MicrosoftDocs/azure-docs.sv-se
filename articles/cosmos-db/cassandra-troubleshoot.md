---
title: Felsök vanliga fel i Azure Cosmos DB API för Cassandra
description: I den här artikeln beskrivs vanliga problem i Azure Cosmos DB API för Cassandra och hur du felsöker dem.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967361"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Felsök vanliga problem i Azure Cosmos DB API för Cassandra

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i [Azure Cosmos DB](./introduction.md) är ett kompatibilitetsläge som ger stöd för [Wire-protokoll](cassandra-support.md) för Apache Cassandra-databasen med öppen källkod.

I den här artikeln beskrivs vanliga fel och lösningar för program som använder Azure Cosmos DB API för Cassandra. Om felet inte finns med i listan och du får ett fel meddelande när du kör en [åtgärd som stöds i Cassandra](cassandra-support.md), men felet inte finns när du använder Native Apache Cassandra, [skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Som en helt hanterad molnbaserad tjänst Azure Cosmos DB ger [garantier avseende tillgänglighet, data flöde och konsekvens](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) för API för Cassandra. API för Cassandra fören klar även driften av noll-underhåll och korrigering av inga stillestånds tider.
>
>Dessa garantier är inte möjliga i tidigare implementeringar av Apache Cassandra, så många av API för Cassandra Server dels åtgärder skiljer sig från Apache Cassandra. Vi rekommenderar särskilda inställningar och metoder för att undvika vanliga fel.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Det här felet är ett omslutnings undantag på översta nivån med ett stort antal möjliga orsaker och inre undantag, som många kan vara klient relaterade till.

Vanliga orsaker och lösningar:

- **Tids gräns för inaktivitet i Azure belastningsutjämnare**: det här problemet kan även manifesta som `ClosedConnectionException` . Lös problemet genom att ange inställningen för Keep-Alive i driv rutinen (se [Aktivera Keep-Alive för Java-drivrutinen](#enable-keep-alive-for-the-java-driver)) och öka inställningarna för Keep-Alive i operativ systemet, eller [ändra inaktiv tids gräns i Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Belastning för klient program resurs**: kontrol lera att klient datorerna har tillräckligt med resurser för att slutföra begäran.

## <a name="cant-connect-to-a-host"></a>Det går inte att ansluta till en värd

Du kanske ser det här felet: "det går inte att ansluta till någon värd, schemaläggnings försök igen om 600000 millisekunder."

Det här felet kan bero på att käll Network Address Translation (SNAT) är på klient sidan. Följ stegen i [SNAT för utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) för att utesluta det här problemet.

Felet kan också vara ett problem med tids gräns för inaktivitet där Azure Load Balancer har fyra minuters inaktiv tids gräns som standard. Se [inaktiv timeout för belastningsutjämnare](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Aktivera Keep-Alive för Java-drivrutinen](#enable-keep-alive-for-the-java-driver) och ange `keepAlive` intervallet för operativ systemet till mindre än fyra minuter.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Förfrågningarna är begränsade eftersom det totala antalet förbrukade enheter är högre än antalet enheter för programbegäran som du har allokerat i tecken området eller tabellen.

Överväg att skala data flödet som är kopplat till ett nyckel utrymme eller en tabell från Azure Portal (se [elastiskt skala ett Azure Cosmos DB API för Cassandra-konto](manage-scale-cassandra.md)) eller implementera en princip för återförsök.

För Java, se exempel på nya försök för [driv rutinen v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) och [v4. x-drivrutinen](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Se även [Azure Cosmos Cassandra-tillägg för Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException trots tillräckligt med data flöde

Systemet verkar vara begränsnings begär Anden även om tillräckligt med data flöde har allokerats för begär ande volym eller förbrukad enhets kostnad. Det finns två möjliga orsaker:

- **Schema nivå åtgärder**: API för Cassandra implementerar en system data flödes budget för åtgärder på schema nivå (CREATE TABLE, Alter Table, Drop Table). Den här budgeten bör vara tillräckligt för schema åtgärder i ett produktions system. Men om du har ett stort antal åtgärder på schema nivå kan du överskrida den här gränsen.

  Eftersom budgeten inte är användardefinierad bör du överväga att minska antalet schema åtgärder som du kör. Om den åtgärden inte löser problemet, eller om det inte är möjligt för din arbets belastning, [skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Data skevning**: när data flödet har tillhandahållits i API för Cassandra, delas det jämnt mellan fysiska partitioner och varje fysisk partition har en övre gräns. Om du har en stor mängd data som infogas eller efter frågas från en viss partition kan det vara en hastighets begränsning även om du etablerar en stor mängd av det totala data flödet (enheter för programbegäran) för tabellen.

  Granska din data modell och se till att du inte har överdriven skevning som kan orsaka varma partitioner.

## <a name="intermittent-connectivity-errors-java"></a>Tillfälligt anslutnings fel (Java)

Anslutningen tappas bort eller nådde tids gränsen.

Apache Cassandra-drivrutinerna för Java innehåller två principer för intern åter anslutning: `ExponentialReconnectionPolicy` och `ConstantReconnectionPolicy` . Standardvärdet är `ExponentialReconnectionPolicy`. Men för Azure Cosmos DB API för Cassandra rekommenderar vi att du `ConstantReconnectionPolicy` har en fördröjning på två sekunder.

I [dokumentationen för Java 4. x-drivrutinen](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), [dokumentationen för Java 3. x-drivrutinen](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)eller konfigurering av [ReconnectionPolicy för Java-drivrutin](#configure-reconnectionpolicy-for-the-java-driver) exempel.

## <a name="error-with-load-balancing-policy"></a>Fel vid princip för belastnings utjämning

Du kanske har implementerat en princip för belastnings utjämning i v3. x av Java DataStax-drivrutinen med kod som liknar:

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

Om värdet för `withLocalDc()` inte matchar kontakt punktens Data Center kan ett tillfälligt fel uppstå: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Implementera [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). För att det ska fungera kan du behöva uppgradera DataStax med hjälp av följande kod:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Antalet kan inte utföras i en stor tabell

När du kör `select count(*) from table` eller liknar ett stort antal rader, tids gränsen för servern.

Om du använder en lokal CQLSH-klient ändrar du `--connect-timeout` inställningarna eller `--request-timeout` . Se [cqlsh: CQL Shell](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Om antalet fortfarande är timeout kan du få antalet poster från Azure Cosmos DB backend-telemetri genom att gå till fliken mått i Azure Portal, välja måttet `document count` och sedan lägga till ett filter för databasen eller samlingen (den analoga tabellen i Azure Cosmos dB). Du kan sedan hovra över det resulterande diagrammet för den tidpunkt då du vill att antalet poster ska räknas.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="vyn mått":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Konfigurera ReconnectionPolicy för Java-drivrutinen

### <a name="version-3x"></a>Version 3. x

För version 3. x av Java-drivrutinen konfigurerar du återkopplings principen när du skapar ett kluster objekt:

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

## <a name="enable-keep-alive-for-the-java-driver"></a>Aktivera Keep-Alive för Java-drivrutinen

### <a name="version-3x"></a>Version 3. x

För version 3. x av Java-drivrutinen ställer du in Keep-Alive när du skapar ett kluster objekt och kontrollerar sedan att Keep-Alive är [aktiverat i operativ systemet](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

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

För version 4. x av Java-drivrutinen ställer du in Keep-Alive genom att åsidosätta inställningarna i `reference.conf` och kontrollerar sedan att Keep-Alive är [aktiverat i operativ systemet](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [funktioner som stöds](cassandra-support.md) i Azure Cosmos DB API för Cassandra.
- Lär dig hur du [migrerar från interna Apache-Cassandra till Azure Cosmos DB API för Cassandra](cassandra-migrate-cosmos-db-databricks.md).
