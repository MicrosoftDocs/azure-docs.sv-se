---
title: 'Självstudie: Shard data on Worker Nodes-Scale (citus)-Azure Database for PostgreSQL'
description: I den här självstudien visas hur du skapar distribuerade tabeller och visualiserar data distribution med Azure Database for PostgreSQL storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630317"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Självstudie: Shard data on Worker Nodes in Azure Database for PostgreSQL – Scale (citus)

I den här självstudien använder du Azure Database for PostgreSQL-storskalig skalning (citus) för att lära dig att:

> [!div class="checklist"]
> * Skapa hash-distribuerade Shards
> * Se var tabell Shards placeras
> * Identifiera skevad distribution
> * Skapa begränsningar i distribuerade tabeller
> * Köra frågor på distribuerade data

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien krävs en citus-Server grupp med två arbetsnoder. Om du inte har en Server grupp som körs följer du själv studie kursen [skapa Server grupp](tutorial-hyperscale-server-group.md) och återgår sedan till den här.

## <a name="hash-distributed-data"></a>Hash-distribuerade data

Att distribuera tabell rader över flera PostgreSQL-servrar är en viktig teknik för skalbara frågor i storskaliga (citus). Tillsammans kan flera noder innehålla mer data än en traditionell databas och i många fall kan du använda Worker-processorer parallellt för att köra frågor.

I avsnittet krav skapade vi en citus-Server grupp med två arbetsnoder.

![koordinator och två arbetare](tutorial-hyperscale-shard/nodes.png)

Kontextnoden-nodens metadata tabeller spårar arbetare och distribuerade data. Vi kan kontrol lera aktiva arbetare i [pg_dist_nodes](reference-hyperscale-metadata.md#worker-node-table) tabellen.

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Nodenames på citus () är interna IP-adresser i ett virtuellt nätverk, och de faktiska adresser som visas kan skilja sig åt.

### <a name="rows-shards-and-placements"></a>Rader, Shards och placeringar

För att kunna använda processor-och lagrings resurser för arbetsnoder måste vi distribuera tabell data i hela Server gruppen.  När du distribuerar en tabell tilldelas varje rad till en logisk grupp som kallas en *Shard.* Nu ska vi skapa en tabell och distribuera den:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Med citus) tilldelas varje rad till en Shard baserat på värdet för *distributions kolumnen*, som i vårt fall har angetts som `email` . Varje rad kommer att finnas i exakt en Shard och varje Shard kan innehålla flera rader.

![tabellen användare med rader som pekar på Shards](tutorial-hyperscale-shard/table.png)

Som standard `create_distributed_table()` gör 32 Shards, eftersom vi kan se antalet i tabellen metadata [pg_dist_shard](reference-hyperscale-metadata.md#shard-table):

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Storskalig (citus) använder `pg_dist_shard` tabellen för att tilldela rader till Shards baserat på en hash av värdet i kolumnen distribution. Hash-informationen är oviktig för den här självstudien. Vad är det som är viktigt att fråga för att se vilka värden som mappar till vilka Shard-ID: n:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Mappningen av rader till Shards är rent logiskt. Shards måste tilldelas till vissa arbetsnoder för lagring i vilken storskalig (citus) anropar *Shard placering*.

![Shards tilldelad till arbetare](tutorial-hyperscale-shard/shard-placement.png)

Vi kan titta på Shard-placeringarna i [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table).
Med de andra metadata tabellerna som vi har sett visar var varje Shard är i livet.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Data skevning

En Server grupp körs mest effektivt när du placerar data jämnt på arbetsnoder och när du placerar relaterade data tillsammans i samma arbetare. I det här avsnittet ska vi fokusera på den första delen, enhetligheten för placering.

För att demonstrera, ska vi skapa exempel data för vår `users` tabell:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

För att se Shard storlekar kan vi köra [tabell storleks funktioner](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) på Shards.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Vi kan se att Shards är av samma storlek. Vi såg redan att placeringarna är jämnt fördelade bland arbets tagarna, så vi kan ange att arbetsnoderna ska innehålla ungefär lika många rader.

Raderna i vårt `users` exempel distribueras jämnt på grund av egenskaperna för kolumnen distribution `email` .

1. Antalet e-postadresser var större än eller lika med antalet Shards.
2. Antalet rader per e-postadress var liknande (i vårt fall, exakt en rad per adress eftersom vi deklarerade e-post en nyckel).

Alla val av tabell-och distributions kolumner där endera av de båda egenskaperna Miss lyckas med ojämn data storlek för arbetare, det vill säga *data skevningen*.

### <a name="add-constraints-to-distributed-data"></a>Lägg till begränsningar till distribuerade data

Med hjälp av storskalig (citus) kan du fortsätta att njuta av säkerheten i en Relations databas, inklusive [databas begränsningar](https://www.postgresql.org/docs/current/ddl-constraints.html).
Det finns dock en begränsning. På grund av typen av distribuerade system kan inte storskalig (citus) begränsa unikhetsvillkor eller referens integritet mellan arbetsnoder.

Vi ska titta `users` på vårt tabell exempel med en relaterad tabell.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

För effektivitet distribuerar vi `books` på samma sätt som `users` : av ägarens e-postadress. Distribution av likartade kolumn värden kallas [samplacering](concepts-hyperscale-colocation.md).

Vi hade inga problem med att distribuera böcker med en sekundär nyckel till användare eftersom nyckeln fanns i en distributions kolumn. Vi skulle dock ha problem med att göra `isbn` en nyckel:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

I en distribuerad tabell är det bästa vad vi kan göra är att skapa kolumner unika modulo i kolumnen distribution:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Ovanstående begränsning gör bara ISBN unikt per användare. Ett annat alternativ är att skapa böcker med en [referens tabell](howto-hyperscale-modify-distributed-tables.md#reference-tables) i stället för en distribuerad tabell och skapa en separat distribuerad tabell som kopplar böcker till användare.

## <a name="query-distributed-tables"></a>Fråga distribuerade tabeller

I föregående avsnitt såg vi hur distribuerade tabell rader placeras i Shards på arbetsnoder. I de flesta fall behöver du inte veta hur eller var data lagras i en Server grupp. Citus (storskalig) har ett distribuerat fråge-utförar som automatiskt delar upp vanliga SQL-frågor. Den körs parallellt på arbetsnoderna nära data.

Vi kan till exempel köra en fråga för att hitta användarens genomsnittliga ålder, vilket behandlar den distribuerade `users` tabellen så att den är en vanlig tabell i koordinatorn.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![frågan kommer till Shards via koordinatorn](tutorial-hyperscale-shard/query-fragments.png)

Utförar (citus) i bakgrunden skapar en separat fråga för varje Shard, kör dem på arbets tagarna och kombinerar resultatet. Du kan se det om du använder kommandot PostgreSQL förklaring:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

Utdata visar ett exempel på en körnings plan för ett *fragment* som körs på Shard 102040 (tabellen `users_102040` på Worker-10.0.0.21). De andra fragmenten visas inte eftersom de är likartade. Vi kan se att arbetsnoden genomsöker Shard-tabellerna och använder mängden. Koordinator-noden kombinerar agg regeringar för det slutliga resultatet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har vi skapat en distribuerad tabell och lärt dig om dess Shards och placeringar. Vi har sett en utmaning med att använda unikhetsvillkor och sekundär nyckel begränsningar och såg slutligen hur distribuerade frågor fungerar på en hög nivå.

* Läs mer om [tabell typer](concepts-hyperscale-nodes.md) för storskaliga (citus)
* Få fler tips om [att välja en distributions kolumn](concepts-hyperscale-choose-distribution-column.md)
* Lär dig fördelarna med [Table-samplacering](concepts-hyperscale-colocation.md)
