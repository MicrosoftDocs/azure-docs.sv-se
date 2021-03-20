---
title: Skala ut Azure Database for PostgreSQL skalnings Server grupp
description: Skala ut Azure Database for PostgreSQL skalnings Server grupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779999"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Skala ut din Azure-Arc Enabled PostgreSQL Scale Server Group genom att lägga till fler arbetsnoder
Det här dokumentet beskriver hur du skalar ut en Azure Arc-aktiverad PostgreSQL-Server grupp. Det gör du genom att ta dig igenom ett scenario. **Om du inte vill köra genom scenariot och bara vill läsa om hur du skalar ut, hoppar du till stycket [skala ut](#scale-out)**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Kom igång
Om du redan är bekant med skalnings modellen för Azure Arc Enabled PostgreSQL storskalig eller Azure Database for PostgreSQL storskalig (citus) kan du hoppa över det här stycket. Om du inte gör det rekommenderar vi att du börjar med att läsa den här skalnings modellen på dokumentations sidan för Azure Database for PostgreSQL citus. Azure Database for PostgreSQL storskalig (citus) är samma teknik som är värd för en tjänst i Azure (plattform som en tjänst som även kallas PAAS) i stället för att erbjudas som en del av Azure Arc-aktiverade Data Services:
- [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
- [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
- [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
- [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* I dokumenten ovan hoppar du över avsnitten **Logga** in på Azure Portal, & **skapa en Azure Database for PostgreSQL-till-skala (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

## <a name="scenario"></a>Scenario
Det här scenariot avser den PostgreSQL-Server grupp som skapades som ett exempel i dokumentationen om att [skapa en Azure-Arc-aktiverad postgresql för storskalig Server grupp](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Läsa in testdata
Scenariot använder ett exempel på offentligt tillgängliga GitHub-data som är tillgängliga från [webbplatsen för citus data](https://www.citusdata.com/) (citus data ingår i Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Anslut till din Azure Arc-aktiverade PostgreSQL-Server grupp

##### <a name="list-the-connection-information"></a>Visa information om anslutningen
Anslut till din Azure Arc-aktiverade PostgreSQL-Server grupp genom att först hämta anslutnings informationen: det allmänna formatet för det här kommandot är
```console
azdata arc postgres endpoint list -n <server name>
```
Exempel:
```console
azdata arc postgres endpoint list -n postgres01
```

Exempel på utdata:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Anslut till det klient verktyg du väljer.

Kör följande fråga för att kontrol lera att du för närvarande har två (eller flera storskaliga arbetsnoder), som var och en motsvarar en Kubernetes pod:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Skapa ett exempel schema
Skapa två tabeller genom att köra följande fråga:

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

JSONB är JSON-datatypen i binär form i PostgreSQL. Den lagrar ett flexibelt schema i en enda kolumn och med PostgreSQL. Schemat kommer att ha ett GIN-index för att indexera varje nyckel och värde i det. Med ett GIN-index blir det snabbt och enkelt att fråga med olika villkor direkt på nytto lasten. Vi går vidare och skapar ett par index innan vi läser in våra data:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Kör en fråga för varje tabell för att Shard standard tabeller. Ange den tabell som vi vill Shard och den nyckel vi vill Shard den på. Vi kommer att Shard både händelse-och användar tabellen på user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Läsa in exempeldata
Läs in data med kopia... FRÅN PROGRAM:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Fråga efter data
Nu ska du mäta hur lång tid en enkel fråga tar med två noder:

```sql
SELECT COUNT(*) FROM github_events;
```
Anteckna körnings tiden för frågan.


## <a name="scale-out"></a>Skala ut
Det allmänna formatet för det skalbara kommandot är:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> Förhandsversionen stöder inte tillbakaskalning. Det går till exempel inte att minska antalet arbetsnoder än. Om du behöver göra det måste du extrahera/säkerhetskopiera data, släppa servergruppen, skapa en ny servergrupp med färre arbetsnoder och sedan importera data.

I det här exemplet ökar vi antalet arbetsnoder från 2 till 4 genom att köra följande kommando:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

När du lägger till noder och du ser ett väntande tillstånd för Server gruppen. Exempel:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

När noderna är tillgängliga körs skalnings funktionen för Shard automatiskt och data distribueras till de nya noderna. Den skalbara åtgärden är en online-åtgärd. Även om noderna läggs till och data distribueras om över noderna är data fortfarande tillgängliga för frågor.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Verifiera den nya formen på Server gruppen (valfritt)
Använd någon av metoderna nedan för att kontrol lera att Server gruppen nu använder ytterligare arbetsnoder som du har lagt till.

#### <a name="with-azdata"></a>Med azdata:
Kör kommandot:
```console
azdata arc postgres server list
```

Den returnerar listan över Server grupper som skapats i ditt namn område och anger antalet arbetsnoder. Exempel:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Med kubectl:
Kör kommandot:
```console
kubectl get postgresql-12
```

Den returnerar listan över Server grupper som skapats i ditt namn område och anger antalet arbetsnoder. Exempel:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Om du har skapat en Server grupp av version 11-PostgreSQL i stället för 12 kör du följande kommando i stället: _kubectl get postgresql-11_

#### <a name="with-a-sql-query"></a>Med en SQL-fr åga:
Anslut till din server grupp med det valda klient verktyget och kör följande fråga:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Gå tillbaka till scenariot

Om du vill jämföra körnings tiden för frågan Select Count mot exempel data uppsättningen använder du samma Count-fråga. Den kan användas i de fyra arbetsnoderna, utan några ändringar i SQL-instruktionen.

```sql
SELECT COUNT(*) FROM github_events;
```
Notera körnings tiden.


> [!NOTE]
> Beroende på din miljö – om du till exempel har distribuerat din test Server grupp med `kubeadm` på en enskild nod VM – kan du se en enkel förbättring av körnings tiden. För att få en bättre uppfattning om vilken typ av prestanda förbättringar du kan uppnå med Azure Arc Enabled PostgreSQL-storskalig, se följande korta videor:
>* [HTAP med hög prestanda med Azure PostgreSQL-storskalighet (citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Skapa HTAP-program med python & citus (Azure PostgreSQL storskalig)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [skalar upp och ned (minne, virtuella kärnor) din Azure Arc-aktiverade postgresql för storskalig Server grupp](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Läs om hur du ställer in Server parametrar i din Azure Arc Enabled PostgreSQL-Server grupp för storskaliga
- Läs koncepten och instruktions guiderna för Azure Database for PostgreSQL storskaligt för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av all kraften i Azure Database för postgres-skalning. :
    * [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
    * [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
    * [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* I dokumenten ovan hoppar du över avsnitten **Logga** in på Azure Portal, & **skapa en Azure Database for PostgreSQL-till-skala (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

- [Lagrings konfiguration och Kubernetes lagrings koncept](storage-configuration.md)
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
