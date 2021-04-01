---
title: Ändra distribuerade tabeller – storskalig (citus) – Azure Database for PostgreSQL
description: SQL-kommandon för att skapa och ändra distribuerade tabeller – storskalig (citus) med hjälp av Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026445"
---
# <a name="distribute-and-modify-tables"></a>Distribuera och ändra tabeller

## <a name="distributing-tables"></a>Distribuera tabeller

Om du vill skapa en distribuerad tabell måste du först definiera tabell schemat. För att göra det kan du definiera en tabell med hjälp av [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) -instruktionen på samma sätt som du gör med en vanlig postgresql-tabell.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Sedan kan du använda \_ funktionen Create Distributed \_ Table () för att ange tabell distributions kolumnen och skapa Worker-Shards.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

Funktions anropet informerar den storskaliga (citus) som GitHub \_ Events-tabellen ska distribueras i \_ kolumnen lagrings platsen ID (genom hashing av kolumnvärdet). Funktionen skapar också Shards på arbetsnoderna med hjälp av värdena citus. Shard \_ Count och citus. \_ Shard \_ .

Det skapar totalt antal citus. Shard \_ antal Shards, där varje Shard äger en del av ett hash-utrymme och replikeras baserat på \_ standardvärdet för citus. Shard-replikeringskonfiguration \_ . Shard-replikerna som skapas i arbetaren har samma tabell schema, index och begränsnings definitioner som tabellen i koordinatorn. När replikerna har skapats sparar funktionen alla distribuerade metadata i koordinatorn.

Varje skapad Shard tilldelas ett unikt Shard-ID och alla dess repliker har samma Shard-ID. Shards representeras på arbetsnoden som vanliga PostgreSQL-tabeller som heter \' TableName \_ shardid \' där TableName är namnet på den distribuerade tabellen och Shard-ID: t är det unika ID som tilldelats. Du kan ansluta till Worker postgres-instanserna för att visa eller köra kommandon på enskilda Shards.

Nu är du redo att infoga data i den distribuerade tabellen och köra frågor på den. Du kan också lära dig mer om UDF som används i det här avsnittet i [Table och SHARD DDL](reference-hyperscale-functions.md#table-and-shard-ddl) -referens.

### <a name="reference-tables"></a>Referens tabeller

Metoden ovan distribuerar tabeller till flera horisontella Shards.  En annan möjlighet är att distribuera tabeller till en enda Shard och replikera Shard till alla arbetsnoder. Tabeller som distribueras på det här sättet kallas *referens tabeller.* De används för att lagra data som behöver nås ofta av flera noder i ett kluster.

Vanliga kandidater till referens tabeller är:

-   Mindre tabeller som måste kopplas till med större distribuerade tabeller.
-   Tabeller i appar för flera klienter som saknar en klient-ID-kolumn eller som är \' t kopplad till en klient. (Eller, vid migrering, även för vissa tabeller som är kopplade till en klient.)
-   Tabeller som behöver unika begränsningar i flera kolumner och är tillräckligt små.

Anta till exempel att en eCommerce plats med flera innehavare måste beräkna moms för transaktioner i någon av dess butiker. Skatte information inte är \' t är speciell för alla klienter. Det är klokt att infoga det i en delad tabell. En US-centrisk referens tabell kan se ut så här:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Nu kan frågor som en beräknings skatt för en Shopping vagn anslutas till `states` tabellen utan nätverks kostnader och kan lägga till en sekundär nyckel till delstats koden för bättre verifiering.

Förutom att distribuera en tabell som en enda replikerad Shard, `create_reference_table` markerar UDF-filen som en referens tabell i citus-metadata-tabellerna (). Citus) utför automatiskt två-fas-incheckningar ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) för ändringar i tabeller som marker ATS på det här sättet, vilket ger starka konsekvens garantier.

Om du har en distribuerad tabell med ett Shard antal kan du uppgradera den till en känd referens tabell så här:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Ett annat exempel på hur du använder referens tabeller finns i [själv studie kursen för flera klient databaser](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Distribuera koordinator data

Om en befintlig PostgreSQL-databas konverteras till koordinator-noden för ett citus-kluster kan data i dess tabeller distribueras effektivt och med minimalt avbrott i ett program.

`create_distributed_table`Funktionen som beskrivs tidigare fungerar både i tomma och icke-tomma tabeller, och för den senare distribuerar tabell rader automatiskt i hela klustret. Du kommer att veta om den kopierar data efter meddelandets förekomst, \" meddelande: kopiera data från lokal tabell \. . \" Exempel:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Skrivningar i tabellen blockeras medan data migreras och väntande skrivningar hanteras som distribuerade frågor när funktionen har genomförts. (Om funktionen Miss lyckas blir frågorna lokala igen.) Läsningar kan fortsätta som normalt och blir distribuerade frågor när funktionen har genomförts.

När du distribuerar tabeller A och B, där en har en sekundär nyckel till B, distribuerar du nyckeln mål tabell B först. Att göra det i fel ordning orsakar ett fel:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Om det inte går att distribuera i rätt ordning tar du bort sekundär nycklarna, distribuerar tabellerna och återskapar sekundär nycklarna.

När du migrerar data från en extern databas, till exempel från Amazon RDS till citus-molnet, skapar du först citus-distribuerade tabeller () och `create_distributed_table` kopierar sedan data till tabellen.
Om du kopierar till distribuerade tabeller slipper du ta slut på utrymme på koordinator-noden.

## <a name="colocating-tables"></a>Samplacera tabeller

Samplacering innebär att placera relaterad information på samma datorer. Det möjliggör effektiva frågor och drar nytta av den vågräta skalbarheten för hela data uppsättningen. Mer information finns i [samplacering](concepts-hyperscale-colocation.md).

Tabeller är samplacerade i grupper. Om du vill kontrol lera en tabells grupp tilldelning för samplacering manuellt använder du den valfria `colocate_with` parametern för `create_distributed_table` . Om du inte \' bryr dig om en tabells \' samplacering utelämnar du den här parametern. Standardvärdet är det som används för att `'default'` gruppera tabellen med någon annan standard tabell för samplacering som har samma distributions kolumn typ, antal Shard och en replikeringsrelation. Om du vill avbryta eller uppdatera den här implicita samplaceringen kan du använda `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

När en ny tabell inte är relaterad till andra i en implicit samplacerings grupp anger du `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Om du delar upp orelaterade tabeller i sina egna samplacerings grupper förbättras prestanda för [ombalansering av Shard](howto-hyperscale-scale-rebalance.md) , eftersom Shards i samma grupp måste flyttas tillsammans.

När tabeller är relaterade (till exempel när de kommer att kopplas), kan det vara klokt att explicit hitta dem. Vinsterna för lämplig samplacering är viktigare än eventuella ombalanserings kostnader.

Om du vill samplacera flera tabeller, distribuerar du en och lägger sedan till de andra i sin samplacerings grupp. Exempel:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Information om samplacerings grupper lagras i [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) tabellen, medan [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) visar vilka tabeller som är tilldelade till vilka grupper.

## <a name="dropping-tables"></a>Släpper tabeller

Du kan använda standard kommandot PostgreSQL DROP TABLE för att ta bort dina distribuerade tabeller. Precis som med vanliga tabeller tar DROP TABLE bort eventuella index, regler, utlösare och begränsningar som finns för mål tabellen. Dessutom släpper den också Shards på arbetsnoderna och rensar deras metadata.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Ändra tabeller

Citus (storskalig) sprider automatiskt många typer av DDL-uttryck.
Om du ändrar en distribuerad tabell på koordinator noden uppdateras Shards även för arbets tagarna. Andra DDL-uttryck kräver manuell spridning och vissa andra är förbjudna, till exempel sådana som skulle ändra en distributions kolumn.
Om du försöker köra DDL som är inaktive ras för automatisk spridning utlöses ett fel och inga tabeller på koordinator noden ändras.

Här är en referens för de typer av DDL-uttryck som sprider sig.
Automatisk spridning kan aktive ras eller inaktive ras med en [konfigurations parameter](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Lägga till/ändra kolumner

Citus (storskalig) sprider de flesta [Alter Table](https://www.postgresql.org/docs/current/static/ddl-alter.html) -kommandon automatiskt. Att lägga till kolumner eller ändra deras standardvärden fungerar som i en PostgreSQL-databas med en enda dator:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Betydande ändringar i en befintlig kolumn, t. ex. att byta namn på den eller ändra dess datatyp, är också bra. Det går dock inte att ändra data typen för [distributions kolumnen](concepts-hyperscale-nodes.md#distribution-column) .
Den här kolumnen avgör hur tabell data distribueras via citus-klustret (storskalig) och att ändra dess datatyp kräver att data flyttas.

Om du försöker göra det uppstår ett fel:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Lägga till/ta bort begränsningar

Med hjälp av storskalig (citus) kan du fortsätta att njuta av säkerheten i en Relations databas, inklusive databas begränsningar (se PostgreSQL- [dokumenten](https://www.postgresql.org/docs/current/static/ddl-constraints.html)).
På grund av typen av distribuerade system kommer storskaligheten (citus) inte kors referens begränsningar eller referens integritet mellan arbetsnoder.

Om du vill konfigurera en sekundär nyckel mellan samplacerade distribuerade tabeller ska du alltid inkludera distributions kolumnen i nyckeln. Även om distributions kolumnen kan innebära att nyckeln är sammansatt.

Sekundär nycklar kan skapas i följande situationer:

-   mellan två lokala (icke-distribuerade) tabeller,
-   mellan två referens tabeller
-   mellan två [samplacerade](concepts-hyperscale-colocation.md) distribuerade tabeller när nyckeln innehåller distributions kolumnen, eller
-   som en distribuerad tabell som refererar till en [referens tabell](concepts-hyperscale-nodes.md#type-2-reference-tables)

Sekundär nycklar från referens tabeller till distribuerade tabeller stöds inte.

> [!NOTE]
>
> Primär nycklar och unikhetsvillkor måste innehålla distributions kolumnen. Om du lägger till dem i en icke-postdistributionsgrupp-kolumn skapas ett fel

Det här exemplet visar hur du skapar primära och externa nycklar i distribuerade tabeller:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

På samma sätt inkluderar du distributions kolumnen i unikhetsvillkor:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Icke-null-begränsningar kan tillämpas på alla kolumner (distributioner eller inte) eftersom de inte kräver några sökningar mellan arbets tagarna.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Använda ogiltiga begränsningar

I vissa situationer kan det vara användbart att tillämpa begränsningar för nya rader, samtidigt som befintliga icke-överensstämmande rader inte ändras. Citus (storskalig) stöder den här funktionen för kontroll begränsningar och sekundär nycklar med hjälp av PostgreSQL s som inte är en \' \" giltig \" begränsnings beteckning.

Överväg till exempel ett program som lagrar användar profiler i en [referens tabell](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

Under tiden tar det lång tid att ta några icke-adresser i tabellen.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Vi vill verifiera adresserna, men PostgreSQL inte vanligt vis att vi ska lägga till en kontroll begränsning som inte kan utföras för befintliga rader. *Men det tillåter* att ett villkor som marker ATS som inte är giltigt:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Nya rader är nu skyddade.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Senare, under låg belastnings tider, kan en databas administratör försöka åtgärda felaktiga rader och omverifiera begränsningen.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

PostgreSQL-dokumentationen innehåller mer information om ogiltig och VERIFIERAd begränsning i avsnittet [Alter Table](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Lägga till/ta bort index

Citus (storskalig) har stöd för att lägga till och ta bort [index](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Genom att lägga till ett index får du ett skrivlås som kan vara oönskat i ett \" system med flera klient organisationer. \" Om du vill minimera avbrotts tiden i programmet skapar du indexet [samtidigt](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Den här metoden kräver mer total arbete än en standard index version och tar längre tid att slutföra. Men eftersom den tillåter normal drift att fortsätta medan indexet har skapats, är den här metoden användbar för att lägga till nya index i en produktions miljö.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
