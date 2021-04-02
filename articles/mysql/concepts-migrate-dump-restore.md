---
title: Migrera med dump och Restore-Azure Database for MySQL
description: I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL med hjälp av verktyg som mysqldump, MySQL Workbench och PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94537925"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL
- Dumpa och Återställ från kommando raden (med mysqldump)
- Dumpa och Återställ med PHPMyAdmin

Du kan också läsa mer om hur du migrerar databaser till Azure Database for MySQL med hjälp av [Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) . Den här guiden ger vägledning som leder till en lyckad planering och körning av en MySQL-migrering till Azure.

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här instruktions guiden måste du ha:
- [Skapa en Azure Database for MySQL-server – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- kommando rads verktyget [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) installerat på en dator.
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) eller ett annat mysql-verktyg från tredje part för att utföra dump-och Restore-kommandon.

> [!TIP]
> Om du vill migrera stora databaser med databas storlekar på över 1 TBs kanske du vill överväga att använda community-verktyg som t. ex. en **dumpare/för-belastningsutjämnare** som stöder parallell export och import. Lär dig [hur du migrerar stora MySQL-databaser](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Vanliga användnings fall för dump och återställning

De vanligaste användnings fallen är:

- **Flytta från annan leverantör av hanterade tjänster** : mest hanterade tjänst leverantörer kanske inte ger åtkomst till den fysiska lagrings filen av säkerhets skäl så att logisk säkerhets kopiering och återställning är det enda alternativet att migrera.
- **Migrering från en lokal miljö eller virtuell dator** – Azure Database for MySQL stöder inte återställning av fysiska säkerhets kopieringar som gör den logiska säkerhets kopieringen och återställningen som den enda metoden.
- **Att flytta lagrings utrymme för säkerhets kopior från lokalt redundant till Geo-redundant lagring** – Azure Database for MySQL tillåter konfigurering av lokalt redundant eller Geo-redundant lagring för säkerhets kopiering tillåts bara när servern skapas. När servern har etablerats kan du inte ändra alternativet för redundant lagring för säkerhetskopior. För att kunna flytta lagrings utrymme för säkerhets kopior från lokalt redundant lagring till Geo-redundant lagring är dump och Restore det enda alternativet. 
-  **Migrering från alternativa lagrings motorer till InnoDB** -Azure Database for MySQL stöder bara InnoDB Storage Engine och stöder därför inte alternativa lagrings motorer. Om dina tabeller har kon figurer ATS med andra lagrings motorer konverterar du dem till InnoDB-motorns format innan du migrerar till Azure Database for MySQL.

    Om du till exempel har en WordPress eller WebApp som använder mina ISAM-tabeller, måste du först konvertera tabellerna genom att migrera till InnoDB-format innan du återställer till Azure Database for MySQL. Använd satsen `ENGINE=InnoDB` för att ställa in motorn som används när du skapar en ny tabell och överför sedan data till den kompatibla tabellen innan du återställer.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - För att undvika eventuella kompatibilitetsproblem bör du se till att samma version av MySQL används i käll- och målsystemen när databaser dumpas. Om din befintliga MySQL-server till exempel är version 5,7 bör du migrera till Azure Database for MySQL konfigurerad för att köra version 5,7. `mysql_upgrade`Kommandot fungerar inte i en Azure Database for MySQL-server och stöds inte. 
> - Om du behöver uppgradera mellan MySQL-versioner måste du först dumpa eller exportera den lägre versions databasen till en högre version av MySQL i din egen miljö. Kör sedan `mysql_upgrade` innan du försöker migrera till en Azure Database for MySQL.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda bör du tänka på följande när det gäller dumpning av stora databaser:
-   Använd `exclude-triggers` alternativet i mysqldump när det är dumpnings databaser. Exkludera utlösare från dumpfiler för att undvika att Utlös ande kommandon utlöses under data återställningen.
-   Använd `single-transaction` alternativet för att ställa in transaktions isolerings läget till REPETERBAR läsning och skicka ett SQL-uttryck för starttransaktion till servern innan du påbörjar dumpnings data. Dumpning av många tabeller i en enda transaktion medför att en extra lagring används under återställningen. `single-transaction`Alternativet och `lock-tables` alternativet är ömsesidigt uteslutande eftersom lås tabeller gör att eventuella väntande transaktioner kan bekräftas implicit. Om du vill dumpa stora tabeller kombinerar du `single-transaction` alternativet med `quick` alternativet.
-   Använd `extended-insert` syntax med flera rader som innehåller flera värde listor. Detta resulterar i en mindre dumpfil och påskyndar infogningar när filen läses in igen.
-  Använd `order-by-primary` alternativet i mysqldump när det gäller dumpnings databaser, så att data skriptas i primär nyckel ordning.
-   Använd `disable-keys` alternativet i mysqldump när dumpnings data ska inaktivera begränsningar för sekundär nyckel före belastningen. Att inaktivera kontroller av sekundär nyckel ger prestanda vinster. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle innebära att du träffar en resurs gräns och övervaka resurser med hjälp av de mått som är tillgängliga i Azure Portal.
-   Använd `defer-table-indexes` alternativet i mysqlpump när det gäller dumpnings databaser, så att skapandet av index sker efter att tabell data har lästs in.
-   Använd `skip-definer` alternativet i mysqlpump för att utelämna definar-och SQL-säkerhetssatser från Create-instruktionerna för vyer och lagrade procedurer.  När du läser in dumpfilen igen skapas objekt som använder standardvärdena för avrundning och SQL-säkerhet.
-   Kopiera säkerhetskopieringsfilerna till en Azure Blob/Store och utför återställningen därifrån, vilket bör vara mycket snabbare än att utföra återställningen via Internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Skapa en databas på mål Azure Database for MySQLs servern
Skapa en tom databas på mål Azure Database for MySQL servern där du vill migrera data. Använd ett verktyg som MySQL Workbench eller mysql.exe för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data eller så kan du skapa en databas med ett annat namn.

För att ansluta, letar du upp anslutnings informationen i **översikten** över din Azure Database for MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Hitta anslutnings informationen i Azure Portal":::

Lägg till anslutnings informationen i MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="Anslutnings sträng för MySQL Workbench":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Förbereda mål Azure Database for MySQL servern för snabb data inläsningar
Följande Server parametrar och konfiguration måste ändras för att förbereda mål Azure Database for MySQL servern för snabbare data inläsningar.
- max_allowed_packet – Ställ in på 1073741824 (dvs. 1 GB) för att förhindra eventuella problem med data spill på grund av långa rader.
- slow_query_log – inställt på att stänga av den långsamma frågans logg. Detta eliminerar överbelastningen som orsakas av långsam loggning av frågor vid data inläsning.
- query_store_capture_mode – Ställ in på ingen för att inaktivera Frågearkivet. Detta eliminerar överbelastningen som orsakas av provtagnings aktiviteter från Query Store.
- innodb_buffer_pool_size – skala upp servern till 32 vCore-minnesoptimerade SKU från pris nivån i portalen under migreringen för att öka innodb_buffer_pool_size. Innodb_buffer_pool_size kan bara ökas genom att skala upp beräkning för Azure Database for MySQL server.
- innodb_io_capacity & innodb_io_capacity_max – ändra till 9000 från Server parametrarna i Azure Portal för att förbättra i/o-användningen för att optimera migreringens hastighet.
- innodb_write_io_threads & innodb_write_io_threads-ändra till 4 från Server parametrarna i Azure Portal för att förbättra migreringens hastighet.
- Skala upp lagrings nivå – IOPs för Azure Database for MySQL server ökar progressivt med ökningen på lagrings nivå. För snabbare belastningar kanske du vill öka lagrings nivån för att öka den IOPs som är etablerad. Kom ihåg att lagringen bara kan skalas upp, inte.

När migreringen är klar kan du återgå till att återställa Server parametrarna och konfigurationen för beräknings nivån till dess tidigare värden.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Dumpa och Återställ med mysqldump-verktyget

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Skapa en säkerhets kopierings fil från kommando raden med mysqldump
Om du vill säkerhetskopiera en befintlig MySQL-databas på den lokala lokala servern eller på en virtuell dator kör du följande kommando:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De parametrar som ska tillhandahållas är:
- [uname] Ditt användar namn för databasen
- pass Lösen ordet för din databas (Observera att det inte finns något blank steg mellan-p och lösen ordet)
- DB Namnet på din databas
- [BackupFile. SQL] fil namnet för säkerhets kopian av databasen
- [--opt] Alternativet mysqldump

Om du till exempel vill säkerhetskopiera en databas med namnet "testdb" på MySQL-servern med användar namnet "testuser" och utan lösen ord till en fil testdb_backup. SQL, använder du följande kommando. Kommandot säkerhetskopierar `testdb` databasen till en fil `testdb_backup.sql` med namnet, som innehåller alla SQL-instruktioner som krävs för att återskapa databasen. Se till att användar namnet testuser har minst SELECT-behörighet för dumpade tabeller, Visa vy för dumpade vyer, utlösare för dumpade utlösare och lås tabeller om alternativet--Single-Transaction inte används.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Kör nu mysqldump för att skapa säkerhets kopian av `testdb` databasen

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Om du vill välja vissa tabeller i databasen som ska säkerhets kopie ras, listar du tabell namnen avgränsade med blank steg. Om du till exempel vill säkerhetskopiera endast TABLE1-och tabell2-tabeller från "testdb", följer du det här exemplet:

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Om du vill säkerhetskopiera fler än en databas samtidigt använder du--databas växeln och listar databas namnen avgränsade med blank steg.
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Återställa MySQL-databasen med hjälp av kommando raden eller MySQL Workbench
När du har skapat mål databasen kan du använda MySQL-kommandot eller MySQL Workbench för att återställa data till den specifika nyligen skapade databasen från dumpfilen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
I det här exemplet återställer du data till den nyligen skapade databasen på mål Azure Database for MySQLs servern.

Här är ett exempel på hur du använder **MySQL** för **en enskild server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Här är ett exempel på hur du använder **MySQL** för **flexibel Server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Dumpa och Återställ med PHPMyAdmin
Följ dessa steg om du vill dumpa och återställa en databas med PHPMyadmin.

> [!NOTE]
> För en enskild server måste användar namnet vara i det här formatet, username@servername men för en flexibel Server kan du bara använda "username" om du använder " username@servername " för den flexibla servern. det går inte att ansluta.

### <a name="export-with-phpmyadmin"></a>Exportera med PHPMyadmin
Om du vill exportera kan du använda det vanliga verktyget phpMyAdmin, som du kanske redan har installerat lokalt i din miljö. Så här exporterar du MySQL-databasen med PHPMyAdmin:
1. Öppna phpMyAdmin.
2. Välj din databas. Klicka på databas namnet i listan till vänster.
3. Klicka på länken **Exportera** . En ny sida visas för att Visa dumpen av databasen.
4. I export-ytan klickar du på länken **Välj alla** för att välja tabeller i databasen.
5. I avsnittet SQL-alternativ klickar du på lämpliga alternativ.
6. Klicka på alternativet **Spara som fil** och motsvarande komprimerings alternativ och klicka sedan på knappen **gå** . En dialog ruta visas där du ombeds att spara filen lokalt.

### <a name="import-using-phpmyadmin"></a>Importera med PHPMyAdmin
Att importera databasen liknar att exportera. Utför följande åtgärder:
1. Öppna phpMyAdmin.
2. På sidan installation av phpMyAdmin klickar du på **Lägg** till för att lägga till din Azure Database for MySQL-server. Ange anslutnings informationen och inloggnings informationen.
3. Skapa en korrekt namngiven databas och välj den till vänster på skärmen. Om du vill skriva om den befintliga databasen klickar du på databas namnet, markerar alla kryss rutorna bredvid tabell namnen **och väljer Ta bort för** att ta bort befintliga tabeller.
4. Klicka på **SQL** -länken för att visa sidan där du kan skriva SQL-kommandon eller ladda upp din SQL-fil.
5. Använd knappen **Bläddra** för att hitta databas filen.
6. Klicka på knappen **gå** för att exportera säkerhets kopian, kör SQL-kommandona och återskapa databasen.

## <a name="known-issues"></a>Kända problem
För kända problem, tips och trick rekommenderar vi att du tittar på vår [techcommunity-blogg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database for MySQL](./howto-connection-string.md).
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [Guide för databas migrering](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Om du vill migrera stora databaser med databas storlekar på över 1 TBs kanske du vill överväga att använda community-verktyg som t. ex. en **dumpare/för-belastningsutjämnare** som stöder parallell export och import. Lär dig [hur du migrerar stora MySQL-databaser](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
