---
title: Ansluta och ställa frågor till flexibel Server PostgreSQL
description: Länkar till snabb starter som visar hur du ansluter till din Azure Database for PostgreSQL flexibla Server och kör frågor.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364611"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Ansluta och fråga översikt för Azure Database for PostgreSQL-flexibel Server

Följande dokument innehåller länkar till exempel som visar hur du ansluter och frågar med Azure Database for PostgreSQL enskild server. Den här guiden innehåller även TLS-rekommendationer och tillägg som du kan använda för att ansluta till servern i språk som stöds nedan.

>[!IMPORTANT]
> Azure Database for PostgreSQL flexibel Server är i för **hands version**.

## <a name="quickstarts"></a>Snabbstarter

| Snabbstart | Beskrivning |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Du kan använda pgAdmin för att ansluta till servern och fören klar skapandet, underhållet och användningen av databas objekt.|
|[psql i Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Den här artikeln visar hur du kör [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) i [Azure Cloud Shell](../../cloud-shell/overview.md) för att ansluta till servern och sedan köra instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Du kan köra **psql** om det är installerat i utvecklings miljön|
|[Python](connect-python.md)|Den här snabb starten visar hur du använder python för att ansluta till en databas och hur du använder arbeta med databas objekt för att fråga efter data. |
|[Django med App Service](tutorial-django-app-service-postgres.md)|Den här självstudien visar hur du använder ruby för att skapa ett program för att ansluta till en databas och använda arbeta med databas objekt för att fråga data.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-överväganden för databas anslutning

Transport Layer Security (TLS) används av alla driv rutiner som Microsoft tillhandahåller eller stöder för att ansluta till databaser i Azure Database for PostgreSQL. Ingen särskild konfiguration krävs, men Använd TLS 1,2 för nyligen skapade servrar. Vi rekommenderar att du använder TLS 1,0 och 1,1 för att uppdatera TLS-versionen för dina servrar. Se [så här konfigurerar du TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>PostgreSQL-tillägg

PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg samlar flera SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. När tilläggen har lästs in i databasen fungerar de som inbyggda funktioner.

- [Postgres 12-tillägg](./concepts-extensions.md#postgres-12-extensions)
- [Postgres 11-tillägg](./concepts-extensions.md#postgres-11-extensions)
- [dbLink och postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Mer information finns i [så här använder du postgresql-tillägg på en flexibel Server](concepts-extensions.md).

## <a name="next-steps"></a>Nästa steg

- [Migrera data med dumpa och Återställ](../howto-migrate-using-dump-and-restore.md)
- [Migrera data med import och export](../howto-migrate-using-export-and-import.md)
