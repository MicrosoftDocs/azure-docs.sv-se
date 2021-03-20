---
title: Integrera Apache Kafka och Azure Cosmos DB API för Cassandra med Kafka Connect
description: Lär dig hur du matar in data från Kafka till Azure Cosmos DB API för Cassandra med DataStax Apache Kafka Connector
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803637"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Mata in data från Apache Kafka i Azure Cosmos DB API för Cassandra med Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Befintliga Cassandra-program kan enkelt fungera med [Azure Cosmos DB API för Cassandra](cassandra-introduction.md) på grund av dess [CQLv4-drivrutin](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Du kan utnyttja den här funktionen för att integrera med strömmande plattformar, till exempel [Apache Kafka](https://kafka.apache.org/) och hämta data till Azure Cosmos dB.

Data i Apache Kafka (ämnen) är bara användbara när de används av andra program eller matas in i andra system. Det är möjligt att bygga en lösning med [Kafka-tillverkare/konsument-](https://kafka.apache.org/documentation/#api) API: er [med hjälp av ett språk-och klient-SDK som du själv väljer](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect tillhandahåller en alternativ lösning. Det är en plattform för att strömma data mellan Apache Kafka och andra system på ett skalbart och tillförlitligt sätt. Eftersom Kafka Connect har stöd för de hylla-kopplingar som innehåller Cassandra, behöver du inte skriva anpassad kod för att integrera Kafka med Azure Cosmos DB API för Cassandra. 

I den här artikeln kommer vi att använda [Apache Kafka DataStax-anslutningen](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)med öppen källkod som fungerar ovanpå Kafka Connect Framework för att mata in poster från ett Kafka ämne i rader i en eller flera Cassandra-tabeller. Exemplet innehåller en återanvändbar installation med Docker Compose. Detta är mycket användbart eftersom det gör att du kan starta alla nödvändiga komponenter lokalt med ett enda kommando. Dessa komponenter omfattar Kafka, Zookeeper, Kafka Connect Worker och Sample data Generator-programmet.

Här är en uppdelning av komponenterna och deras tjänst definitioner – du kan referera till den fullständiga `docker-compose` filen [i GitHub-lagrings platsen](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka och Zookeeper använder [debezium](https://hub.docker.com/r/debezium/kafka/) -avbildningar.
- För att köra som en Docker-behållare är DataStax Apache Kafka Connector bakade ovanpå en befintlig Docker-avbildning – [debezium/Connect-Base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Den här avbildningen innehåller en installation av Kafka och dess Kafka Connect-bibliotek, vilket gör det enkelt att lägga till anpassade anslutningar. Du kan referera till [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- `data-generator`Tjänsten dirigerar slumpmässigt genererade JSON-data till `weather-data` Kafka-avsnittet. Du kan referera till koden och `Dockerfile` i [GitHub-lagrings platsen](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Förutsättningar

* [Etablera ett Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account)

* [Använd cqlsh eller värdbaserad Shell för verifiering](cassandra-support.md#hosted-cql-shell-preview)

* Installera [Docker](https://docs.docker.com/get-docker/) och [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Skapa ett blank steg, tabeller och starta integrerings pipelinen

Skapa Cassandra-och de tabeller som krävs för demonstrations programmet med hjälp av Azure Portal.

> [!NOTE]
> Använd samma tecken utrymme och tabell namn enligt nedan

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Klona GitHub-lagrings platsen:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Starta alla tjänster:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Det kan ta en stund att ladda ned och starta behållarna: det här är bara en gång.

Så här kontrollerar du om alla behållare har startat:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

Data Generator applikationen kommer att börja pumpa data i `weather-data` avsnittet i Kafka. Du kan också göra en snabb Sanity-kontroll för att bekräfta. Titta på Docker-behållaren som kör Kafka Connect Worker:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

När du har lagt till behållar gränssnittet startar du den vanliga Kafka-konsolen och du bör se väder data (i JSON-format) som flödar in.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Installation av Cassandra Sink Connector

Kopiera JSON-innehållet nedan till en fil (du kan ge den namnet `cassandra-sink-config.json` ). Du måste uppdatera den enligt konfigurationen och resten av det här avsnittet innehåller vägledning kring det här avsnittet.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Här är en sammanfattning av attributen:

**Grundläggande anslutning**

- `contactPoints`: Ange kontakt punkten för Cosmos DB Cassandra
- `loadBalancing.localDc`: Ange region för Cosmos DB konto, t. ex. Sydostasien
- `auth.username`: Ange användar namnet
- `auth.password`: Ange lösen ordet
- `port`: Ange port värde (detta är `10350` inte `9042` . lämna det som det är)

**SSL-konfiguration**

Azure Cosmos DB tillämpar [säker anslutning via SSL](database-security.md) och Kafka Connect Connector stöder även SSL.

- `ssl.keystore.path`: sökväg till JDK-nyckel arkivet i behållaren- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: Lösen ord för JDK-nyckel arkiv (standard)
- `ssl.hostnameValidation`: Vi omvandlar en egen nod med värd namns verifiering
- `ssl.provider`: `JDK` används som SSL-Provider

**Generiska parametrar**

- `key.converter`: Vi använder sträng konverteraren `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: eftersom data i Kafka ämnen är JSON, använder vi `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Eftersom vår JSON-nyttolast saknar ett schema som är associerat med det (för demonstrations appen) måste vi instruera Kafka Connect till att inte söka efter ett schema genom att ange det här attributet till `false` . Om du inte gör det leder det till att det Miss lyckas.

### <a name="install-the-connector"></a>Installera anslutningen

Installera anslutnings tjänsten med hjälp av Kafka Connect REST-slutpunkten:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Så här kontrollerar du status:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Om allt går bra bör kopplingen börja väv sitt magiska. Den ska autentisera till Azure Cosmos DB och börja mata in data från Kafka-avsnittet ( `weather-data` ) till Cassandra-tabeller – `weather.data_by_state` och `weather.data_by_station`

Nu kan du fråga efter data i tabellerna. Gå till Azure Portal och ta fram det värdbaserade CQL-gränssnittet för ditt Azure Cosmos DB-konto.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Öppna CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Fråga efter data från Azure Cosmos DB

Kontrol lera `data_by_state` `data_by_station` tabellerna och. Här följer några exempel frågor som hjälper dig att komma igång:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna ru/s med Azure Cosmos DB kapacitets planerings](estimate-ru-with-capacity-planner.md) artiklar
