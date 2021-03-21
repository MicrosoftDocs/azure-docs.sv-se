---
title: Migrera en databas – Azure Database for PostgreSQL-enskild server
description: Beskriver hur du extraherar en PostgreSQL-databas till en skript fil och importerar data till mål databasen från den filen.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d0f1824a1e4754dddea26484c836df79e95246d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580988"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrera din PostgreSQL-databas med export och import
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Du kan använda [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) för att extrahera en PostgreSQL-databas till en skript fil och [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att importera data till mål databasen från den filen.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md) med brand Väggs regler för att tillåta åtkomst och databas under den.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) kommando rads verktyg installerat
- kommando rads verktyget [psql](https://www.postgresql.org/docs/current/static/app-psql.html) installerat

Följ dessa steg om du vill exportera och importera PostgreSQL-databasen.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Skapa en skript fil med pg_dump som innehåller de data som ska läsas in
Om du vill exportera din befintliga PostgreSQL-databas lokalt eller i en virtuell dator till en SQL-skriptfil kör du följande kommando i din befintliga miljö:

```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Om du till exempel har en lokal server och en databas med namnet **testdb** :
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importera data på mål Azure Database for PostgreSQL
Du kan använda kommando raden psql och parametern--dbname (-d) för att importera data till den Azure Database for PostgreSQL servern och läsa in data från SQL-filen.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
I det här exemplet används psql-verktyget och en skript fil med namnet **testdb. SQL** från föregående steg för att importera data till databasen **mypgsqldb** på mål servern **mydemoserver.postgres.Database.Azure.com**.

Använd det här kommandot för **en enskild server** 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

Använd det här kommandot för **flexibel Server**  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Nästa steg
- Information om hur du migrerar en PostgreSQL-databas med dump och Restore finns i [migrera PostgreSQL-databasen med dump och Restore](howto-migrate-using-dump-and-restore.md).
- Mer information om hur du migrerar databaser till Azure Database for PostgreSQL finns i [Guide för databas migrering](https://aka.ms/datamigration).
