---
title: Partitionering i Azure Cosmos DB API för Cassandra
description: Lär dig mer om partitionering i Azure Cosmos DB API för Cassandra
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087646"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Partitionering i Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Den här artikeln beskriver hur partitionering fungerar i Azure Cosmos DB API för Cassandra. 

API för Cassandra använder partitionering för att skala enskilda tabeller i ett nyckel utrymme för att uppfylla ditt programs prestanda behov. Partitioner skapas baserat på värdet för en partitionsnyckel som är kopplad till varje post i en tabell. Alla poster i en partition har samma partitionerings nyckel värde. Azure Cosmos DB transparent och automatiskt hanterar placeringen av partitioner över de fysiska resurserna för att effektivt uppfylla tabellens skalbarhet och prestanda behov. När data flödes-och lagrings kraven för en applikation ökar, Azure Cosmos DB flyttar och balanserar data över ett större antal fysiska datorer.

I utvecklings perspektivet fungerar partitionering på samma sätt för Azure Cosmos DB API för Cassandra som i interna [Apache-Cassandra](https://cassandra.apache.org/). Det finns dock vissa skillnader bakom bakgrunden. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Skillnader mellan Apache Cassandra och Azure Cosmos DB

I Azure Cosmos DB kallas alla datorer där partitioner lagras som en [fysisk partition](partitioning-overview.md#physical-partitions). Den fysiska partitionen är via till en virtuell dator. en dedikerad beräknings enhet eller uppsättning fysiska resurser. Varje partition som lagras i den här beräknings enheten kallas en [logisk partition](partitioning-overview.md#logical-partitions) i Azure Cosmos dB. Om du redan är bekant med Apache Cassandra kan du tänka på logiska partitioner på samma sätt som du tänker på vanliga partitioner i Cassandra. 

Apache Cassandra rekommenderar en gräns på 100 MB för storleken på data som kan lagras i en partition. API för Cassandra för Azure Cosmos DB tillåter upp till 20 GB per logisk partition och upp till 30 GB data per fysisk partition. I Azure Cosmos DB, till skillnad från Apache-Cassandra, uttrycks beräknings kapaciteten som är tillgänglig i den fysiska partitionen med hjälp av ett enda mått som kallas [begär ande enheter](request-units.md), vilket gör att du kan tänka på arbets belastningen i fråga om begär Anden (läsningar eller skrivningar) per sekund, i stället för kärnor, minne eller IOPS. Detta kan göra kapacitets planeringen mer direkt framåt när du förstår kostnaden för varje begäran. Varje fysisk partition kan ha upp till 10000 ru: er beräknings utrymme. Du kan lära dig mer om skalbarhets alternativ genom att läsa vår artikel om [elastisk skalning](manage-scale-cassandra.md) i API för Cassandra. 

I Azure Cosmos DB består varje fysisk partition av en uppsättning repliker, även kallade replik uppsättningar, med minst fyra repliker per partition. Detta är i motsats till Apache Cassandra, där det är möjligt att ange en replikeringsrelation på 1. Detta leder dock till låg tillgänglighet om den enda noden med data slutar fungera. I API för Cassandra finns det alltid en replikeringsrelation på 4 (kvorum 3). Azure Cosmos DB hanterar automatiskt replik uppsättningar, medan dessa måste behållas med hjälp av olika verktyg i Apache Cassandra. 

Apache Cassandra har ett koncept av tokens, som är hashar av partitionsnyckel. Token baseras på en byte-hash på murmur3 64 med värden mellan-2 ^ 63 och-2 ^ 63-1. Det här intervallet kallas ofta "token ring" i Apache Cassandra. Token ring distribueras till token och dessa intervall delas upp bland noderna i ett internt Apache Cassandra-kluster. Partitionering för Azure Cosmos DB implementeras på ett liknande sätt, förutom att den använder en annan hash-algoritm och har en större intern token-ring. Men externt exponerar vi samma token-intervall som Apache Cassandra, d.v.s.-2 ^ 63 till-2 ^ 63-1.


## <a name="primary-key"></a>Primärnyckel

Alla tabeller i API för Cassandra måste ha en `primary key` definierad. Syntaxen för en primär nyckel visas nedan:

```shell
column_name cql_type_definition PRIMARY KEY
```

Anta att vi vill skapa en användar tabell som lagrar meddelanden för olika användare:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

I den här designen har vi definierat `id` fältet som primär nyckel. Den primära nyckeln fungerar som identifierare för posten i tabellen och används också som partitionsnyckel i Azure Cosmos DB. Om primär nyckeln definieras i det tidigare beskrivna sättet kommer det bara finnas en enda post i varje partition. Detta resulterar i en perfekt vågrät och skalbar distribution när data skrivs till databasen och är idealisk för användnings fall med nyckel värdes ökningar. Programmet ska ange den primära nyckeln när data läses från tabellen för att maximera Läs prestanda. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="partitioner" border="false":::


## <a name="compound-primary-key"></a>Sammansatt primär nyckel

Apache Cassandra har även begreppet  `compound keys` . En sammansatt `primary key` består av mer än en kolumn. den första kolumnen är `partition key` och eventuella ytterligare kolumner är `clustering keys` . Syntaxen för en `compound primary key` visas nedan:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Anta att vi vill ändra designen ovan och göra det möjligt att effektivt hämta meddelanden för en specifik användare:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

I den här designen definierar vi nu `user` som partitionsnyckel och `id` som kluster nyckel. Du kan definiera så många kluster nycklar som du vill, men varje värde (eller en kombination av värden) för kluster nyckeln måste vara unika för att leda till att flera poster läggs till i samma partition, till exempel:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

När data returneras sorteras de efter kluster nyckeln, som förväntas i Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Skärm bild som visar returnerade data som sorteras efter kluster nyckeln.":::

Med data som modelleras på det här sättet kan flera poster tilldelas till varje partition, grupperade efter användare. Vi kan därmed utfärda en fråga som effektivt dirigeras av `partition key` (i det här fallet `user` ) för att hämta alla meddelanden för en specifik användare. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagram som visar hur flera poster kan tilldelas varje partition, grupperade efter användare." border="false":::


## <a name="composite-partition-key"></a>Sammansatt partitionsnyckel

Sammansatta partitionerings nycklar fungerar på samma sätt som sammansatta nycklar, förutom att du kan ange flera kolumner som en sammansatt partitionsnyckel. Syntaxen för sammansatta partitionsnyckel visas nedan:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Du kan till exempel ha följande, där den unika kombinationen av `firstname` och `lastname` skulle utgöra partitionsnyckel och `id` är kluster nyckeln:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [partitionering och horisontell skalning i Azure Cosmos DB](partitioning-overview.md).
* Lär dig mer om [etablerade data flöden i Azure Cosmos DB](request-units.md).
* Lär dig mer om [global distribution i Azure Cosmos DB](distribute-data-globally.md).
