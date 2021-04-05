---
title: Migrera data från PostgreSQL till Azure Cosmos DB API för Cassandra konto med Apache Kafka
description: Lär dig hur du använder Kafka Connect för att synkronisera data från PostgreSQL till Azure Cosmos DB API för Cassandra i real tid.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203073"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrera data från PostgreSQL till Azure Cosmos DB API för Cassandra konto med Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB har blivit ett bra val för företags arbets belastningar som körs på Apache Cassandra av olika anledningar, t. ex.:

* **Betydande kostnads besparingar:** Du kan spara kostnader med Azure Cosmos DB, vilket inkluderar kostnaden för VM, bandbredd och eventuella tillämpliga Oracle-licenser. Dessutom behöver du inte hantera data Center, servrar, SSD-lagring, nätverk och el kostnader.

* **Bättre skalbarhet och tillgänglighet:** Den eliminerar de enskilda felen, bättre skalbarhet och tillgänglighet för dina program.

* **Ingen omkostnader för hantering och övervakning:** Som en helt hanterad moln tjänst, Azure Cosmos DB ta bort behovet av att hantera och övervaka en myriaden av inställningar.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) är en plattform för strömning av data mellan [Apache Kafka](https://kafka.apache.org/) och andra system på ett skalbart och tillförlitligt sätt. Det stöder flera av de flesta hylla connectors, vilket innebär att du inte behöver anpassad kod för att integrera externa system med Apache Kafka.

Den här artikeln visar hur du använder en kombination av Kafka-anslutningar för att skapa en datapipeline för att kontinuerligt synkronisera poster från en Relations databas, till exempel [postgresql](https://www.postgresql.org/) , för att [Azure Cosmos DB API för Cassandra](cassandra-introduction.md).

## <a name="overview"></a>Översikt

Här följer en översikt över slutet till slut flödet som presenteras i den här artikeln.

Data i PostgreSQL-tabellen flyttas till Apache Kafka med hjälp av [Debezium postgresql-anslutningen](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), som är en Kafka Connect **Source** -anslutning. Infogningar, uppdateringar eller borttagningar i poster i PostgreSQL-tabellen registreras som `change data` händelser och skickas till Kafka-ämnen. [DataStax Apache Kafka Connector](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka Connect **Sink** Connector) utgör den andra delen av pipelinen. Den synkroniserar avsnittet Ändra data händelser från Kafka för att Azure Cosmos DB API för Cassandra tabeller.

> [!NOTE]
> Genom att använda vissa funktioner i DataStax-Apache Kafkas anslutningen kan vi skicka data till flera tabeller. I det här exemplet kommer anslutningen att hjälpa oss att bevara ändrings data poster till två Cassandra-tabeller som har stöd för olika krav för frågor.

## <a name="prerequisites"></a>Förutsättningar

* [Etablera ett Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account)
* [Använd cqlsh eller värdbaserad Shell för verifiering](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 eller senare
* [Docker](https://www.docker.com/) (valfritt)

## <a name="base-setup"></a>Grundläggande konfiguration

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Konfigurera PostgreSQL Database om du inte redan gjort det. 

Det kan vara en befintlig lokal databas eller så kan du [Ladda ned och installera en](https://www.postgresql.org/download/) på din lokala dator. Det är också möjligt att använda en [Docker-behållare](https://hub.docker.com/_/postgres).

Starta en behållare:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Anslut till din PostgreSQL-instans med hjälp av [`psql`](https://www.postgresql.org/docs/current/app-psql.html) klienten:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Skapa en tabell för att lagra exempel beställnings information:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Skapa Cassandra-och de tabeller som krävs för demonstrations programmet med hjälp av Azure Portal.

> [!NOTE]
> Använd samma tecken utrymme och tabell namn enligt nedan

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Installations Apache Kafka 

I den här artikeln används ett lokalt kluster, men du kan välja ett annat alternativ. [Ladda ned Kafka](https://kafka.apache.org/downloads), zippa upp den, starta Zookeeper och Kafka-klustret.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Installations anslutningar

Installera Debezium PostgreSQL och DataStax Apache Kafka Connector. Hämta plugin-arkivet Debezium PostgreSQL Connector. För att till exempel hämta version 1.3.0 av Connector (senaste vid tidpunkten för skrivning), använder du [den här länken](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Hämta DataStax Apache Kafka Connector från [den här länken](https://downloads.datastax.com/#akc).

Zippa upp båda kopplings arkiven och kopiera JAR-filerna till [Kafka Connect-plugin. Path](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Mer information finns i dokumentationen om [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) och [DataStax](https://docs.datastax.com/en/kafka/doc/) .

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Konfigurera Kafka Anslut och starta datapipeline

### <a name="start-kafka-connect-cluster"></a>Starta Kafka Connect-kluster

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Starta PostgreSQL Connector-instans

Spara anslutnings konfigurationen (JSON) till ett fil exempel `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Starta PostgreSQL Connector-instansen:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> För att ta bort kan du använda: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Infoga data

`orders_info`Tabellen innehåller order information, till exempel order-ID, kund-ID, stad osv. Fyll i tabellen med slumpmässiga data med hjälp av nedanstående SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Den ska infoga 10 poster i tabellen. Se till att uppdatera antalet poster i enlighet med `generate_series(1, 10)` krav exemplet för att infoga poster med hjälp av `100``generate_series(1, 100)`

Att bekräfta:

```bash
select * from retail.orders_info;
```

Kontrol lera händelserna för registrering av ändrings data i avsnittet Kafka

> [!NOTE]
> Observera att ämnes namnet är det som finns enligt `myserver.retail.orders_info` [anslutnings konventionen](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Du bör se avsnittet Ändra data händelser i JSON-format.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Starta DataStax Apache Kafka anslutnings instans

Spara anslutnings konfigurationen (JSON) till ett fil exempel `cassandra-sink-config.json` och uppdatera egenskaperna enligt din miljö.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Starta anslutnings instansen:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Anslutningen ska vara i drift och från slut punkt till slut punkt från PostgreSQL till Azure Cosmos DB kommer att fungera.

### <a name="query-azure-cosmos-db"></a>Köra frågor mot Azure Cosmos DB

Kontrol lera Cassandra-tabellerna i Azure Cosmos DB. Här följer några frågor som du kan prova:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Du kan fortsätta att infoga mer data i PostgreSQL och bekräfta att posterna är synkroniserade med Azure Cosmos DB.

## <a name="next-steps"></a>Nästa steg

* [Integrera Apache Kafka och Azure Cosmos DB API för Cassandra med Kafka Connect](cassandra-kafka-connect.md)
* [Integrera Apache Kafka ansluta på Azure Event Hubs (för hands version) med Debezium för registrering av ändrings data](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migrera data från Oracle till Azure Cosmos DB API för Cassandra med Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna ru/s med Azure Cosmos DB kapacitets planerings](estimate-ru-with-capacity-planner.md) artiklar

