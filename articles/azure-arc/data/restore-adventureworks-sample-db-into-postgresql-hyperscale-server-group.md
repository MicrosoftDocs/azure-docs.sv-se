---
title: Återställa AdventureWorks-exempel databasen till Azure Arc Enabled PostgreSQL-skalning
description: Återställa AdventureWorks-exempel databasen till Azure Arc Enabled PostgreSQL-skalning
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b1ee779be118fcafd0efa2bd2718ece1c34c50d1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954336"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Återställa AdventureWorks-exempel databasen till Azure Arc Enabled PostgreSQL-skalning

[AdventureWorks](/sql/samples/adventureworks-install-configure) är en exempel databas som innehåller en OLTP-databas som används i självstudier och exempel. Den tillhandahålls och underhålls av Microsoft som en del av [SQL Server-GitHub-lagringsplatsen](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Ett projekt med öppen källkod har konverterat AdventureWorks-databasen så att den är kompatibel med Azure Arc Enabled PostgreSQL-skalning.
- [Ursprungligt projekt](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Följ på Project som förkonverterar CSV-filerna så att de är kompatibla med PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

Det här dokumentet beskriver en enkel process för att hämta AdventureWorks-exempel databasen som återställs till din PostgreSQL-Server grupp för storskalig skalning.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Hämta säkerhets kopie filen AdventureWorks

Ladda ned AdventureWorks. SQL-filen till PostgreSQL för skalnings gruppen för Server gruppen. I det här exemplet ska vi använda `kubectl exec` kommandot för att fjärrköra ett kommando i behållare för postgresql-ProScale Server grupp för att ladda ned filen till behållaren. Du kan ladda ned den här filen från valfri plats som är tillgänglig för `curl` . Använd den här metoden om du har andra databas säkerhets kopierings filer som du vill hämta i PostgreSQL-behållare för Server gruppen. När det är i PostgreSQL-behållaren för Server gruppen för den storskaliga servern är det enkelt att skapa databasen, schemat och fylla i data.

Kör ett kommando som detta för att ladda ned filerna Ersätt värdet för Pod namn och namn områdes namn innan du kör det:

> [!NOTE]
>  Din behållare måste ha Internet anslutning över 443 för att kunna hämta filen från GitHub.

> [!NOTE]
>  Använd Pod namn för koordinator-noden i postgres-ProScale Server-gruppen. Namnet är <server group name> -0.  Om du inte är säker på pod-namnet kör du kommandot `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>Steg 2: återställa AdventureWorks-databasen

På samma sätt kan du köra ett kubectl exec-kommando för att använda psql CLI-verktyget som ingår i PostgreSQL för Server grupp behållare för för att skapa och läsa in databasen.

Kör ett kommando som detta för att skapa den tomma databasen och Ersätt värdet för Pod-namnet och namn områdets namn innan du kör det.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Kör sedan ett kommando som detta för att återställa databasen som ersätter värdet för Pod-namnet och namn områdets namn innan du kör det.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Obs! Du kan inte se så mycket prestanda för delar som körs på Azure Arc Enabled PostgreSQL-skalning tills du skalar ut och du Shard/distribuerar data/tabeller på arbetsnoderna i PostgreSQL-gruppen för hög skalnings Server. Se [förslag på nästa steg](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Föreslagna nästa steg
- Läs begrepp och instruktions guider för Azure Database for PostgreSQL storskalig för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av all kraften i Azure Database for PostgreSQL storskalig. :
    * [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
    * [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
    * [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* I dokumenten ovan hoppar du över avsnitten **Logga** in på Azure Portal, & **skapa en Azure Database for PostgreSQL-till-skala (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

- [Skala ut din Azure Database for PostgreSQL Hyperscale-servergrupp](scale-out-postgresql-hyperscale-server-group.md)
