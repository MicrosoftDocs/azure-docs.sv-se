---
title: Tillägg – Azure Database for PostgreSQL-flexibel Server
description: Lär dig mer om tillgängliga postgres-tillägg i Azure Database for PostgreSQL-flexibel Server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: e8f71eb120b86f35672c9123b52f7f19c9fee662
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608467"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL-tillägg i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg buntar samman flera relaterade SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett kommando. När tilläggen har lästs in i databasen fungerar de som inbyggda funktioner.

## <a name="how-to-use-postgresql-extensions"></a>Använda PostgreSQL-tillägg
PostgreSQL-tillägg måste vara installerade i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [create Extension](https://www.postgresql.org/docs/current/sql-createextension.html) . Det här kommandot läser in paketerade objekt i databasen.

Azure Database for PostgreSQL stöder en delmängd av nyckel tillägg som anges nedan. Den här informationen är också tillgänglig genom att köra `SHOW azure.extensions;` . Tillägg som inte finns med i det här dokumentet stöds inte på Azure Database for PostgreSQL-flexibel Server. Du kan inte skapa eller läsa in ditt eget tillägg i Azure Database for PostgreSQL.


## <a name="postgres-12-extensions"></a>Postgres 12-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL-flexibla servrar som har postgres version 12. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Exempel på adress Standardiserare för data uppsättning|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | funktioner för att verifiera Relations integritet|
> |[hinna](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | anslutnings metod för blomma – signatur-filbaserat index|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | mall för text Sök lexikon för utökad synonym bearbetning|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | heltals Aggregator och uppräknare. Ersatt|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | granska innehållet i databas sidor på en låg nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | kontrol lera kartan över ledigt utrymme (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | granska Synlighets kartan (VM) och Synlighets informationen på sid nivå|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS Geometry, geografi |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS raster typer och funktioner| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | information om SSL-certifikat|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-metod som accepterar antalet rader som en gräns|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE-metod som accepterar tiden i millisekunder som en gräns|
> |[avaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|

## <a name="postgres-11-extensions"></a>Postgres 11-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL-flexibla servrar som har postgres version 11. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adress Standardiserare för data uppsättning|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | funktioner för att verifiera Relations integritet|
> |[hinna](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | anslutnings metod för blomma – signatur-filbaserat index|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | mall för text Sök lexikon för utökad synonym bearbetning|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | heltals Aggregator och uppräknare. Ersatt|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | granska innehållet i databas sidor på en låg nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | kontrol lera kartan över ledigt utrymme (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | granska Synlighets kartan (VM) och Synlighets informationen på sid nivå|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | information om SSL-certifikat|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE-metod som accepterar antalet rader som en gräns|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE-metod som accepterar tiden i millisekunder som en gräns|
> |[avaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|


## <a name="dblink-and-postgres_fdw"></a>dbLink och postgres_fdw
med [dbLink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) och [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) kan du ansluta från en postgresql-server till en annan, eller till en annan databas på samma server. Flexibel Server stöder både inkommande och utgående anslutningar till valfri PostgreSQL-Server. Den sändande servern måste tillåta utgående anslutningar till den mottagande servern. På samma sätt måste den mottagande servern tillåta anslutningar från den sändande servern. 

Vi rekommenderar att du distribuerar dina servrar med [VNet-integrering](concepts-networking.md) om du planerar att använda dessa två tillägg. Som standard möjliggör VNet-integrering anslutningar mellan servrar i VNET. Du kan också välja att använda [VNet-nätverks säkerhets grupper](../../virtual-network/manage-network-security-group.md) för att anpassa åtkomst.

## <a name="pg_prewarm"></a>pg_prewarm

Pg_prewarm-tillägget läser in relations data i cacheminnet. Att förvärma cacheminnet innebär att dina frågor har bättre svars tider vid första körningen efter en omstart. De automatiska för inaktiverade funktionerna är för närvarande inte tillgängliga i Azure Database for PostgreSQL-flexibel Server.

## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements-tillägget](https://www.postgresql.org/docs/current/pgstatstatements.html) är förinstallerat på varje Azure Database for PostgreSQL flexibel Server för att ge dig ett sätt att spåra körnings statistik över SQL-uttryck.
Inställningen `pg_stat_statements.track` , som styr vilka instruktioner som räknas av tillägget, är standardvärdet `top` , vilket innebär att alla instruktioner som utfärdas direkt av klienter spåras. De två andra spårnings nivåerna är `none` och `all` . Den här inställningen kan konfigureras som en server parameter.

Det uppstår en kompromiss mellan information om körningen pg_stat_statements tillhandahåller och påverkan på Server prestanda när varje SQL-sats loggas. Om du inte aktivt använder pg_stat_statements-tillägget rekommenderar vi att du ställer in `pg_stat_statements.track` på `none` . Observera att vissa övervaknings tjänster från tredje part kan förlita sig på pg_stat_statements för att leverera frågor om prestanda insikter, så kontrol lera om detta är fallet för dig eller inte.


## <a name="next-steps"></a>Nästa steg

Om du inte ser ett tillägg som du vill använda, kan du berätta för oss. Rösta på befintliga förfrågningar eller skapa nya feedback-förfrågningar i vårt [feedback-forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).