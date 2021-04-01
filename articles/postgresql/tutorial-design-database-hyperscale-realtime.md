---
title: 'Självstudie: utforma en real tids instrument panel – storskalig skalning (citus) – Azure Database for PostgreSQL'
description: Den här självstudien visar hur du parallellisera frågor på instrument panelen i real tid med Azure Database for PostgreSQL storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026275"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Självstudie: utforma en instrument panel med real tids analys med Azure Database for PostgreSQL – storskalig (citus)

I den här självstudien använder du Azure Database for PostgreSQL-storskalig skalning (citus) för att lära dig att:

> [!div class="checklist"]
> * Skapa en Hyperscale-servergrupp (Citus)
> * Använd psql-verktyget för att skapa ett schema
> * Shard-tabeller över noder
> * Generera exempeldata
> * Utför samlade uppdateringar
> * Fråga rå data och sammanställda data
> * Förfaller data

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Använd psql-verktyget för att skapa ett schema

När du har anslutit till Azure Database for PostgreSQL skala (citus) med psql kan du utföra några grundläggande uppgifter. Den här självstudien vägleder dig genom att mata in trafik data från Web Analytics och sedan samla in data för att tillhandahålla instrument paneler i real tid baserat på dessa data.

Nu ska vi skapa en tabell som använder all vår rå data för webb trafik. Kör följande kommandon i psql-terminalen:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Vi ska också skapa en tabell som innehåller våra samlingarna per minut och en tabell som behåller positionen för den senaste sammanslagningen. Kör följande kommandon i psql också:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Du kan se de nyligen skapade tabellerna i listan med tabeller nu med detta psql-kommando:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Shard-tabeller över noder

En storskalig distribution lagrar tabell rader på olika noder baserat på värdet för en användardefinierad kolumn. Denna "distributions kolumn" markerar hur data ska shardade mellan noder.

Låt oss ställa in distributions kolumnen som plats \_ -ID, nyckeln Shard. I psql kör du följande funktioner:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Generera exempeldata

Nu bör vår server grupp vara redo att mata in vissa data. Vi kan köra följande lokalt från vår `psql` anslutning för att infoga data kontinuerligt.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Frågan infogar ungefär åtta rader varje sekund. Raderna lagras på olika arbetsnoder enligt anvisningarna i kolumnen distribution `site_id` .

   > [!NOTE]
   > Lämna frågan för generering av data som körs och öppna en andra psql-anslutning för de återstående kommandona i den här självstudien.
   >

## <a name="query"></a>Fråga

Med alternativet för skalnings värd kan flera noder bearbeta frågor parallellt för hastighet. Databasen beräknar till exempel agg regeringar som SUM och COUNT på arbetsnoder och kombinerar resultaten till ett slutligt svar.

Här är en fråga om att räkna webb förfrågningar per minut tillsammans med få statistik.
Prova att köra det i psql och observera resultatet.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Rensar data

Den föregående frågan fungerar fint i tidiga faser, men dess prestanda försämras i takt med att dina data skalas. Även om distribuerad bearbetning är det snabbare att förberäkna data än att beräkna om dem upprepade gånger.

Vi kan se till att vår instrument panel hålls fast genom att regelbundet rensa rå data i en sammanslagen tabell. Du kan experimentera med agg regerings tiden. Vi använde en agg regerings tabell per minut, men du kan dela upp data i 5, 15 eller 60 minuter i stället.

För att kunna köra den här sammanfattningen är det enklare att publicera den i en plpgsql-funktion. Kör dessa kommandon i psql för att skapa `rollup_http_request` funktionen.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Med vår funktion på plats kan du köra den för att samla in data:

```sql
SELECT rollup_http_request();
```

Och med våra data i ett församlat formulär kan vi skicka en fråga till sammanslagnings tabellen för att hämta samma rapport som tidigare. Kör följande fråga:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Gamla data förfaller

Sammanslagningarna gör frågor snabbare, men vi måste fortfarande förfalla gamla data för att undvika obegränsade lagrings kostnader. Bestäm hur länge du vill behålla data för varje kornig het och Använd standard frågor för att ta bort inaktuella data. I följande exempel beslutade vi att behålla rå data i en dag och agg regeringar per minut i en månad:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

I produktion kan du figursätta dessa frågor i en funktion och anropa dem varje minut i ett cron-jobb.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du etablerar en Server grupp för storskaliga (citus). Du är ansluten till den med psql, skapat ett schema och distribuerade data. Du har lärt dig att fråga data i rå data, samla in data, fråga de sammanställda tabellerna och förfalla gamla data.

- Lär dig mer om [Node-typer](./concepts-hyperscale-nodes.md) för Server grupper
- Fastställ bästa [initial storlek](howto-hyperscale-scale-initial.md) för Server gruppen
