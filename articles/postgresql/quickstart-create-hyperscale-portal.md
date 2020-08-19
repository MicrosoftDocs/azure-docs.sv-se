---
title: 'Snabb start: skapa en Server grupp – storskalig (citus)-Azure Database for PostgreSQL'
description: Snabb start för att skapa och fråga distribuerade tabeller på Azure Database for PostgreSQL storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 977082b7f9055b90ee5c93913154934741d93772
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547706"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Snabb start: skapa en citus-servergrupp (för skalning) i Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabb starten visar hur du skapar en citus-servergrupp (Azure Database for PostgreSQL-Scale Scale) med hjälp av Azure Portal. Du kommer att utforska distribuerade data: horisontell partitionering tabeller över flera noder, mata in exempel data och köra frågor som körs på flera noder.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Skapa och distribuera tabeller

När du har anslutit till noden för den storskaliga koordinatorn med psql kan du utföra några grundläggande uppgifter.

Det finns tre typer av tabeller i storskaliga servrar:

- Distribuerade eller shardade tabeller (Sprid ut för att hjälpa till med skalning för prestanda och parallellisering)
- Referens tabeller (flera kopior finns kvar)
- Lokala tabeller (används ofta för interna administratörs tabeller)

I den här snabb starten fokuserar vi främst på distribuerade tabeller och bekanta dig med dem.

Data modellen som vi ska arbeta med är enkel: användar-och händelse data från GitHub. Händelser omfattar att skapa gaffel, git-incheckningar som är relaterade till en organisation och mer.

När du har anslutit via psql ska vi skapa våra tabeller. I psql-konsolen kör du:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`payload`Fältet i `github_events` har en JSONB-datatype. JSONB är JSON-datatypen i binär form i postgres. Datatype gör det enkelt att lagra ett flexibelt schema i en enda kolumn.

Postgres kan skapa ett `GIN` index av den här typen, vilket kommer att indexera varje nyckel och värde i den. Med ett index blir det snabbt och enkelt att fråga nytto lasten med olika villkor. Nu ska vi gå vidare och skapa ett par index innan vi läser in våra data. I psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Härnäst ska vi ta dessa postgres-tabeller på koordinator-noden och meddela att de kan Shard dem över arbets tagarna. För att göra det kör vi en fråga för varje tabell som anger nyckeln för att Shard den. I det aktuella exemplet ska vi Shard både händelse-och användar tabellen på `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Vi är redo att läsa in data. I psql kan du fortfarande ladda ned filerna:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Läs sedan in data från filerna i de distribuerade tabellerna:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Köra frågor

Nu är det dags för den roliga delen, som faktiskt kör några frågor. Vi börjar med en enkel `count (*)` för att se hur mycket data vi läst in:

```sql
SELECT count(*) from github_events;
```

Det fungerade snyggt. Vi kommer tillbaka till den sortens agg regering i en bit, men nu ska vi titta på några andra frågor. I kolumnen JSONB `payload` finns det en utmärkt mängd data, men den varierar beroende på händelse typ. `PushEvent` händelser innehåller en storlek som inkluderar antalet distinkta incheckningar för push. Vi kan använda den för att hitta det totala antalet incheckningar per timme:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Hittills har frågorna involverade GitHub \_ -händelserna exklusivt, men vi kan kombinera den här informationen med GitHub- \_ användare. Eftersom vi shardade både användare och händelser på samma identifierare ( `user_id` ), kommer raderna i båda tabellerna med matchande användar-ID att [samplaceras](concepts-hyperscale-colocation.md) på samma databasnoder och kan enkelt anslutas.

Om vi ansluter till `user_id` kan skalningen av anslutningen gå över till Shards för körning parallellt på arbetsnoder. Vi kan till exempel hitta de användare som skapade det största antalet databaser:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen **ta bort** på sidan **Översikt** för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig **borttagning** .

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du etablerar en Server grupp för storskaliga (citus). Du är ansluten till den med psql, skapat ett schema och distribuerade data.

Sedan följer du en självstudie för att bygga skalbara program för flera klienter.
> [!div class="nextstepaction"]
> [Utforma en databas för flera innehavare](https://aka.ms/hyperscale-tutorial-multi-tenant)
