---
title: Skala elastiskt med API för Cassandra i Azure Cosmos DB
description: Läs om tillgängliga alternativ för att skala ett Azure Cosmos DB API för Cassandra konto och deras fördelar/nack delar
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340965"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Skala ett Azure Cosmos DB API för Cassandra-konto elastiskt
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Det finns flera olika alternativ för att utforska den elastiska natur som Azure Cosmos DB-API: n för Cassandra. För att förstå hur du skalar effektivt i Azure Cosmos DB är det viktigt att förstå hur du etablerar rätt mängd enheter för programbegäran (RU/s) för att hantera prestanda kraven i systemet. Mer information om enheter för programbegäran finns i artikeln om [enheter för programbegäran](request-units.md) . 

För API för Cassandra kan du hämta avgiften för enhets begär Anden för enskilda frågor med hjälp av [.net-och Java-SDK](./find-request-unit-charge-cassandra.md): er. Detta är användbart när du ska bestämma hur många RU/s du behöver etablera i tjänsten.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databas åtgärder förbrukar enheter för programbegäran" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Begränsning av hanterings hastighet (429 fel)

Azure Cosmos DB Returnerar fel frekvensen (429) fel om klienterna förbrukar fler resurser (RU/s) än den mängd som du har etablerad. API för Cassandra i Azure Cosmos DB översätter dessa undantag till överbelastade fel på det inbyggda Cassandra-protokollet. 

Om systemet inte är känsligt för fördröjning kan det vara tillräckligt för att hantera data flödes hastigheten med hjälp av återförsök. Se [exempel på Java-kod](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) för hur du hanterar hastighets begränsning transparent med hjälp av [Azure Cosmos DB tillägget](https://github.com/Azure/azure-cosmos-cassandra-extensions) för [principen för återförsök i Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) i Java. Du kan också använda [Spark-tillägget](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) för att hantera hastighets begränsning.

## <a name="manage-scaling"></a>Hantera skalning

Om du behöver minimera svars tiden finns det ett spektrum av alternativ för hantering av skalnings-och etablerings data flöde (ru: er) i API för Cassandra:

* [Manuellt med hjälp av Azure Portal](#use-azure-portal)
* [Program mässigt med hjälp av funktionerna i kontroll planet](#use-control-plane)
* [Program mässigt med hjälp av CQL-kommandon med en angiven SDK](#use-cql-queries)
* [Dynamiskt med hjälp av autoskalning](#use-autoscale)

I följande avsnitt beskrivs fördelarna och nack delarna med varje metod. Du kan sedan välja den bästa strategin för att balansera systemets skalnings behov, den totala kostnaden och effektiviteten för din lösning.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Använd Azure Portal

Du kan skala resurserna i Azure Cosmos DB API för Cassandra konto genom att använda Azure Portal. Mer information finns i artikeln om att [etablera data flöde på behållare och databaser](set-throughput.md). I den här artikeln beskrivs de relativa fördelarna med att ställa in data flöde på antingen [databas](set-throughput.md#set-throughput-on-a-database) -eller [behållar](set-throughput.md#set-throughput-on-a-container) nivå i Azure Portal. Termerna "Database" och "container" som nämns i dessa artiklar mappar till "disk utrymme" och "Tabell" för API för Cassandra.

Fördelen med den här metoden är att det är ett enkelt sätt att hantera data flödes kapaciteten på-databasen. Nack delen är dock att det i många fall kan krävas att vissa nivåer av automatisering måste vara både kostnads effektiv och hög prestanda. I nästa avsnitt beskrivs relevanta scenarier och metoder.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Använd kontroll planet

Azure Cosmos DBens API för Cassandra ger möjlighet att justera genomflödet program mässigt med hjälp av våra olika kontroll Plans funktioner. Se artikeln [Azure Resource Manager](./templates-samples-cassandra.md), [POWERSHELL](powershell-samples.md)och [Azure CLI](cli-samples.md) för vägledning och exempel.

Fördelen med den här metoden är att du kan automatisera skala upp eller ned resurser baserat på en timer för att redovisa hög aktivitet eller perioder med låg aktivitet. Ta en titt på vårt exempel [här](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) för hur du kan göra detta med hjälp av Azure Functions och PowerShell.

En nackdel med den här metoden är att du inte kan svara på oförutsägbara skalnings behov i real tid. I stället kan du behöva använda program kontexten i systemet, på klient/SDK-nivå eller använda [autoskalning](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Använda CQL-frågor med en angiven SDK

Du kan skala systemet dynamiskt med kod genom att köra [CQL Alter-kommandon](cassandra-support.md#keyspace-and-table-options) för den aktuella databasen eller behållaren.

Fördelen med den här metoden är att du kan svara på skalnings behov dynamiskt och på ett anpassat sätt som passar ditt program. Med den här metoden kan du fortfarande utnyttja standardvärdena för RU/s. Om systemets skalnings behov främst förväntas (cirka 70% eller mer) kan du använda SDK med CQL för att få en mer kostnads effektiv metod för automatisk skalning än att använda autoskalning. Nack delen med den här metoden är att det kan vara ganska komplicerat att implementera återförsök medan hastighets begränsningen kan öka svars tiden.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Använd autoskalning av allokerat data flöde

Förutom standard (manuell) eller programmerings sätt för etablering av data flöde kan du också konfigurera Azure Cosmos-behållare i autoskalning av allokerat data flöde. Automatisk skalning kommer automatiskt att skalas efter förbruknings behoven inom de angivna RU-intervallen utan att kompromissa med service avtal. Mer information finns i avsnittet [Skapa Azure Cosmos-behållare och databaser i den automatiska skalnings](provision-throughput-autoscale.md) artikeln.

Fördelen med den här metoden är att det är det enklaste sättet att hantera skalnings behoven i systemet. Det kommer inte att tillämpa Rate-Limiting **inom de konfigurerade ru-intervallen**. Nack delen är att om skalnings behoven i systemet är förutsägbara kan autoskalning vara ett mindre kostnads effektivt sätt att hantera dina skalnings behov än att använda de beskrivna kontroll planet eller SDK-nivå närmast ovan.

Om du vill ställa in eller ändra Max genom strömning (ru: er) för autoskalning med CQL, använder du följande (Ersätt nyckel utrymme/tabell namn enligt detta):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program