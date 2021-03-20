---
title: Citus (Table-interlocation-Scale) – Azure Database for PostgreSQL
description: Så här lagrar du relaterad information tillsammans för snabbare frågor
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90884434"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Tabell-samplacering i Azure Database for PostgreSQL – storskalig (citus)

Samplacering innebär att lagra relaterad information tillsammans på samma noder. Frågor kan gå snabbt när alla nödvändiga data är tillgängliga utan nätverks trafik. Att placera relaterade data på olika noder gör att frågor kan köras effektivt parallellt på varje nod.

## <a name="data-colocation-for-hash-distributed-tables"></a>Data samplacering för hash-distribuerade tabeller

I Azure Database for PostgreSQL – storskalig (citus) lagras en rad i en Shard om hash-värdet för värdet i kolumnen distribution ligger inom Shard hash-intervall. Shards med samma hash-intervall placeras alltid på samma nod. Rader med samma distributions kolumn värden är alltid på samma nod i tabeller.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Shards":::

## <a name="a-practical-example-of-colocation"></a>Ett användbart exempel på samplacering

Tänk på följande tabeller som kan vara en del av en webb analys SaaS för flera innehavare:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Nu vill vi besvara frågor som kan utfärdas av en kund riktad instrument panel. En exempel fråga är "returnera antalet besök den senaste veckan för alla sidor som börjar med"/Blog "i klient organisationen sex."

Om våra data fanns i Single-Server distributions alternativet kunde vi enkelt uttrycka vår fråga med hjälp av den omfattande uppsättningen Relations åtgärder som erbjuds av SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Så länge som [arbets](https://en.wikipedia.org/wiki/Working_set) minnet för den här frågan passar i minnet, är en tabell med en server en lämplig lösning. Nu ska vi ta del av möjligheterna att skala data modellen med distributions alternativet för storskaliga (citus).

### <a name="distribute-tables-by-id"></a>Distribuera tabeller efter ID

Frågor med en server börjar saktas ned när antalet klienter och de data som lagras för varje klient växer. Arbets minnet avslutar anpassning i minnet och CPU blir en Flask hals.

I det här fallet kan vi Shard data över flera noder med hjälp av skalning (citus). Det första och viktigaste valet vi behöver göra när vi bestämmer dig för Shard är distributions kolumnen. Vi börjar med ett naïve val av att använda `event_id` för händelse tabellen och `page_id` för `page` tabellen:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

När data sprids över olika arbetare kan vi inte utföra en koppling som vi skulle göra på en enskild PostgreSQL-nod. I stället måste vi utfärda två frågor:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Därefter måste resultatet från de två stegen kombineras av programmet.

Att köra frågorna måste se till att data i Shards är spridda över noderna.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="Ineffektiva frågor":::

I det här fallet skapar data distributionen avsevärda nack delar:

-   Omkostnader från att fråga varje Shard och köra flera frågor.
-   Overhead av Q1 returnerar många rader till klienten.
-   K2 blir större.
-   Behovet av att skriva frågor i flera steg kräver ändringar i programmet.

Data sprids, så att frågorna kan vara parallella. Det är bara fördelaktigt om mängden arbete som frågan gör är betydligt större än omkostnaderna för att fråga många Shards.

### <a name="distribute-tables-by-tenant"></a>Distribuera tabeller efter klient organisation

I storskaliga (citus) garanteras att rader med samma distributions kolumn värde är på samma nod. Från och med kan vi skapa våra tabeller med `tenant_id` som distributions kolumn.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nu kan du svara på den ursprungliga en server fråga utan ändringar (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

På grund av filter och koppling på tenant_id vet citus (disscale) att hela frågan kan besvaras med hjälp av uppsättningen samplacerade Shards som innehåller data för den aktuella klienten. En enskild PostgreSQL-nod kan besvara frågan i ett enda steg.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="Bättre fråga":::

I vissa fall måste frågor och tabell scheman ändras så att de inkluderar klient-ID i unika begränsningar och kopplings villkor. Den här ändringen är vanligt vis enkel.

## <a name="next-steps"></a>Nästa steg

- Se hur klient data befinner sig i [själv studie kursen för flera innehavare](tutorial-design-database-hyperscale-multi-tenant.md).
