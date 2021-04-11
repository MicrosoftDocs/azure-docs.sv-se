---
title: Tillägg – storskalig (citus) – Azure Database for PostgreSQL
description: Beskriver möjligheten att utöka funktionaliteten i databasen med hjälp av tillägg i Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012770"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-tillägg i Azure Database for PostgreSQL – storskalig (citus)

Med PostgreSQL är det möjligt att utöka funktionerna i databasen med hjälp av tillägg. Tillägg tillåter flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. När tilläggen har lästs in i databasen, fungerar de som inbyggda funktioner. Mer information om PostgreSQL-tillägg finns i [paket relaterade objekt till ett tillägg](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Använd PostgreSQL-tillägg

PostgreSQL-tillägg måste vara installerade i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [skapa tillägg](https://www.postgresql.org/docs/current/static/sql-createextension.html) från psql-verktyget för att läsa in de paketerade objekten i databasen.

Azure Database for PostgreSQL-Scale (citus) stöder för närvarande en delmängd av nyckel tillägg som visas här. Andra tillägg än de som listas stöds inte. Du kan inte skapa ditt eget tillägg med Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database for PostgreSQL

I följande tabeller visas de standard PostgreSQL-tillägg som för närvarande stöds av Azure Database for PostgreSQL. Den här informationen är också tillgänglig genom att köra `SELECT * FROM pg_available_extensions;` .

Versionerna av varje tillägg som installeras i en Server grupp skiljer sig ibland beroende på versionen av PostgreSQL (11, 12 eller 13). De tabeller som listar tilläggs versioner per databas version.

### <a name="citus-extension"></a>Citus-tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus distribuerad databas. | 9,5-1 | 9,5-1 | 10.0-2 |

### <a name="data-types-extensions"></a>Tillägg för data typer

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Innehåller en Skift läges okänslig tecken sträng typ. | 1.5 | 1.6 | 1.6 |
> | [kuben](https://www.postgresql.org/docs/current/static/cube.html) | Tillhandahåller en datatyp för flerdimensionella kuber. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Tillhandahåller en HyperLogLog-datastruktur. | 2,14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Tillhandahåller en datatyp för lagring av uppsättningar med nyckel/värde-par. | 1.5 | 1.6 | 1.7 |
> | [inte är](https://www.postgresql.org/docs/current/static/isn.html) | Tillhandahåller data typer för internationella standarder för produkt nummer. | 1.2 | 1.2 | 1.2 |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Stort objekt underhåll. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Tillhandahåller en datatyp för hierarkiska träd strukturer. | 1.1 | 1,1 | 1,2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datatyp som representerar linje segment eller flytt ALS intervall. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Data typen för en online-ackumulering av rankad statistik, till exempel quantiles och trimmade medel. | 1.0 | 1.0 | 1.0 |
> | [TOPN](https://github.com/citusdata/postgresql-topn/) | Typ för Top-n-JSONB. | 2.2.2 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Full texts öknings tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Innehåller en mall för text Sök lexikon för heltal. | 1.0 | 1.0 | 1.0 |
> | [dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Mall för text Sök lexikon för utökad synonym bearbetning. | 1.0 | 1.0 | 1.0 |
> | [avaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | En text Sök ord lista som tar bort accenttecken (dia kritiska tecken) från lexemes. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Funktions tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktioner för att autoöka fält. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Ger ett sätt att beräkna fantastiska avstånd på jordens yta. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Innehåller flera funktioner som avgör likheter och avstånd mellan strängar. | 1.1 | 1.1 | 1.1 |
> | [infoga \_ användar namn](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktioner för att spåra vem som har ändrat en tabell. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Heltals Aggregator och uppräknare (föråldrad). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Tillhandahåller funktioner och operatorer för att ändra null-fria matriser med heltal. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktioner för att spåra senaste ändrings tid. | 1.0 | 1.0 | 1.0 |
> | [PG \_ part](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller efter tid eller ID. | 4.1 | 4.4.1 | 4.4.1 |
> | [PG- \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Tillhandahåller funktioner och operatorer för att fastställa likheten mellan alfanumerisk text baserat på trigram matchning. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktioner för att implementera referens integritet (föråldrad). | 1.0 | 1.0 | 1.0 |
> | analys av sessioner \_ | Funktioner för att skicka frågor till hstore-matriser. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Innehåller funktioner som ändrar hela tabeller, inklusive kors. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Utlösta ändrings meddelanden. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktioner för att implementera res tid. | 1.0 | | |
> | [UUID – ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genererar universellt unika identifierare (UUID). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Tillägg för index typer

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [hinna](https://www.postgresql.org/docs/current/bloom.html) | Anslutnings metod för blomma – Signature filbaserat index. | 1.0 | 1.0 | 1.0 |
> | [b \_ gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Tillhandahåller exempel på GIN operator klasser som implementerar B-träd-liknande beteenden för vissa data typer. | 1.3 | 1.3 | 1.3 |
> | [b \_ register](https://www.postgresql.org/docs/current/static/btree-gist.html) | Tillhandahåller klasser för registrerings index operatorer som implementerar B-träd. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Språk tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL-inbelastnings förfarande språk. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpaket](https://www.postgresql.org/docs/current/adminpack.html) | Administrativa funktioner för PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funktioner för att verifiera Relations integritet. | 1,1 | 1,2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | En modul som stöder anslutningar till andra PostgreSQL-databaser inifrån en databas-session. Mer information om det här tillägget finns i avsnittet "dbLink och postgres_fdw". | 1.2 | 1.2 | 1.2 |
> | [fil \_ FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Sekundärt data omslag för Flat-filåtkomst. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Granska innehållet på databas sidor på en låg nivå. | 1.7 | 1.7 | 1.8 |
> | [PG- \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Ger ett sätt att undersöka vad som händer i det delade cacheminnet i real tid. | 1.3 | 1.3 | 1.3 |
> | [PG- \_ cron](https://github.com/citusdata/pg_cron) | Jobb schema för PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [PG- \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Kontrol lera kartan över ledigt utrymme (FSM). | 1.2 | 1.2 | 1.2 |
> | [PG- \_ förvarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Är ett sätt att läsa in relations data i bufferten. | 1.2 | 1.2 | 1.2 |
> | [\_rapporter om PG stat \_](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Ger ett sätt att spåra körnings statistik för alla SQL-uttryck som körs av en server. Mer information om det här tillägget finns i avsnittet pg_stat_statements. | 1.6 | 1.7 | 1.8 |
> | [PG- \_ synlighet](https://www.postgresql.org/docs/current/pgvisibility.html) | Granska Synlighets kartan (VM) och Synlighets information på sidnivå. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Ger möjlighet att Visa lås information på radnivå. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Ger ett sätt att visa statistik på tuple-nivå. | 1.5 | 1.5 | 1.5 |
> | [postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Sekundärt data omslag som används för att komma åt data som lagras i externa PostgreSQL-servrar. Mer information om det här tillägget finns i avsnittet "dbLink och postgres_fdw".| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Information om TLS/SSL-certifikat. | 1.2 | 1.2 | 1.2 |
> | [TSM \_ system \_ rader](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-metoden som accepterar antalet rader som en gräns. | 1.0 | 1.0 | 1.0 |
> | [TSM \_ system \_ tid](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-metoden som accepterar tiden i millisekunder som en gräns. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-frågor och XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS-tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [Postgis](https://www.postgis.net/), postgis \_ -topologi, postgis \_ Tiger \_ -kod, postgis \_ sfcgal | Spatialdata och geografiska objekt för PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | adress \_ standardiserare, adress \_ standardiserade \_ data \_ US | Används för att parsa en adress till komponent element. Används för att stödja kod normaliserings steg för den här adressen. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis \_ sfcgal | PostGIS SFCGAL-funktioner. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis \_ Tiger- \_ kod | PostGIS Tiger hårdkodad och omvänd landskod. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis- \_ topologi | Spatiala typer och funktioner för PostGIS-topologi. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Tillägget för PG \_ stat- \_ satser](https://www.postgresql.org/docs/current/pgstatstatements.html) är förinstallerat på alla Azure Database for postgresql server för att ge dig ett sätt att spåra KÖRNINGs statistik för SQL-uttryck.

Inställningen `pg_stat_statements.track` styr vilka instruktioner som räknas av tillägget. Den används som standard `top` , vilket innebär att alla instruktioner som utfärdas direkt av klienter spåras. De två andra spårnings nivåerna är `none` och `all` . Den här inställningen kan konfigureras som en server parameter via [Azure Portal](./howto-configure-server-parameters-using-portal.md) eller [Azure CLI](./howto-configure-server-parameters-using-cli.md).

Det är en kompromiss mellan information om körning av fråga pg_stat_statements tillhandahåller och påverkan på Server prestanda när varje SQL-sats loggas. Om du inte aktivt använder pg_stat_statements-tillägget rekommenderar vi att du ställer in `pg_stat_statements.track` på `none` . Vissa övervaknings tjänster från tredje part kan vara beroende av pg_stat_statements för att leverera frågor om prestanda insikter, så kontrol lera om detta är fallet för dig eller inte.

## <a name="dblink-and-postgres_fdw"></a>dbLink och postgres_fdw

Du kan använda dbLink och postgres \_ FDW för att ansluta från en postgresql-server till en annan, eller till en annan databas på samma server.  Den mottagande servern måste tillåta anslutningar från den sändande servern via brand väggen.  Om du vill använda dessa tillägg för att ansluta mellan Azure Database for PostgreSQL servrar eller citus-servergrupper, anger du **Tillåt att Azure-tjänster och-resurser får åtkomst till den här server gruppen (eller servern)** till på.  Du måste också aktivera den här inställningen om du vill använda tilläggen för att återgå till samma server.
Inställningen **Tillåt att Azure-tjänster och-resurser får åtkomst till den här server gruppen** finns på sidan Azure Portal för Server gruppen för citus-Server under **nätverk**.  För närvarande stöds inte utgående anslutningar från Azure Database for PostgreSQL enskild server och storskalig (citus), förutom för anslutningar till andra Azure Database for PostgreSQL servrar och citus-servergrupper ().
