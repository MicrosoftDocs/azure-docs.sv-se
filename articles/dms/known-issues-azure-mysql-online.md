---
title: 'Kända problem: online-migreringar till Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem och begränsningar för migrering med online-migrering för att Azure Database for MySQL när du använder Azure Database Migration Service.
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
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606897"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problem med online-migrering & begränsningar för Azure DB för MySQL med Azure Database Migration Service

Kända problem och begränsningar som är kopplade till online-migreringar från MySQL till Azure Database for MySQL beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av online-migrering


- Käll Server versionen för MySQL måste vara version 5.6.35, 5.7.18 eller senare
- Azure Database for MySQL stöder:
  - MySQL Community Edition
  - InnoDB-motor
- Migrering av samma version. Migrering av MySQL 5,6 till Azure Database for MySQL 5,7 stöds inte. Migreringar till eller från MySQL 8,0 stöds inte.
- Aktivera binär loggning i my.ini (Windows) eller My. cnf (UNIX)
  - Ange Server_id till ett tal som är större eller lika med 1, till exempel Server_id = 1 (endast för MySQL 5,6)
  - Ange log-bin = \<path> (endast för MySQL 5,6)
  - Ange binlog_format = rad
  - Expire_logs_days = 5 (rekommenderas endast för MySQL 5,6)
- Användaren måste ha rollen ReplicationAdmin.
- Sorteringar som har definierats för käll-MySQL-databasen är desamma som de som definierats i mål Azure Database for MySQL.
- Schemat måste matcha mellan käll-MySQL-databasen och mål databasen i Azure Database for MySQL.
- Schemat i mål Azure Database for MySQL får inte ha sekundär nycklar. Använd följande fråga om du vill ta bort sekundär nycklar:
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.
- Schemat i mål Azure Database for MySQL får inte ha några utlösare. Så här släpper du utlösare i mål databasen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Begränsningar för data typer

- Begränsning: om det finns en JSON-datatype i käll databasen MySQL, kommer migreringen att Miss **fördubblas** under en kontinuerlig synkronisering.

    **Lösning**: ändra JSON-datatype till medium text eller LONGTEXT i käll-MySQL-databasen.

- **Begränsning**: om det inte finns någon primär nyckel för tabeller kommer den kontinuerliga synkroniseringen att Miss växlar.

    **Lösning**: temporärt ange en primär nyckel för tabellen för migrering för att fortsätta. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="lob-limitations"></a>LOB-begränsningar

LOB-kolumner (Large Object) är kolumner som kan växa stora i storlek. För MySQL, är medel text, LONGTEXT, BLOB, MEDIUMBLOB, LONGBLOB osv. några av data typerna för LOB.

- **Begränsning**: om LOB-datatyper används som primär nycklar kommer migreringen att Miss Miss förflyttningen.

    **Lösning**: Ersätt primär nyckel med andra data typer eller kolumner som inte är LOB.

- **Begränsning**: om kolumnen för stora objekt (LOB) är större än parametern "Limit LOB-storlek" (bör inte vara större än 64 KB) kan data trunkeras vid målet. Du kan kontrol lera längden på LOB-kolumnen med den här frågan:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Lösning**: om du har LOB-objekt som är större än 64 KB använder du parametern "Tillåt obegränsad LOB-storlek". Observera att migrering med parametern "Tillåt obegränsad LOB-storlek" är långsammare än migreringar med parametern "gräns för LOB-storlek".

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Begränsningar vid migrering online från AWS RDS MySQL

När du försöker utföra en online-migrering från AWS RDS MySQL till att Azure Database for MySQL, kan du komma över följande fel.

- **Fel:** Databasen {0} har sekundär nyckel (er) på målet. Åtgärda målet och starta en ny migreringsaktivitet för data. Kör skriptet nedan på målet för att visa en lista över sekundär nyckel (er)

  **Begränsning**: om du har sekundär nycklar i schemat kommer den inledande inläsningen och den kontinuerliga synkroniseringen av migreringen att Miss Miss förflyttningen.
  **Lösning**: kör följande skript i MySQL Workbench för att extrahera skriptet för att ta bort sekundär nyckel och lägga till sekundär nyckel skript:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fel:** Databasen {0} finns inte på servern. Den angivna MySQL-källservern är skiftlägeskänslig. Kontrollera namnet på databasen.

  **Begränsning**: när du migrerar en MySQL-databas till Azure med hjälp av kommando rads gränssnittet (CLI) kan användarna nå det här felet. Tjänsten kunde inte hitta databasen på käll servern, vilket kan bero på att du har angett ett felaktigt databas namn eller att databasen inte finns på den angivna servern. Obs! databas namn är Skift läges känsliga.

  **Lösning**: Ange det exakta databas namnet och försök igen.

- **Fel:** Det finns tabeller med samma namn i databasen {Database}. Azure Database för MySQL stöder inte skiftlägeskänsliga tabeller.

  **Begränsning**: det här felet uppstår när du har två tabeller med samma namn i käll databasen. Azure Database for MySQL stöder inte Skift läges känsliga tabeller.

  **Lösning**: uppdatera tabell namnen så att de blir unika och försök sedan igen.

- **Fel:** Mål databasen {Database} är tom. Migrera schemat.

  **Begränsning**: det här felet uppstår när mål Azure Database for MySQL databasen inte har det schema som krävs. Schema migrering krävs för att kunna migrera data till målet.

  **Lösning**: [migrera schemat](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) från käll databasen till mål databasen.

## <a name="other-limitations"></a>Andra begränsningar

- En lösen ords sträng som har inledande och avslutande klammer {} i början och slutet av lösen ords strängen stöds inte. Den här begränsningen gäller både anslutning till källa MySQL och mål Azure Database for MySQL.
- Följande DDLs stöds inte:
  - Alla partition DDLs
  - Ta bort tabell
  - Byt namn på tabell
- Med hjälp av *Alter table <table_name> Lägg till kolumn <column_name>* -instruktion för att lägga till kolumner i början eller i mitten av en tabell stöds inte. *Alter table <table_name> Lägg till kolumn <column_name>* lägger till kolumnen i slutet av tabellen.
- Index som skapats endast för en del av kolumn data stöds inte. Följande instruktion är ett exempel som skapar ett index med endast en del av kolumn data:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- I Azure Database Migration Service är gränsen för databaser som ska migreras i en enda migrering fyra.

- Azure DMS stöder inte referens åtgärden CASCADE, som hjälper dig att automatiskt ta bort eller uppdatera en matchande rad i den underordnade tabellen när en rad tas bort eller uppdateras i den överordnade tabellen. Mer information finns i avsnittet referens åtgärder i artikelns [begränsningar för sekundär nyckel](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html). Azure DMS kräver att du släpper sekundär nyckel begränsningar i mål databas servern under den inledande data inläsningen och du kan inte använda referens åtgärder. Om din arbets belastning är beroende av att uppdatera en relaterad underordnad tabell via den här referens åtgärden, rekommenderar vi att du utför en [dumpning och återställning](../mysql/concepts-migrate-dump-restore.md) i stället. 

- **Fel:** Rad storleken är för stor (> 8126). Att ändra vissa kolumner till TEXT eller BLOB kan hjälpa dig. I aktuellt rad format lagras BLOB-prefixet 0 byte infogat.

  **Begränsning**: det här felet uppstår när du migrerar till Azure Database for MySQL med InnoDB-lagrings motorn och eventuell tabell rad storlek är för stor (>8126 byte).

  **Lösning**: uppdatera schemat för den tabell som har en rad storlek som är större än 8126 byte. Vi rekommenderar att du inte ändrar strikt läge eftersom data kommer att trunkeras. Det finns inte stöd för att ändra page_size.