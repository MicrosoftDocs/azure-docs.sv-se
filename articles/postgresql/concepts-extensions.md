---
title: Tillägg – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om de tillgängliga postgres-tilläggen i Azure Database for PostgreSQL-enskild server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: de395bed885804445146b01745edf5e93426cb13
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606325"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-tillägg i Azure Database for PostgreSQL – enskild server
PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg samlar flera SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. När tilläggen har lästs in i databasen fungerar de som inbyggda funktioner.

## <a name="how-to-use-postgresql-extensions"></a>Använda PostgreSQL-tillägg
PostgreSQL-tillägg måste vara installerade i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) från psql-verktyget för att läsa in de paketerade objekten i databasen.

Azure Database for PostgreSQL stöder en delmängd av nyckel tillägg som anges nedan. Den här informationen är också tillgänglig genom att köra `SELECT * FROM pg_available_extensions;` . Tillägg utöver de som anges i listan stöds inte. Du kan inte skapa ditt eget tillägg i Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Postgres 11-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL servrar som har postgres version 11. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adress Standardiserare för data uppsättning|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypotetiska index för PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatorer som emulerar en delmängd funktioner och paket från kommersiella RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-tillägg|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/Java Script (V8) Trusted Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2, 1.7.4             | Möjliggör skalbara infogningar och komplexa frågor för Time Series-data|
> |[avaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|

## <a name="postgres-10-extensions"></a>Postgres 10-tillägg 

Följande tillägg är tillgängliga i Azure Database for PostgreSQL servrar som har postgres version 10.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adress Standardiserare för data uppsättning|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösen ord|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatorer som emulerar en delmängd funktioner och paket från kommersiella RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting-tillägg|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | text likhets mätning och indexs ökning baserat på trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/Java Script (V8) Trusted Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Möjliggör skalbara infogningar och komplexa frågor för Time Series-data|
> |[avaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|

## <a name="postgres-96-extensions"></a>Postgres 9,6-tillägg 

Följande tillägg är tillgängliga i Azure Database for PostgreSQL servrar som har postgres version 9,6.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Exempel på adress Standardiserare för data uppsättning|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | stöd för indexering av vanliga data typer i registret|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösen ord|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatorer som emulerar en delmängd funktioner och paket från kommersiella RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting-tillägg|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Visa statistik på tuple nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | granska cacheminnet för den delade bufferten|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | text likhets mätning och indexs ökning baserat på trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/Java Script (V8) Trusted Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Möjliggör skalbara infogningar och komplexa frågor för Time Series-data|
> |[avaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|

## <a name="postgres-95-extensions"></a>Postgres 9,5-tillägg

>[!NOTE]
> PostgreSQL version 9,5 har dragits tillbaka.

Följande tillägg är tillgängliga i Azure Database for PostgreSQL servrar som har postgres version 9,5.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Exempel på adress Standardiserare för data uppsättning|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | stöd för indexering av vanliga data typer i registret|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösen ord|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | datatyp för hierarkiska träd strukturer|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatorer som emulerar en delmängd funktioner och paket från kommersiella RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | kryptografiska funktioner|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting-tillägg|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Visa statistik på tuple nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | granska cacheminnet för den delade bufferten|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | text likhets mätning och indexs ökning baserat på trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[avaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generera globalt unika identifierare (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements-tillägget](https://www.postgresql.org/docs/current/pgstatstatements.html) är förinstallerat på varje Azure Database for postgresql-server för att ge dig ett sätt att spåra körnings statistik över SQL-uttryck.
Inställningen `pg_stat_statements.track` , som styr vilka instruktioner som räknas av tillägget, är standardvärdet `top` , vilket innebär att alla instruktioner som utfärdas direkt av klienter spåras. De två andra spårnings nivåerna är `none` och `all` . Den här inställningen kan konfigureras som en server parameter via [Azure Portal](./howto-configure-server-parameters-using-portal.md) eller [Azure CLI](./howto-configure-server-parameters-using-cli.md).

Det uppstår en kompromiss mellan information om körningen pg_stat_statements tillhandahåller och påverkan på Server prestanda när varje SQL-sats loggas. Om du inte aktivt använder pg_stat_statements-tillägget rekommenderar vi att du ställer in `pg_stat_statements.track` på `none` . Observera att vissa övervaknings tjänster från tredje part kan förlita sig på pg_stat_statements för att leverera frågor om prestanda insikter, så kontrol lera om detta är fallet för dig eller inte.

## <a name="dblink-and-postgres_fdw"></a>dbLink och postgres_fdw
med [dbLink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) och [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) kan du ansluta från en postgresql-server till en annan, eller till en annan databas på samma server. Den mottagande servern måste tillåta anslutningar från den sändande servern via brand väggen. När du använder dessa tillägg för att ansluta mellan Azure Database for PostgreSQL-servrar kan du göra detta genom att ange "Tillåt åtkomst till Azure-tjänster" till på. Detta behövs även om du vill använda tilläggen för att återgå till samma server. Inställningen "Tillåt åtkomst till Azure-tjänster" finns på Azure Portal sidan för postgres-servern under anslutnings säkerhet. Att aktivera "Tillåt åtkomst till Azure-tjänster" på placerar alla Azure IP-adresser i listan över tillåtna.

För närvarande stöds inte utgående anslutningar från Azure Database for PostgreSQL, förutom för anslutningar till andra Azure Database for PostgreSQL-servrar i samma region.

## <a name="uuid"></a>uuid
Om du planerar att använda `uuid_generate_v4()` från [UUID-ossp-tillägget](https://www.postgresql.org/docs/current/uuid-ossp.html)kan du jämföra med med `gen_random_uuid()` pgcrypto- [tillägget](https://www.postgresql.org/docs/current/pgcrypto.html) för prestanda förmåner.

## <a name="pgaudit"></a>pgAudit
[PgAudit-tillägget](https://github.com/pgaudit/pgaudit/blob/master/README.md) innehåller loggning av session-och objekt granskning. Information om hur du använder det här tillägget i Azure Database for PostgreSQL finns i [artikeln gransknings begrepp](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm-tillägget läser in relations data i cacheminnet. Att förvärma cacheminnet innebär att dina frågor har bättre svars tider vid första körningen efter en omstart. I postgres 10 och nedan görs förvärmingen manuellt med hjälp av [funktionen för invarm](https://www.postgresql.org/docs/10/pgprewarm.html).

I postgres 11 och senare kan du konfigurera förvärmning så att det sker [automatiskt](https://www.postgresql.org/docs/current/pgprewarm.html). Du måste inkludera pg_prewarm i `shared_preload_libraries` parameter listan och starta om servern för att tillämpa ändringen. Parametrar kan anges från mallen [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API eller arm. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB är en databas för tids serier som är paketerad som ett tillägg för PostgreSQL. TimescaleDB tillhandahåller tidsorienterade analys funktioner, optimeringar och skalar postgres för arbets belastningar för tids serier.

[Läs mer om TimescaleDB](https://docs.timescale.com/latest), ett registrerat varumärke som tillhör [tids skala, Inc.](https://www.timescale.com/). Azure Database for PostgreSQL tillhandahåller TimescaleDB [Apache-2-versionen](https://www.timescale.com/legal/licenses).

### <a name="installing-timescaledb"></a>Installerar TimescaleDB
Om du vill installera TimescaleDB måste du ta med den i serverns delade preload-bibliotek. En ändring av postgres- `shared_preload_libraries` parametern kräver att en **omstart av servern** börjar gälla. Du kan ändra parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

Använda [Azure Portal](https://portal.azure.com/):

1. Välj din Azure Database for PostgreSQL-server.

2. Välj **Server parametrar** på sid panelen.

3. Sök efter `shared_preload_libraries` parametern.

4. Välj **TimescaleDB**.

5. Klicka på **Spara** för att bevara ändringarna. Du får ett meddelande när ändringen har sparats. 

6. Efter aviseringen **startar du om** servern för att tillämpa ändringarna. Information om hur du startar om en server finns i [starta om en Azure Database for postgresql-server](howto-restart-server-portal.md).


Nu kan du aktivera TimescaleDB i postgres-databasen. Anslut till databasen och utfärda följande kommando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Om du ser ett fel bekräftar du att du har [startat om servern](howto-restart-server-portal.md) när du har sparat shared_preload_libraries. 

Nu kan du skapa en TimescaleDB-tabell [från början](https://docs.timescale.com/getting-started/creating-hypertables) eller migrera [befintliga Time Series-data i postgresql](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Återställa en tids Skale databas
Om du vill återställa en tids Skale databas med pg_dump och pg_restore måste du köra två hjälp procedurer i mål databasen: `timescaledb_pre_restore()` och `timescaledb_post restore()` .

Förbered mål databasen först:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Nu kan du köra pg_dump på den ursprungliga databasen och sedan göra pg_restore. Efter återställningen ska du se till att köra följande kommando i den återställda databasen:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, kan du berätta för oss. Rösta på befintliga förfrågningar eller skapa nya feedback-förfrågningar i vårt [feedback-forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).