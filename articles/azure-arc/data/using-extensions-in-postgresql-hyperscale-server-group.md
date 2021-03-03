---
title: Använd PostgreSQL-tillägg
description: Använd PostgreSQL-tillägg
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6586375d7db71274f40eb62aeb24f9daad0d7c2e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688305"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Använd PostgreSQL-tillägg i Azure Arc-aktiverade PostgreSQL för storskalig Server grupp

PostgreSQL är på bästa sätt när du använder den med tillägg. I själva verket är ett viktigt-element i våra egna funktioner i den Microsoft-baserade `citus` tillägget som installeras som standard, vilket gör att postgres kan Shard data transparent över flera noder.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Tillägg som stöds
Standard [`contrib`](https://www.postgresql.org/docs/12/contrib.html) tilläggen och följande tillägg har redan distribuerats i behållare för din Azure-Arc-aktiverad postgresql-server grupp:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. Citus-tillägget av [citus-data](https://www.citusdata.com/) läses in som standard eftersom den ger storskalig funktionalitet till postgresql-motorn. Det finns inte stöd för att släppa citus-tillägget från din Azure Arc PostgreSQL-skalnings Server grupp.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Uppdateringar av den här listan publiceras när de utvecklas över tid.

> [!IMPORTANT]
> Även om du kan ansluta till Server gruppen andra än de som anges ovan, i den här för hands versionen, kommer de inte att sparas i systemet. Det innebär att den inte kommer att vara tillgänglig efter omstart av systemet och du måste ta den igen.

Den här guiden tar i ett scenario att använda två av dessa tillägg:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Vilka tillägg måste läggas till i shared_preload_libraries och skapas?

|Tillägg   |Måste läggas till shared_preload_libraries  |Måste skapas |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Inga       |Ja        |
|`pg_audit`     |Ja       |Ja        |
|`plpgsql`      |Ja       |Ja        |
|`postgis`      |Inga       |Ja        |
|`plv8`      |Inga       |Ja        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Lägg till tillägg i shared_preload_libraries
Mer information om shared_preload_libraries finns i PostgreSQL-dokumentationen [här](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Det här steget behövs inte för de tillägg som ingår i `contrib`
- Det här steget krävs inte för tillägg som inte krävs för inläsning av shared_preload_libraries. För dessa tillägg kan du gå vidare nästa stycke [skapa tillägg](https://docs.microsoft.com/azure/azure-arc/data/using-extensions-in-postgresql-hyperscale-server-group#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Lägg till ett tillägg när du skapar en server grupps skapelse tid
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Lägg till ett tillägg till en instans som redan finns
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Visa listan över tillägg som lagts till shared_preload_libraries
Kör något av följande kommando.

### <a name="with-an-azdata-cli-command"></a>Med ett azdata CLI-kommando
```console
azdata arc postgres server show -n <server group name>
```
Bläddra i utdata och Lägg märke till engine\extensions-avsnitten i specifikationerna för din server grupp. Exempel:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Med kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Bläddra i utdata och Lägg märke till engine\extensions-avsnitten i specifikationerna för din server grupp. Exempel:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Skapa tillägg
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Visa listan över tillägg som skapats
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Släpp ett tillägg
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`Tillägget
Du behöver inte lägga till `PostGIS` tillägget i `shared_preload_libraries` .
Hämta [exempel data](http://duspviz.mit.edu/tutorials/intro-postgis/) från MIT-avdelningen i tätorts studier & planera. Kör `apt-get install unzip` för att installera zippa efter behov.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Nu ska vi ansluta till vår databas och skapa `PostGIS` tillägget:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Om du vill använda ett tillägg i `postgis` paketet (till exempel,, `postgis_raster` `postgis_topology` .. `postgis_sfcgal` `fuzzystrmatch` .) måste du först skapa postgis-tillägget och sedan skapa det andra tillägget. Till exempel: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

Och skapa schemat:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nu kan vi kombinera med funktionen för `PostGIS` skalbarhet, genom att göra den coffee_shops tabellen distribuerad:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vi läser in några data:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Och fyll i `geom` fältet med korrekt kodad latitud och longitud i `PostGIS` `geometry` data typen:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nu kan vi visa en lista över de kaffe butiker som är närmast MIT (77 Massachusetts Ara vid 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`Tillägget

Nu ska vi aktivera `pg_cron` vår postgresql-server grupp genom att lägga till den i shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Server gruppen kommer att startas om och installationen av tilläggen slutförs. Det kan ta 2 till 3 minuter.

Nu kan vi ansluta igen och skapa `pg_cron` tillägget:

```sql
CREATE EXTENSION pg_cron;
```

I test syfte kan du skapa en tabell `the_best_coffee_shop` som tar ett slumpmässigt namn från vår tidigare `coffee_shops` tabell och infogar tabell innehållet:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Vi kan använda `cron.schedule` plus några SQL-uttryck för att få ett slumpmässigt tabell namn (Observera att en temporär tabell används för att lagra ett resultat för en distribuerad fråga) och lagra den i `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Och nu får vi ett annat namn på en minut:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Se [PG_CRON viktigt](https://github.com/citusdata/pg_cron) om du vill ha fullständig information om syntaxen.


## <a name="next-steps"></a>Nästa steg
- Läs dokumentationen om [`plv8`](https://plv8.github.io/)
- Läs dokumentationen om [`PostGIS`](https://postgis.net/)
- Läs dokumentationen om [`pg_cron`](https://github.com/citusdata/pg_cron)
