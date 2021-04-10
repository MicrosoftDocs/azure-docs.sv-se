---
title: Migrera med dump och Restore-Azure Database for MariaDB
description: I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database för MariaDB med hjälp av verktyg som mysqldump, MySQL Workbench och phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628228"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrera din MariaDB-databas till en Azure Database för MariaDB med hjälp av dumpa och Återställ

I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database för MariaDB:
- Dumpa och Återställ med hjälp av ett kommando rads verktyg (med mysqldump) 
- Dumpa och Återställ med phpMyAdmin

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar migrera databasen gör du följande:
- Skapa en [Azure Database for MariaDB Server-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Installera kommando rads verktyget [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Hämta och installera [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) eller ett annat mysql-verktyg från tredje part för att köra dump-och Restore-kommandon.

## <a name="use-common-tools"></a>Använd vanliga verktyg
Använd vanliga verktyg och verktyg som MySQL Workbench eller mysqldump för att fjärrans luta och återställa data till Azure Database för MariaDB. Använd de här verktygen på klient datorn med en Internet anslutning för att ansluta till Azure Database för MariaDB. Använd en SSL-krypterad anslutning som bästa säkerhets praxis. Mer information finns i [Konfigurera SSL-anslutning i Azure Database for MariaDB](concepts-ssl-connection-security.md). Du behöver inte flytta dumpfiler till någon speciell moln plats när du migrerar data till Azure Database för MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användnings områden för dump och återställning
Du kan använda MySQL-verktyg som mysqldump och mysqlpump för att dumpa och läsa in databaser till en Azure Database for MariaDB-server i flera vanliga scenarier. 

- Använd databas dum par när du migrerar en hel databas. Den här rekommendationen innehåller när du flyttar en stor mängd data, eller när du vill minimera tjänst avbrott för Live-webbplatser eller program. 
-  Se till att alla tabeller i databasen använder InnoDB lagrings motor när du läser in data i Azure Database för MariaDB. Azure Database for MariaDB stöder endast lagrings motorn InnoDB och inga andra lagrings motorer. Om dina tabeller har kon figurer ATS med andra lagrings motorer konverterar du dem till InnoDB-motorns format innan du migrerar dem till Azure Database för MariaDB.
   
   Om du till exempel har en WordPress-app eller en webbapp som använder ISAM-tabeller, måste du först konvertera tabellerna genom att migrera dem till InnoDB-format innan du återställer dem till Azure Database för MariaDB. Använd satsen för `ENGINE=InnoDB` att ange vilken motor som ska användas för att skapa en ny tabell och överför sedan data till den kompatibla tabellen innan du återställer den. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Se till att du använder samma version av MariaDB på käll-och mål systemen för att undvika kompatibilitetsproblem när du är dumpnings databaser. Om din befintliga MariaDB-server till exempel är version 10,2 bör du migrera till Azure Database för MariaDB som är konfigurerad för att köra version 10,2. `mysql_upgrade`Kommandot fungerar inte i en Azure Database for MariaDB-Server och stöds inte. Om du behöver uppgradera över MariaDB-versioner måste du först dumpa eller exportera databasen från en tidigare version till en senare version av MariaDB i din egen miljö. Sedan kan du köra `mysql_upgrade` innan du försöker migrera till Azure Database för MariaDB.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
Tänk på följande när du vill optimera prestandan när du är dumpa stora databaser:
-   Använd `exclude-triggers` alternativet i mysqldump. Exkludera utlösare från dumpfiler för att undvika att låta Utlös kommandona vara aktiva under data återställningen. 
-   Använd `single-transaction` alternativet för att ställa in transaktions isolerings läget till REPETERBAR läsning och skicka ett SQL-uttryck för start transaktion till servern före dumpnings data. Dumpning av många tabeller i en enda transaktion medför att en extra lagrings utrymme förbrukas under återställningen. `single-transaction`Alternativet och `lock-tables` alternativet är ömsesidigt uteslutande. Detta beror på att Lås tabeller gör att eventuella väntande transaktioner kan bekräftas implicit. Om du vill dumpa stora tabeller kombinerar du `single-transaction` alternativet med `quick` alternativet. 
-   Använd `extended-insert` syntax med flera rader som innehåller flera värde listor. Den här metoden resulterar i en mindre dumpfil och påskyndar infogningar när filen läses in igen.
-  Använd `order-by-primary` alternativet i mysqldump när du är dumpnings databaser, så att data skriptas i primär nyckel ordning.
-   Använd `disable-keys` alternativet i mysqldump när du är dumpnings data och inaktivera sekundär nyckel begränsningar före belastningen. Genom att inaktivera sekundär nyckel kontroller kan du förbättra prestanda. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läs in data parallellt. Undvik alltför parallellt, vilket kan innebära att du når en resurs gräns och övervakar resurser genom att använda de mått som är tillgängliga i Azure Portal. 
-   Använd `defer-table-indexes` alternativet i mysqlpump när du är dumpnings databaser, så att skapandet av index sker efter att tabell data har lästs in.
-   Kopiera säkerhetskopieringsfilerna till en Azure Blob-lagringsplats och utför återställningen därifrån. Den här metoden bör vara mycket snabbare än att utföra återställningen via Internet.

## <a name="create-a-backup-file"></a>Skapa en säkerhets kopia

Om du vill säkerhetskopiera en befintlig MariaDB-databas på den lokala lokala servern eller på en virtuell dator kör du följande kommando med hjälp av mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

De parametrar som ska tillhandahållas är:
- *\<uname>*: Ditt användar namn för databasen 
- *\<pass>*: Lösen ordet för din databas (Observera att det inte finns något blank steg mellan-p och lösen ordet) 
- *\<dbname>*: Namnet på din databas 
- *\<backupfile.sql>*: Fil namnet för säkerhets kopian av databasen 
- *\<--opt>*: Alternativet mysqldump 

Om du till exempel vill säkerhetskopiera en databas med namnet *testdb* på MariaDB-servern med användar namnet *testuser* och utan lösen ord till en fil testdb_backup. SQL, använder du följande kommando. Kommandot säkerhetskopierar `testdb` databasen till en fil `testdb_backup.sql` med namnet, som innehåller alla SQL-instruktioner som krävs för att återskapa databasen. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Om du vill välja vissa tabeller som ska säkerhets kopie ras i databasen, listar du tabell namnen, avgränsade med blank steg. Om du till exempel vill säkerhetskopiera endast TABLE1-och tabell2-tabeller från *testdb*, följer du det här exemplet: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Om du vill säkerhetskopiera fler än en databas samtidigt använder du--databas växeln och listar databas namnen, avgränsade med blank steg. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Skapa en databas på mål servern
Skapa en tom databas på mål Azure Database for MariaDB servern där du vill migrera data. Använd ett verktyg som MySQL Workbench för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data, eller så kan du skapa en databas med ett annat namn.

För att ansluta, letar du upp anslutnings informationen i fönstret **Översikt** i Azure Database för MariaDB.

![Skärm bild av översikts fönstret för en Azure Database for MariaDB-server i Azure Portal.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

I MySQL Workbench lägger du till anslutnings informationen.

![Skärm bild av fönstret MySQL-anslutningar i MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Återställa din MariaDB-databas
När du har skapat mål databasen kan du använda MySQL-kommandot eller MySQL Workbench för att återställa data till den nyligen skapade databasen från dumpfilen.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

I det här exemplet återställer du data till den nyligen skapade databasen på mål Azure Database for MariaDBs servern.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Exportera din MariaDB-databas med hjälp av phpMyAdmin

Om du vill exportera kan du använda det vanliga verktyget phpMyAdmin, som redan kan installeras lokalt i din miljö. Gör så här för att exportera MariaDB-databasen:
1. Öppna phpMyAdmin.
1. Välj din databas i den vänstra rutan och välj sedan länken **Exportera** . En ny sida visas för att Visa dumpen av databasen.
1. I **export** -avsnittet väljer du länken **Välj alla** för att välja tabeller i databasen. 
1. I avsnittet **SQL-alternativ** väljer du lämpliga alternativ. 
1. Välj alternativet **Spara som fil** och motsvarande komprimerings alternativ och välj sedan **gå**. Spara filen lokalt i prompten.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importera databasen med phpMyAdmin

Import processen liknar den exporterande processen. Gör följande:
1. Öppna phpMyAdmin. 
1. På sidan installation av phpMyAdmin väljer du **Lägg till** för att lägga till din Azure Database for MariaDB-Server. 
1. Ange anslutnings informationen och inloggnings informationen.
1. Skapa en databas med lämplig namn och välj sedan den i det vänstra fönstret. Om du vill skriva om den befintliga databasen väljer du databas namnet, markerar alla kryss rutor bredvid tabell namnen och **väljer Ta bort för att** ta bort befintliga tabeller. 
1. Välj **SQL** -länken för att visa sidan där du kan ange SQL-kommandon eller ladda upp din SQL-fil. 
1. Välj **bläddringsknappen** för att hitta databas filen. 
1. Välj knappen **gå** för att exportera säkerhets kopian, kör SQL-kommandona och återskapa databasen.

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database för MariaDB](./howto-connection-string.md).
