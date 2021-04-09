---
title: Integrera med Apache Kafka Connect – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Kafka Connect med Azure Event Hubs för Kafka.
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935163"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Integrera stöd för Apache Kafka Connect i Azure Event Hubs
[Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) är ett ramverk för att ansluta och importera/exportera data från/till ett externt system som MySQL, HDFS och File System via ett Kafka-kluster. Den här självstudien vägleder dig genom att använda Kafka Connect Framework med Event Hubs.

> [!WARNING]
> Användning av Apache Kafka Connect Framework och dess kopplingar är **inte berättigade till produkt support via Microsoft Azure**.
>
> Apache Kafka Connect antar att den dynamiska konfigurationen lagras i komprimerade ämnen med annars obegränsad kvarhållning. Azure Event Hubs [implementerar inte komprimering som en Service Broker-funktion](event-hubs-federation-overview.md#log-projections) och bevarar alltid en tidsbaserad gräns för kvarhållning av händelser, med roten från principen att Azure Event Hubs är en real tids händelse strömnings motor och inte en långsiktig data eller konfigurations lager.
>
> Även om det Apache Kafka projektet kan vara bekvämt att blanda dessa roller, tror Azure att sådan information bäst hanteras i en lämplig databas eller konfigurations lagring.
>
> Många Apache Kafka Connect-scenarier fungerar, men de här konceptuella skillnaderna mellan Apache Kafkas och Azures Event Hubss bevarande modeller kan orsaka att vissa konfigurationer inte fungerar som förväntat. 

Den här självstudien vägleder dig genom integreringen av Kafka Connect med en Event Hub och distribuerar grundläggande FileStreamSource-och FileStreamSink-anslutningar. Den här funktionen finns för närvarande som en förhandsversion. De här anslutningsapparna är inte avsedda för produktionsanvändning, men de demonstrerar ett Kafka Connect-scenario med slutpunkt till slutpunkt där Azure Event Hubs fungerar som asynkron Kafka-meddelandekö.

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera Kafka Connect för Event Hubs
> * Köra Kafka Connect
> * Skapa anslutningsappar

## <a name="prerequisites"></a>Förutsättningar
För att kunna slutföra den här genomgången behöver du följande:

- En Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka-version (version 1.1.1, Scala-version 2.11), som finns på [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Läs introduktionsartikeln [Event Hubs för Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner för att skapa ett namn område och en Event Hub finns i [skapa en Event Hub](event-hubs-create.md) . Hämta Event Hubs-anslutningssträngen och fullständigt domännamn (FQDN) för senare användning. Anvisningar finns i avsnittet om att [hämta en Event Hubs-anslutningssträng](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Klona exempelprojektet
Klona Azure Event Hubs-lagringsplatsen och navigera till undermappen tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurera Kafka Connect för Event Hubs
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


## <a name="run-kafka-connect"></a>Köra Kafka Connect

I det här steget startas en Kafka Connect-arbetare lokalt i distribuerat läge med hjälp av Event Hubs för att upprätthålla klustertillståndet.

1. Spara filen `connect-distributed.properties` ovan lokalt.  Se till att ersätta alla värden inom klammerparenteser.
2. Navigera till platsen för Kafka-versionen på datorn.
4. Kör `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  REST API för Connect-arbetaren är redo för interaktion när du ser `'INFO Finished starting connectors and tasks'`. 

> [!NOTE]
> Kafka Connect använder Kafka AdminClient API för att automatiskt skapa ämnen med rekommenderade konfigurationer, inklusive komprimering. En snabb kontroll av namnrymden i Azure-portalen visar att Connect-arbetarens interna ämnen har skapats automatiskt.
>
>Kafka Connect, interna ämnen **måste använda komprimering**.  Event Hubs-teamet ansvarar inte för att åtgärda Felaktiga konfigurationer om interna anslutnings ämnen är felaktigt konfigurerade.

### <a name="create-connectors"></a>Skapa anslutningsappar
Det här avsnittet vägleder dig genom att skapa anslutningsapparna FileStreamSource och FileStreamSink. 

1. Skapa en katalog för indata- och utdatafiler.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Skapa två filer: en fil med startdata som anslutningsappen FileStreamSource läser från och en annan som anslutningsappen FileStreamSink skriver till.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Skapa en FileStreamSource-anslutningsapp.  Se till att ersätta klamrarna med din startkatalogsökväg.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Du bör se Event Hub `connect-quickstart` på Event Hubs-instansen när du har kört kommandot ovan.
4. Kontrollera statusen för källanslutningsappen.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Du kan även välja att använda [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) för att verifiera att händelserna ankommer till ämnet `connect-quickstart`.

5. Skapa en FileStreamSink-anslutningsapp.  Se även denna gång till att ersätta klamrarna med din startkatalogsökväg.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Kontrollera statusen för kanalmottagarens anslutningsapp.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Kontrollera att data har replikerats mellan filer och att data är identiska i båda filerna.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Rensa
Kafka Connect skapar Event Hub-ämnen för att lagra konfigurationer, förskjutningar och status som består även när Connect-klustret har stängts. Såvida du inte vill att de ska bestå rekommenderas det att dessa ämnen tas bort. Du kan även ta bort händelsehubben `connect-quickstart`, som skapades under loppet av den här genomgången.

## <a name="next-steps"></a>Nästa steg

Mer information om Event Hubs för Kafka finns i följande artiklar:  

- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Developer Guide för Azure Event Hubs](apache-kafka-developer-guide.md)
