---
title: Indexering i Azure Cosmos DB API för Cassandra konto
description: Lär dig hur sekundär indexering fungerar i Azure Azure Cosmos DB API för Cassandra-konto.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339906"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Sekundär indexering i Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB utnyttjar den underliggande indexerings infrastrukturen för att exponera den index styrka som är inbyggd i plattformen. Men till skillnad från SQL API-kärnan indexeras API för Cassandra i Azure Cosmos DB inte alla attribut som standard. I stället stöder den sekundär indexering för att skapa ett index på vissa attribut, som fungerar på samma sätt som Apache Cassandra.  

I allmänhet uppmanas vi inte att köra filter frågor på de kolumner som inte är partitionerade. Du måste använda syntaxen Tillåt filtrering explicit, vilket resulterar i en åtgärd som kanske inte fungerar bra. I Azure Cosmos DB kan du köra sådana frågor på attribut med låg kardinalitet eftersom de sprider sig över partitioner för att hämta resultaten.

Vi rekommenderar inte att du skapar ett index i en ofta uppdaterad kolumn. Det är försiktig att skapa ett index när du definierar tabellen. Detta säkerställer att data och index är i ett konsekvent tillstånd. Om du skapar ett nytt index för befintliga data kan du för närvarande inte spåra ändring av index förloppet för tabellen. Om du behöver följa förloppet för den här åtgärden måste du begära att förloppet ändras via ett [support ärende]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Sekundärt index stöds inte för följande objekt:
> - data typer som frysta samlings typer, decimaler och variant-typer.
> - Statiska kolumner
> - Kluster nycklar

## <a name="indexing-example"></a>Indexerings exempel

Börja med att skapa ett exempel på ett blank steg och en tabell genom att köra följande kommandon i CQL Shell-prompten:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Infoga sedan exempel användar data med följande kommandon:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Om du försöker köra följande instruktion får du ett fel meddelande som uppmanar dig att använda `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Även om API för Cassandra stöder filtrering, som vi nämnt i föregående avsnitt, rekommenderar vi inte det. I stället bör du skapa ett index i som visas i följande exempel:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
När du har skapat ett index i fältet LastName kan du köra den tidigare frågan. Med API för Cassandra i Azure Cosmos DB behöver du inte ange något index namn. Ett standard index med format `tablename_columnname_idx` används. Till exempel ` t1_lastname_idx` är index namnet för föregående tabell.

## <a name="dropping-the-index"></a>Släpper indexet 
Du måste veta vad index namnet är för att ta bort indexet. Kör `desc schema` kommandot för att hämta en beskrivning av din tabell. Utdata från det här kommandot inkluderar index namnet i formatet `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Du kan sedan använda index namnet för att ta bort indexet, som du ser i följande exempel:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur [Automatisk indexering](index-overview.md) fungerar i Azure Cosmos DB
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API](cassandra-support.md)
