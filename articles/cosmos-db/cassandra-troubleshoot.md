---
title: Felsök vanliga fel i Azure Cosmos DB API för Cassandra
description: Det här dokumentet innehåller information om hur du felsöker vanliga problem som uppstår i Azure Cosmos DB API för Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 75ce307056d24f84e9f72f746eb77b09b89e21b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927503"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Felsök vanliga problem i Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB är ett kompatibilitetsläge som tillhandahåller stöd för [Wire-protokoll](cassandra-support.md) för den populära Apache Cassandra-databasen med öppen källkod och drivs av [Azure Cosmos DB](./introduction.md). Som en helt hanterad molnbaserad tjänst Azure Cosmos DB ger [garantier avseende tillgänglighet, data flöde och konsekvens](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) för API för Cassandra. Dessa garantier är inte möjliga i äldre implementeringar av Apache Cassandra. API för Cassandra fören klar också plattforms åtgärder utan drift, och uppdatering utan avbrott. Därför skiljer sig många av IT-backend-åtgärder från Apache Cassandra, så vi rekommenderar särskilda inställningar och metoder för att undvika vanliga fel. 

I den här artikeln beskrivs vanliga fel och lösningar för program som konsumerar Azure Cosmos DB API för Cassandra.

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

| Fel               |  Beskrivning             | Lösning  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | Det totala antalet förbrukade enheter för förbrukad enhet är fler än de begärda enheterna som har allokerats på tecken utrymmet eller tabellen. Så att förfrågningarna är begränsade. | Överväg att skala data flödet som är kopplat till ett tecken utrymme eller en tabell från Azure Portal (se [här](manage-scale-cassandra.md) för skalnings åtgärder i API för Cassandra) eller så kan du implementera en princip för återförsök. För Java, se exempel på nya försök för [v3. x driv rutin](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) och [v4. x-drivrutin](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Se även [Azure Cosmos Cassandra-tillägg för Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| OverloadedException (Java) även med tillräckligt data flöde | Systemet förefaller vara begränsnings begär Anden trots att tillräckligt med data flöde har allokerats för begär ande volym och/eller förbrukad enhets kostnad för begäran  | API för Cassandra implementerar system data flödes budgeten för åtgärder på schema nivå (CREATE TABLE, ALTER TABLE, DROP TABLE). Den här budgeten bör vara tillräckligt för schema åtgärder i ett produktions system. Men om du har ett stort antal åtgärder på schema nivå är det möjligt att du överskrider den här gränsen. Eftersom den här budgeten inte är inloggad av användaren, måste du minska antalet schema åtgärder som ska köras. Om den här åtgärden inte löser problemet, eller om det inte är möjligt för din arbets belastning, kan du [skapa en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Efter en tid av inaktivitet efter lyckade anslutningar kan programmet inte ansluta| Det här felet kan bero på en tids gräns för inaktivitet i Azure belastningsutjämnare, vilket är 4 minuter. Ange Keep Alive-inställningen i driv rutinen (se nedan) och öka inställningarna för Keep-Alive i operativ systemet, eller [Justera inaktiv tids gräns i Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Andra tillfälliga anslutnings fel (Java) | Anslutningen tappas bort eller nådde tids gränsen | Apache Cassandra-drivrutinerna för Java innehåller två principer för intern åter anslutning: `ExponentialReconnectionPolicy` och `ConstantReconnectionPolicy` . Standardvärdet är `ExponentialReconnectionPolicy`. Men för Azure Cosmos DB API för Cassandra rekommenderar vi att du `ConstantReconnectionPolicy` har en fördröjning på 2 sekunder. Se [dokumentationen för driv rutiner](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  för Java v4. x-drivrutin och [här](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) för Java 3. x-vägledning (se även exemplen nedan).|

Om felet inte finns med i listan ovan och du får ett fel meddelande när du kör en [åtgärd som stöds i API för Cassandra](cassandra-support.md), där felet *inte finns när du använder ursprunglig Apache-Cassandra*, skapar du [en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)

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