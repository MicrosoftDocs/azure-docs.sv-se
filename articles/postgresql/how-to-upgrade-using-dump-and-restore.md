---
title: Uppgradera med dumpa och Återställ-Azure Database for PostgreSQL-enskild server
description: Beskriver metoder för att uppgradera offline med hjälp av dumpa och Återställ databaser för att migrera till en högre version Azure Database for PostgreSQL-en server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493669"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Uppgradera PostgreSQL-databasen med hjälp av dumpa och Återställ

Du kan uppgradera din PostgreSQL-server som distribueras i Azure Database for PostgreSQL-enskild server genom att migrera dina databaser till en högre huvud versions server med hjälp av följande metoder.
* **Offline** -Metod med postgresql [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) som medför stillestånds tid för att migrera data. Det här dokumentet beskriver den här metoden för uppgradering/migrering.
* **Online** -metod med [Database migration service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Med den här metoden kan du minska stillestånds tiden och hålla mål databasen synkroniserad med med källan och du kan välja när du vill klippa ut. Det finns dock några krav och begränsningar som kan åtgärdas med hjälp av DMS. Mer information finns i [dokumentation om DMS](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 Följande tabell innehåller några rekommendationer baserat på databasens storlek och scenarier.

| **Databas/scenario** | **Dumpa/Återställ (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Du har en liten databas och kan erbjuda drift stopp vid uppgradering  | X | |
| Små databaser (< 10 GB)  | X | X | 
| Liten – medels databaser (10 GB – 100 GB) | X | X |
| Stora databaser (> 100 GB) |  | X |
| Kan ge ett drift stopp vid uppgraderingen (oavsett databasens storlek) | X |  |
| Kan du hantera [krav för](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)DMS, inklusive en omstart? |  | X |
| Kan du undvika DDLs och ej loggade tabeller under uppgraderings processen? | |  X |

Den här guiden innehåller några metoder för offline-migrering och exempel som visar hur du kan migrera från käll servern till mål servern som kör en senare version av PostgreSQL.

> [!NOTE]
> PostgreSQL-dump och återställning kan utföras på många sätt. Du kan välja att migrera med hjälp av någon av de metoder som anges i den här hand boken eller välja alternativ som passar dina behov. För detaljerad dumpning och återställning av syntax med ytterligare parametrar, se artiklarna [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Krav för att använda dump och Restore med Azure Database for PostgreSQL
 
För att gå igenom den här instruktions guiden behöver du:

- En **käll** PostgreSQL-databas som kör 9,5, 9,6 eller 10 som du vill uppgradera
- En **mål** databas server för postgresql med önskad huvud version [Azure Database for postgresql server](quickstart-create-server-database-portal.md). 
- Ett PostgreSQL klient system som kör dump-och Restore-kommandon.
  - Det kan vara en Linux-eller Windows-klient med PostgreSQL installerat och har [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kommando rads verktyg är installerade. 
  - Du kan också använda [Azure Cloud Shell](https://shell.azure.com) eller genom att klicka på Azure Cloud Shell i meny fältet längst upp till höger i [Azure Portal](https://portal.azure.com). Du måste logga in på ditt konto `az login` innan du kör dump-och Restore-kommandona.
- Din PostgreSQL-klient använder helst i samma region som käll-och mål servrarna. 


## <a name="additional-details-and-considerations"></a>Ytterligare information och överväganden
- Du kan hitta anslutnings strängen för käll-och mål databaserna genom att klicka på anslutnings strängarna i portalen. 
- Du kan köra mer än en databas på servern. Du kan hitta listan över databaser genom att ansluta till käll servern och köra `\l` .
- Skapa motsvarande databaser i mål databas servern.
- Du kan hoppa över uppgradering `azure_maintenance` eller mall databaser.
- Se tabellerna ovan för att kontrol lera att databasen är lämplig för det här läget för migrering.
- Observera att tids gränsen för sessionen är längre än 20 minuter om du vill använda Azure Cloud Shell. Om databas storleken är < 10 GB kan du kanske slutföra uppgraderingen utan tids gräns för sessionen. Annars kan du behöva hålla sessionen öppen på annat sätt, till exempel trycka på <Enter> en tangent en gång om 10-15 minuter. 


## <a name="example-database-used-in-this-guide"></a>Exempel databas som används i den här guiden

I den här hand boken används följande käll-och mål servrar och databas namn för att illustrera med exempel.

 | **Beskrivning** | **Värde** |
 | ------- | ------- |
 | Käll Server (v 9.5) | pg-95.postgres.database.azure.com |
 | Käll databas | bench5gb |
 | Storlek på käll databas | 5 GB |
 | Käll användar namn | pg@pg-95 |
 | Mål server (V11) | pg-11.postgres.database.azure.com |
 | Mål databas | bench5gb |
 | Mål användar namn | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Uppgradera dina databaser med metoder för offline-migrering
Du kan välja att använda en av de metoder som beskrivs i det här avsnittet för dina uppgraderingar. Du kan använda följande tips när du utför aktiviteterna.

- Om du använder samma lösen ord för käll-och mål databasen kan du ange `PGPASSWORD=yourPassword` miljövariabeln.  Du behöver inte ange något lösen ord varje gång du kör kommandon som psql, pg_dump och pg_restore.  På samma sätt kan du konfigurera ytterligare variabler `PGUSER` , t `PGSSLMODE` . ex., osv. se postgresql- [miljövariabler](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Om din PostgreSQL-Server kräver TLS/SSL-anslutningar (som standard i Azure Database for PostgreSQL-servrar) anger du en miljö variabel `PGSSLMODE=require` så att pg_restore-verktyget ansluter med TLS. Utan TLS kan felet läsa  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- På kommando raden i Windows kör du kommandot `SET PGSSLMODE=require` innan du kör kommandot pg_restore. I Linux eller bash kör du kommandot `export PGSSLMODE=require` innan du kör kommandot pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Metod 1: Migrera med dump-fil

Den här metoden omfattar två steg. Börja med att skapa en dump från käll servern. Det andra steget är att återställa dumpfilen-filen till mål servern. Mer information finns i [migrera med dump-och Restore](howto-migrate-using-dump-and-restore.md) -dokumentation. Detta är den rekommenderade metoden om du har stora databaser och klient systemet har tillräckligt med lagrings utrymme för att lagra dumpfilen.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Metod 2: Migrera med direkt uppspelning av Dumpdata till mål databasen

Om du inte har en PostgreSQL-klient eller om du vill använda Azure Cloud Shell kan du använda den här metoden. Databas dumpningen strömmas direkt till mål databas servern och lagrar inte dumpen i klienten. Detta kan därför användas med en-klient med begränsad lagring och kan även köras från Azure Cloud Shell. 

1. Kontrol lera att databasen finns på mål servern med hjälp av `\l` kommandot. Om databasen inte finns skapar du databasen.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Kör dumpen och Återställ som en enda kommando rad med en pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Exempel:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. När uppgraderings processen (migreringen) är klar kan du testa programmet med mål servern. 
4. Upprepa processen för alla databaser på servern.

 Som exempel illustrerar följande tabell hur lång tid det tog att migrera med metoden för strömnings-dump. Exempel data fylls med hjälp av [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Eftersom din databas kan ha olika antal objekt med varierande storlekar än pgbench genererade tabeller och index, rekommenderar vi starkt att du testar dumpning och återställning av databasen för att förstå den faktiska tid det tar att uppgradera databasen. 

| **Databas storlek** | **Uppskattad tids åtgång** | 
| ----- | ------ |
| 1 GB  | 1-2 minuter |
| 5 GB | 8-10 minuter |
| 10 GB | 15-20 minuter |
| 50 GB | 1 – 1,5 timmar |
| 100 GB | 2,5 – 3 timmar|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Metod 3: Migrera med parallell dump och återställning 

Du kan använda den här metoden om du har några större tabeller i databasen och du vill parallellisera för dump och återställning för den databasen. Du behöver också tillräckligt med lagrings utrymme i klient systemet för att hantera säkerhets kopierings dum par. Den här parallella dumpnings-och återställnings processen minskar tids förbrukningen för att slutföra hela migreringen. Till exempel slutfördes den 50 GB pgbench-databasen som tog 1 – 1,5 timmar att migrera, med metod 1 och 2 tog mindre än 30 minuter med den här metoden.

1. Skapa en motsvarande databas på mål servern för varje databas på käll servern.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Exempel:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Kör kommandot pg_dump i ett katalog format med antalet jobb = 4 (antalet tabeller i databasen). Med större beräknings nivå och med fler tabeller kan du öka antalet till en högre siffra. Pg_dump skapar en katalog för att lagra komprimerade filer för varje jobb.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Exempel:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Återställ sedan säkerhets kopian på mål servern.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Exempel:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Den process som anges i det här dokumentet kan också användas för att uppgradera Azure Database for PostgreSQL-flexibla servern, som är en för hands version. Den största skillnaden är anslutnings strängen för det flexibla Server målet utan `@dbName` .  Om användar namnet till exempel är `pg` , kommer den enskilda serverns användar namn i anslutnings strängen att vara `pg@pg-95` , men med flexibel Server kan du bara använda `pg` .

## <a name="next-steps"></a>Nästa steg

- När du är nöjd med mål databas funktionen kan du släppa den gamla databas servern. 
- Om du vill använda samma databas slut punkt som käll servern, kan du skapa en Läs replik med det gamla databas Server namnet efter att du har tagit bort den gamla käll databas servern. När du har upprättat ett stabilt tillstånd kan du stoppa repliken så att replik servern blir en oberoende Server. Se [replikering](./concepts-read-replicas.md) för mer information.
- Kom ihåg att testa och validera dessa kommandon i en test miljö innan du använder dem i produktion.