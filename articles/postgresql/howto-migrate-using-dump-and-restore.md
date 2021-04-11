---
title: Dumpa och Återställ-Azure Database for PostgreSQL-enskild server
description: Du kan extrahera en PostgreSQL-databas till en dumpfil. Sedan kan du återställa från en fil som skapats av pg_dump i Azure Database for PostgreSQL enskild server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450063"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Migrera din PostgreSQL-databas med hjälp av dumpa och Återställ
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Du kan använda [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) för att extrahera en PostgreSQL-databas till en dumpfil. Använd sedan [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) för att återställa PostgreSQL-databasen från en arkivfil som skapats av `pg_dump` .

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md), inklusive brand Väggs regler för att tillåta åtkomst.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kommando rads verktyg är installerade.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Skapa en dumpfil som innehåller de data som ska läsas in

Om du vill säkerhetskopiera en befintlig PostgreSQL-databas lokalt eller i en virtuell dator kör du följande kommando:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Om du till exempel har en lokal server och en databas med namnet **testdb** , kör du:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Återställa data till mål databasen

När du har skapat mål databasen kan du använda `pg_restore` kommandot och  `--dbname` parametern för att återställa data till mål databasen från dumpfilen.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Inklusive- `--no-owner` parametern gör att alla objekt som skapas under återställningen ägs av den användare som anges i `--username` . Mer information finns i postgresql- [dokumentationen](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> På Azure Database for PostgreSQL-servrar är TLS/SSL-anslutningar aktiverat som standard. Om din PostgreSQL-Server kräver TLS/SSL-anslutningar, men inte har dem, ställer du in en miljö variabel `PGSSLMODE=require` så att verktyget pg_restore ansluter med TLS. Utan TLS kan felet läsa: "OÅTERKALLELIG: SSL-anslutning krävs. Ange SSL-alternativ och försök igen. " På kommando raden i Windows kör du kommandot `SET PGSSLMODE=require` innan du kör `pg_restore` kommandot. I Linux eller bash kör du kommandot `export PGSSLMODE=require` innan du kör `pg_restore` kommandot. 
>

I det här exemplet återställer du data från dump-filen **testdb. dump** till databasen **mypgsqldb** på mål servern **mydemoserver.postgres.Database.Azure.com**.

Här är ett exempel på hur du använder det här `pg_restore` för en enskild server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Här är ett exempel på hur du kan använda detta `pg_restore` för flexibel Server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Optimera migreringsprocessen

Ett sätt att migrera den befintliga PostgreSQL-databasen till Azure Database for PostgreSQL är att säkerhetskopiera databasen på källan och återställa den i Azure. Överväg att använda följande parametrar med kommandona för säkerhets kopiering och återställning för att minimera tiden som krävs för att slutföra migreringen.

> [!NOTE]
> Detaljerad information om syntaxen finns i [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>För säkerhets kopieringen

Ta säkerhets kopian med `-Fc` växeln, så att du kan utföra återställningen parallellt för att göra den snabbare. Exempel:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>För återställning

- Flytta säkerhets kopian till en virtuell Azure-dator i samma region som den Azure Database for PostgreSQL server som du migrerar till. Utför `pg_restore` från den virtuella datorn för att minska nätverks fördröjningen. Skapa den virtuella datorn med [accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) aktiverat.

- Öppna dumpfilen för att kontrol lera att Create index-instruktionerna är efter infogningen av data. Om så inte är fallet flyttar du Create index-instruktionerna när data har infogats. Detta bör redan göras som standard, men det är en bra idé att bekräfta.

- Återställ med växlarna `-Fc` och `-j` (med ett nummer) för att parallellisera återställningen. Antalet som du anger är antalet kärnor på mål servern. Du kan också ange till två gånger antalet kärnor på mål servern för att se effekten.

    Här är ett exempel på hur du använder det här `pg_restore` för en enskild server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Här är ett exempel på hur du kan använda detta `pg_restore` för flexibel Server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- Du kan också redigera dumpfilen genom att lägga till kommandot `set synchronous_commit = off;` i början och kommandot `set synchronous_commit = on;` i slutet. Om du inte aktiverar den i slutet innan apparna ändrar data kan det leda till efterföljande förlust av data.

- På mål Azure Database for PostgreSQL servern kan du göra följande innan du återställer:
    
  - Inaktivera prestanda spårning av frågor. Den här statistiken behövs inte under migreringen. Du kan göra detta genom att ställa in `pg_stat_statements.track` , `pg_qs.query_capture_mode` och `pgms_wait_sampling.query_capture_mode` till `NONE` .

  - Använd en hög beräknings-och hög minnes-SKU, till exempel 32 vCore-minne som är optimerat, för att påskynda migreringen. Du kan enkelt skala tillbaka till den önskade SKU: n när återställningen har slutförts. Ju högre SKU, desto mer parallellitet kan du uppnå genom att öka motsvarande `-j` parameter i `pg_restore` kommandot.

  - Mer IOPS på mål servern kan förbättra prestandan för återställning. Du kan etablera mer IOPS genom att öka serverns lagrings storlek. Den här inställningen kan inte ångras, men fundera över om en högre IOPS skulle gynna din faktiska arbets belastning i framtiden.

Kom ihåg att testa och validera dessa kommandon i en test miljö innan du använder dem i produktion.

## <a name="next-steps"></a>Nästa steg

- Om du vill migrera en PostgreSQL-databas med hjälp av export och import, se [migrera din PostgreSQL-databas med export och import](howto-migrate-using-export-and-import.md).
- Mer information om hur du migrerar databaser till Azure Database for PostgreSQL finns i [Guide för databas migrering](https://aka.ms/datamigration).


