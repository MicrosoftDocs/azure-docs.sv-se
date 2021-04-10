---
title: Integrera Apache Kafka ansluta på Azure Event Hubs med Debezium för registrering av ändrings data
description: Den här artikeln innehåller information om hur du använder Debezium med Azure Event Hubs för Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935180"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Integrera Apache Kafka Connect-support på Azure Event Hubs med Debezium för registrering av ändrings data

**Registrering av ändrings data (CDC)** är en teknik som används för att spåra ändringar på rad nivå i databas tabeller som svar på åtgärder för att skapa, uppdatera och ta bort. [Debezium](https://debezium.io/) är en distribuerad plattform som bygger på funktionerna för insamling av ändrings data i olika databaser (till exempel [logisk avkodning i postgresql](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Den innehåller en uppsättning [Kafka Connect-kopplingar](https://debezium.io/documentation/reference/1.2/connectors/index.html) som används för ändringar på radnivå i databas tabeller och konverterar dem till händelse strömmar som sedan skickas till [Apache Kafka](https://kafka.apache.org/).

> [!WARNING]
> Användning av Apache Kafka Connect Framework och Debezium-plattformen och dess anslutningar är **inte berättigade till produkt support via Microsoft Azure**.
>
> Apache Kafka Connect antar att den dynamiska konfigurationen lagras i komprimerade ämnen med annars obegränsad kvarhållning. Azure Event Hubs [implementerar inte komprimering som en Service Broker-funktion](event-hubs-federation-overview.md#log-projections) och bevarar alltid en tidsbaserad gräns för kvarhållning av händelser, med roten från principen att Azure Event Hubs är en real tids händelse strömnings motor och inte en långsiktig data eller konfigurations lager.
>
> Även om det Apache Kafka projektet kan vara bekvämt att blanda dessa roller, tror Azure att sådan information bäst hanteras i en lämplig databas eller konfigurations lagring.
>
> Många Apache Kafka Connect-scenarier fungerar, men de här konceptuella skillnaderna mellan Apache Kafkas och Azures Event Hubss bevarande modeller kan orsaka att vissa konfigurationer inte fungerar som förväntat. 

Den här självstudien vägleder dig genom hur du konfigurerar ett samlat system för ändrings data i Azure med hjälp av [azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (för Kafka), [Azure dB för postgresql](../postgresql/overview.md) och Debezium. Den använder [Debezium postgresql-anslutningen](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) för att strömma databas ändringar från postgresql till Kafka ämnen i Azure Event Hubs

> [!NOTE]
> Den här artikeln innehåller referenser till termen *vitlista*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Konfigurera och konfigurera Azure Database for PostgreSQL
> * Konfigurera och kör Kafka Connect med Debezium PostgreSQL Connector
> * Registrering av test ändrings data
> * Valfritt Använda ändrings data händelser med en `FileStreamSink` koppling

## <a name="pre-requisites"></a>Förutsättningar
För att slutföra den här genom gången behöver du:

- En Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Linux/MacOS
- Kafka-version (version 1.1.1, Scala-version 2.11), som finns på [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Läs introduktionsartikeln [Event Hubs för Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner för att skapa ett namn område och en Event Hub finns i [skapa en Event Hub](event-hubs-create.md) . Hämta Event Hubs-anslutningssträngen och fullständigt domännamn (FQDN) för senare användning. Anvisningar finns i avsnittet om att [hämta en Event Hubs-anslutningssträng](event-hubs-get-connection-string.md). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Konfigurera och konfigurera Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md) är en Relations databas tjänst baserad på community-versionen av postgresql-databasmotorn med öppen källkod och är tillgänglig i två distributions alternativ: Single Server och Scale (citus). [Följ dessa anvisningar](../postgresql/quickstart-create-server-database-portal.md) för att skapa en Azure Database for postgresql-server med hjälp av Azure Portal. 

## <a name="setup-and-run-kafka-connect"></a>Installera och kör Kafka Connect
I det här avsnittet behandlas följande ämnen:

- Installation av Debezium-anslutning
- Konfigurerar Kafka Connect för Event Hubs
- Starta Kafka Connect Cluster with Debezium Connector

### <a name="download-and-setup-debezium-connector"></a>Hämta och konfigurera Debezium Connector
Följ de senaste anvisningarna i Debezium- [dokumentationen](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) för att ladda ned och konfigurera anslutningen.

- Hämta kopplingens plugin-arkiv. Om du till exempel vill hämta versionen `1.2.0` av anslutningen använder du den här länken- https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Extrahera JAR-filerna och kopiera dem till [Kafka Connect-plugin. Path](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurera Kafka Connect för Event Hubs
Minimal omkonfiguration krävs vid omdirigering av Kafka Connect-dataflöde från Kafka till Event Hubs.  Följande exempel med `connect-distributed.properties` illustrerar hur du konfigurerar Connect för att autentisera och kommunicera med Kafka-slutpunkten i Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Ersätt `{YOUR.EVENTHUBS.CONNECTION.STRING}` med anslutnings strängen för din Event Hubs-namnrymd. Anvisningar om hur du hämtar anslutnings strängen finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md). Här är ett exempel på en konfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Köra Kafka Connect
I det här steget startas en Kafka Connect-arbetare lokalt i distribuerat läge med hjälp av Event Hubs för att upprätthålla klustertillståndet.

1. Spara filen `connect-distributed.properties` ovan lokalt.  Se till att ersätta alla värden inom klammerparenteser.
2. Navigera till platsen för Kafka-versionen på datorn.
3. Kör `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` och vänta tills klustret har startats.

> [!NOTE]
> Kafka Connect använder Kafka AdminClient API för att automatiskt skapa ämnen med rekommenderade konfigurationer, inklusive komprimering. En snabb kontroll av namnrymden i Azure-portalen visar att Connect-arbetarens interna ämnen har skapats automatiskt.
>
> Kafka Connect, interna ämnen **måste använda komprimering**.  Event Hubs-teamet ansvarar inte för att åtgärda Felaktiga konfigurationer om interna anslutnings ämnen är felaktigt konfigurerade.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Konfigurera och starta Debezium PostgreSQL source Connector

Skapa en konfigurations fil ( `pg-source-connector.json` ) för postgresql source Connector – ersätt värdena med Azure postgresql-instansen.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` Attribute är ett logiskt namn som identifierar och tillhandahåller ett namn område för den specifika PostgreSQL-databas server/det kluster som ska övervakas. Detaljerad information finns i [Debezium-dokumentationen](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

Om du vill skapa en instans av anslutningen använder du Kafka Connect REST API-slut punkten:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Så här kontrollerar du anslutnings status:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Registrering av test ändrings data
Om du vill se insamling av ändrings data i praktiken måste du skapa/uppdatera/ta bort poster i Azure PostgreSQL-databasen.

Börja med att ansluta till din Azure PostgreSQL-databas (exemplet nedan använder [psql](https://www.postgresql.org/docs/12/app-psql.html))

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Skapa en tabell och infoga poster**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Anslutningen bör nu ha till gång till åtgärder och skicka ändrings data händelser till ett Event Hubs ämne med följande na, e `my-server.public.todos` , förutsatt att du har `my-server` som värde för `database.server.name` och `public.todos` är den tabell vars ändringar du spårar (enligt `table.whitelist` konfigurationen)

**Kontrol lera Event Hubs ämne**

Låt oss Introspect innehållet i ämnet för att se till att allt fungerar som förväntat. Exemplet nedan använder [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , men du kan också [skapa en konsument med något av de alternativ som anges här](apache-kafka-developer-guide.md)

Skapa en fil med namnet `kafkacat.conf` med följande innehåll:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Uppdatera `metadata.broker.list` och `sasl.password` attribut i `kafkacat.conf` per Event Hubs information. 

Starta en konsument i en annan terminal:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Du bör se JSON-nyttolasterna som representerar de ändrings data händelser som genererats i PostgreSQL som svar på de rader som du precis har lagt till i `todos` tabellen. Här är ett utdrag av nytto lasten:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Händelsen består av `payload` tillsammans med dess `schema` (utelämnas för det kortfattat). I `payload` avsnittet ser du hur åtgärden Skapa fungerar ( `"op": "c"` ) `"before": null` innebär att den var en `INSERT` Nyed-rad, `after` ger värden för kolumnerna i raden och `source` ger postgresql-instansens metadata från där den här händelsen hämtades osv.

Du kan testa samma uppdaterings-eller borttagnings åtgärder även och Introspect för att ändra data. Om du till exempel vill uppdatera aktivitets statusen för `configure and install connector` (förutsatt att dess `id` är `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Valfritt Installera FileStreamSink Connector
Nu när alla `todos` tabell ändringar registreras i Event Hubs avsnittet använder vi FileStreamSink-anslutningen (som är tillgänglig som standard i Kafka Connect) för att använda dessa händelser.

Skapa en konfigurations fil ( `file-sink-connector.json` ) för kopplingen – Ersätt `file` attributet med fil systemet

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Så här skapar du anslutningen och kontrollerar dess status:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Infoga/uppdatera/ta bort databas poster och övervaka posterna i den konfigurerade Sink-filen för utdata:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Rensa
Kafka Connect skapar Event Hub-ämnen för att lagra konfigurationer, förskjutningar och status som består även när Connect-klustret har stängts. Såvida du inte vill att de ska bestå rekommenderas det att dessa ämnen tas bort. Du kanske också vill ta bort `my-server.public.todos` händelsehubben som skapades under den här genom gången.

## <a name="next-steps"></a>Nästa steg

Mer information om Event Hubs för Kafka finns i följande artiklar:  

- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Developer Guide för Azure Event Hubs](apache-kafka-developer-guide.md)