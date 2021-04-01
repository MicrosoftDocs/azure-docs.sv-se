---
title: 'Självstudie: utforma en databas för flera innehavare (citus) – Azure Database for PostgreSQL'
description: I den här självstudien visas hur du får ett skalbart program med flera klienter med Azure Database for PostgreSQL storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026207"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Självstudie: utforma en databas med flera innehavare med Azure Database for PostgreSQL – storskalig (citus)

I den här självstudien använder du Azure Database for PostgreSQL-storskalig skalning (citus) för att lära dig att:

> [!div class="checklist"]
> * Skapa en Hyperscale-servergrupp (Citus)
> * Använd psql-verktyget för att skapa ett schema
> * Shard-tabeller över noder
> * Mata in exempeldata
> * Fråga klient data
> * Dela data mellan klienter
> * Anpassa schemat per klient

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Använd psql-verktyget för att skapa ett schema

När du har anslutit till Azure Database for PostgreSQL skala (citus) med psql kan du utföra några grundläggande uppgifter. Den här självstudien vägleder dig genom att skapa en webbapp som gör att annonsörer kan spåra sina kampanjer.

Flera företag kan använda appen så vi skapar en tabell för att lagra företag och en annan för deras kampanjer. I psql-konsolen kör du följande kommandon:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Varje kampanj kommer att betala för att köra annonser. Lägg till en tabell för Ads, genom att köra följande kod i psql efter koden ovan:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Slutligen ska vi spåra statistik om klickningar och exponeringar för varje annons:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Du kan se de nyligen skapade tabellerna i listan över tabeller nu i psql genom att köra:

```postgres
\dt
```

Program med flera klienter kan endast framtvinga unik användning per klient, vilket är anledningen till att alla primära och externa nycklar innehåller företags-ID.

## <a name="shard-tables-across-nodes"></a>Shard-tabeller över noder

En storskalig distribution lagrar tabell rader på olika noder baserat på värdet för en användardefinierad kolumn. Denna "distributions kolumn" markerar vilken klient som äger vilka rader.

Vi anger att distributions kolumnen ska vara företags \_ -ID, klient-ID. I psql kör du följande funktioner:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Mata in exempeldata

Utanför psql nu kan du hämta exempel data uppsättningar på den vanliga kommando raden:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Tillbaka i psql, Mass inläsning av data. Se till att köra psql i samma katalog som du laddade ned datafilerna till.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Dessa data kommer nu att spridas över arbetsnoder.

## <a name="query-tenant-data"></a>Fråga klient data

När programmet begär data för en enskild klient kan databasen köra frågan på en enskild arbetsnod. Frågor med en enda klient fråga filtreras efter ett enda klient-ID. Följande fråga filter till exempel `company_id = 5` för annonsering och exponering. Prova att köra det i psql för att se resultatet.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Dela data mellan klienter

Fram till nu har alla tabeller distribuerats `company_id` , men vissa data är inte naturligt "tillhör" en klient i synnerhet och kan delas. Till exempel kanske alla företag i exemplet AD-plattformen vill hämta geografisk information för deras mål grupper baserat på IP-adresser.

Skapa en tabell för att lagra delad geografisk information. Kör följande kommandon i psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Sedan gör `geo_ips` du en "referens tabell" för att lagra en kopia av tabellen på varje arbetsnod.

```sql
SELECT create_reference_table('geo_ips');
```

Läs in den med exempel data. Kom ihåg att köra det här kommandot i psql inifrån den katalog där du laddade ned data uppsättningen.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Att gå med i klicknings tabellen med geo- \_ IP: er är effektiv på alla noder.
Här är en koppling för att hitta platserna för alla som klickar på AD
290. Prova att köra frågan i psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Anpassa schemat per klient

Varje klient kan behöva lagra särskild information som inte behövs av andra. Alla klienter delar dock en gemensam infrastruktur med ett identiskt databas schema. Var kan extra data gå?

Ett stick är att använda en kolumn typ med öppen slut som PostgreSQL-JSONB.  Vårt schema har ett JSONB-fält i `clicks` kallas `user_data` .
Ett företag (till exempel företag fem) kan använda kolumnen för att spåra om användaren finns på en mobil enhet.

Här är en fråga för att hitta vem som klickar på mer: mobil eller traditionell besökare.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Vi kan optimera den här frågan för ett enskilt företag genom att skapa ett [partiellt index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

I allmänhet kan vi skapa en [gin index](https://www.postgresql.org/docs/current/static/gin-intro.html) på varje nyckel och värde i kolumnen.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du etablerar en Server grupp för storskaliga (citus). Du är ansluten till den med psql, skapat ett schema och distribuerade data. Du har lärt dig att fråga data både inom och mellan klienter och att anpassa schemat per klient.

- Lär dig mer om [Node-typer](./concepts-hyperscale-nodes.md) för Server grupper
- Fastställ bästa [initial storlek](howto-hyperscale-scale-initial.md) för Server gruppen
