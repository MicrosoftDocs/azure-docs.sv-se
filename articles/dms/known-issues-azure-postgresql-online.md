---
title: 'Kända problem: online-migreringar från PostgreSQL till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem och begränsningar för migrering med online-migrering från PostgreSQL till Azure Database for PostgreSQL med hjälp av Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258837"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Kända problem/migrerings begränsningar med online-migreringar från PostgreSQL till Azure DB för PostgreSQL

Kända problem och begränsningar som är kopplade till online-migreringar från PostgreSQL till Azure Database for PostgreSQL beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av online-migrering

- Käll-PostgreSQL-servern måste köra version 9,4, 9,5, 9,6, 10 eller 11. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).
- Endast migreringar till samma eller en senare version stöds. Till exempel stöds migrering av PostgreSQL 9,5 till Azure Database for PostgreSQL 9,6 eller 10, men migrering från PostgreSQL 11 till PostgreSQL 9,6 stöds inte.
- Om du vill aktivera logisk replikering i filen **Source postgresql postgresql. conf** anger du följande parametrar:
  - **wal_level** = logisk
  - **max_replication_slots** = [minst Max antal databaser för migrering]; Om du vill migrera fyra databaser ställer du in värdet på minst 4.
  - **max_wal_senders** = [antal databaser som körs samtidigt]; det rekommenderade värdet är 10
- Lägg till DMS-agentens IP-adress till källan PostgreSQL pg_hba. conf
  1. Anteckna DMS-IP-adressen när du har slutfört etableringen av en instans av Azure Database Migration Service.
  2. Lägg till IP-adressen i filen pg_hba. conf som visas:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- Användaren måste ha REPLIKERINGSPRINCIPEN på den server som är värd för käll databasen.
- Käll-och mål databasens scheman måste vara identiska.
- Schemat i Azure-databasen för PostgreSQL-Single-servern får inte ha några sekundär nycklar. Använd följande fråga om du vill ta bort sekundär nycklar:

    ```
                  SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

- Schemat i Azure-databasen för PostgreSQL-Single-servern får inte ha några utlösare. Använd följande för att inaktivera utlösare i mål databasen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Storleks begränsningar
- Du kan migrera upp till 2 TB data från PostgreSQL till Azure DB för PostgreSQL med hjälp av en enda DMS-tjänst.
## <a name="datatype-limitations"></a>Begränsningar för data typer

  **Begränsning**: om det inte finns någon primär nyckel för tabeller, kanske ändringarna inte synkroniseras med mål databasen.

  **Lösning**: temporärt ange en primär nyckel för tabellen för migrering för att fortsätta. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Begränsningar vid migrering online från AWS RDS PostgreSQL

När du försöker utföra en online-migrering från AWS RDS PostgreSQL till Azure Database for PostgreSQL kan du stöta på följande fel.

- **Fel**: Standardvärdet för kolumnen ”{column}” i tabellen ”{table}” i databasen ”{database}” är olika på käll- och målservrarna. Det har ”{value on source}” i källan och ”{value on target}” i målet.

  **Begränsning**: det här felet uppstår när standardvärdet i ett kolumn schema skiljer sig mellan käll-och mål databaserna.
  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Fel**: Måldatabasen ”{database}” har ”{number of tables}” tabeller, medan källdatabasen ”{database}” har ”{number of tables}” tabeller. Antalet tabeller i käll- och måldatabaserna måste vara lika många.

  **Begränsning**: det här felet uppstår när antalet tabeller skiljer sig mellan käll-och mål databaserna.

  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Fel:** Käll databasen {Database} är tom.

  **Begränsning**: det här felet uppstår när käll databasen är tom. Det beror förmodligen på att du har valt fel databas som källa.

  **Lösning**: kontrol lera den käll databas som du har valt för migrering och försök sedan igen.

- **Fel:** Mål databasen {Database} är tom. Migrera schemat.

  **Begränsning**: det här felet uppstår när det inte finns något schema i mål databasen. Kontrol lera att schemat på målet matchar schemat på källan.
  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Andra begränsningar

- Databas namnet får inte innehålla semikolon (;).
- En uppfångad tabell måste ha en primär nyckel. Om en tabell inte har en primär nyckel, blir resultatet av åtgärderna ta bort och uppdatera post oförutsägbart.
- Uppdatering av ett primär nyckel segment ignoreras. I sådana fall identifieras en sådan uppdatering av målet som en uppdatering som inte uppdaterade några rader och resulterar i att en post skrivs till tabellen undantag.
- Migrering av flera tabeller med samma namn, men ett annat fall (t. ex. TABLE1, TABLE1 och Table1) kan orsaka oförutsägbart beteende och stöds därför inte.
- Ändrings bearbetning av [CREATE | ÄNDRA | SLÄPP | TRUNKERA] tabellen DDLs stöds inte.
- I Azure Database Migration Service kan en enda migrerings aktivitet bara hantera upp till fyra databaser.
- Migrering av pg_largeobjects tabellen stöds inte. 
