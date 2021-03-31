---
title: Logisk replikering och logisk avkodning-Azure Database for PostgreSQL-flexibel Server
description: Lär dig mer om att använda logisk replikering och logisk avkodning i Azure Database for PostgreSQL-flexibel Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91707871"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Logisk replikering och logisk avkodning i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

PostgreSQL för logiska replikering och logiska avkodning stöds i Azure Database for PostgreSQL-flexibel Server, för postgres version 11.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Jämföra logisk replikering och logisk avkodning
Logisk replikering och logisk avkodning har flera likheter. Båda
* gör att du kan replikera data från postgres
* använda [loggen för Skriv åtgärder (Wal)](https://www.postgresql.org/docs/current/wal.html) som källa för ändringarna
* Använd [logiska replikerings platser](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) för att skicka ut data. En plats representerar en ström med ändringar.
* Använd en tabells [egenskap för replik identitet](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) för att avgöra vilka ändringar som kan skickas
* Replikera inte DDL-ändringar


De två teknikerna har skillnader: logisk replikering 
* gör att du kan ange en tabell eller en uppsättning tabeller som ska replikeras
* replikerar data mellan PostgreSQL-instanser

Logisk avkodning 
* extraherar ändringar i alla tabeller i en databas 
* Det går inte att skicka data direkt mellan PostgreSQL-instanser


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Krav för logisk replikering och logisk avkodning

1. Ange server parametern `wal_level` till `logical` .
2. Starta om servern för att tillämpa `wal_level` ändringen.
3. Bekräfta att PostgreSQL-instansen tillåter nätverks trafik från din anslutande resurs.
4. Bevilja behörighet för administratörs användare.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Använda logisk replikering och logisk avkodning

### <a name="logical-replication"></a>Logisk replikering
Logisk replikering använder villkoren "utgivare" och "prenumerant". 
* Utgivaren är PostgreSQL-databasen som du skickar data **från**. 
* Prenumeranten är PostgreSQL-databasen som du skickar data **till**.

Här är en exempel kod som du kan använda för att testa logisk replikering.

1. Anslut till utgivar databasen. Skapa en tabell och Lägg till data.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Skapa en publikation för tabellen.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Anslut till prenumerantens databas. Skapa en tabell med samma schema som utgivaren.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Skapa en prenumeration som kommer att ansluta till publikationen du skapade tidigare.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Nu kan du fråga tabellen på prenumeranten. Du kommer att se att den har tagit emot data från utgivaren.
   ```SQL
   SELECT * FROM basic;
   ```

Du kan lägga till fler rader i utgivarens tabell och se ändringarna på prenumeranten.

Besök PostgreSQL-dokumentationen om du vill veta mer om [logisk replikering](https://www.postgresql.org/docs/current/logical-replication.html).

### <a name="logical-decoding"></a>Logisk avkodning
Logisk avkodning kan förbrukas via direkt uppspelnings protokollet eller SQL-gränssnittet. 

#### <a name="streaming-protocol"></a>Strömnings protokoll
Användning av ändringar med streaming-protokollet är ofta bättre. Du kan skapa en egen konsument/anslutning eller använda en tjänst från tredje part som [Debezium](https://debezium.io/). 

Besök wal2json-dokumentationen för [ett exempel som använder streaming-protokollet med pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>SQL-gränssnitt 
I exemplet nedan använder vi SQL-gränssnittet med wal2json-plugin-programmet.
 
1. Skapa en plats.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Utfärda SQL-kommandon. Exempel:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Förbrukar ändringarna.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Resultatet kommer att se ut så här:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Ta bort facket när du är färdig med den.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Besök PostgreSQL-dokumentationen om du vill veta mer om [logisk avkodning](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Övervakning
Du måste övervaka logisk avkodning. Eventuell oanvänd replikerings plats måste släppas. Platser är kvar på postgres WAL-loggar och relevanta system kataloger tills ändringarna har lästs. Om din prenumerant eller kund Miss lyckas eller inte har kon figurer ATS korrekt, kommer de oanvända loggarna att staplas och fylla lagringen. Oanvända loggar ökar också risken för transaktions-ID wraparound. Båda situationerna kan orsaka att servern blir otillgänglig. Därför är det viktigt att logiska replikerings platser konsumeras kontinuerligt. Om ingen logisk replikerings plats används tar du bort den direkt.

Kolumnen "aktiv" i vyn pg_replication_slots visar om det finns en konsument som är ansluten till en plats.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ange aviseringar](howto-alert-on-metrics.md) för **högsta använda transaktions-ID** och **lagrings utrymme som används** med flexibla Server mått för att meddela dig när värdena ökar de tidigare normala tröskelvärdena. 

## <a name="limitations"></a>Begränsningar
* **Läs repliker** – Azure Database for PostgreSQL Läs repliker stöds för närvarande inte för flexibla servrar.
* **Platser och ha redundans** – logiska replikerings-platser på den primära servern är inte tillgängliga på vänte läges servern i din sekundära AZ. Detta gäller om servern använder det alternativ för zon-redundant hög tillgänglighet. I händelse av redundansväxling till standby-servern är inte logiska replikerings platser tillgängliga i vänte läge.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [nätverks alternativ](concepts-networking.md)
* Lär dig mer om [tillägg](concepts-extensions.md) som är tillgängliga i flexibel Server
* Lär dig mer om [hög tillgänglighet](concepts-high-availability.md)

