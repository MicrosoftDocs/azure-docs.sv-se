---
title: Importera och exportera – Azure Database for MySQL
description: I den här artikeln beskrivs vanliga sätt att importera och exportera databaser i Azure Database for MySQL med hjälp av verktyg som MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 391a38cd3d1e2ffb790587b6c60ed54992d14d2d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561999"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera MySQL-databasen med hjälp av import och export
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
I den här artikeln beskrivs två vanliga metoder för att importera och exportera data till en Azure Database for MySQL-server med hjälp av MySQL Workbench.

En detaljerad och omfattande migrations guide finns i [MySQL to Azure Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Du kan också läsa mer om hur du migrerar databaser till Azure Database for MySQL med hjälp av [Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) . Den här guiden ger vägledning som leder till en lyckad planering och körning av en MySQL-migrering till Azure.

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här instruktions guiden behöver du:
- En Azure Database for MySQL-server genom att följa [skapa en Azure Database for MySQL-server med hjälp av Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) eller ett annat mysql-verktyg från tredje part för import/export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure Database for MySQL-servern
Skapa en tom databas på Azure Database for MySQL-servern med MySQL Workbench, TOAD eller Navicat för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data, eller så kan du skapa en databas med ett annat namn.

För att ansluta, letar du upp anslutnings informationen i **översikten** över din Azure Database for MySQL.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Hitta anslutnings informationen i Azure Portal":::

Lägg till anslutnings informationen till MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Anslutnings sträng för MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Avgöra när import-och export tekniker ska användas

> [!TIP]
> För scenarier där du vill dumpa och återställa hela databasen, bör du använda [dump och Restore](concepts-migrate-dump-restore.md) -metoden i stället.

Använd MySQL-verktyg för att importera och exportera databaser till Azure MySQL-databasen i följande scenarier. För andra verktyg, se sidan 22 i [guiden MySQL till Azure Database migration](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- När du behöver selektivt välja några tabeller som ska importeras från en befintlig MySQL-databas till Azure MySQL-databasen, är det bäst att använda import-och export metoden.  Genom att göra det kan du utelämna eventuella tabeller som inte behövs från migreringen för att spara tid och resurser. Använd t `--include-tables` `--exclude-tables` . ex. växeln eller med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) och `--tables` växeln med [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar andra databas objekt än tabeller skapar du explicit dessa objekt. Ta med begränsningar (primär nyckel, sekundär nyckel, index), vyer, funktioner, procedurer, utlösare och andra databas objekt som du vill migrera.
- När du migrerar data från externa data källor som inte är en MySQL-databas skapar du Flat Files och importerar dem med hjälp av [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Både en server och en flexibel Server stöder **bara InnoDB-lagrings motorn**. Se till att alla tabeller i databasen använder InnoDB lagrings motor när du läser in data i Azure Database for MySQL.
> Om käll databasen använder en annan lagrings motor kan du konvertera till InnoDB-motorn innan du migrerar databasen. Om du till exempel har en WordPress-eller webbapp som använder sig av en ISAM-motor måste du först konvertera tabellerna genom att migrera data till InnoDB-tabeller. Använd satsen för `ENGINE=INNODB` att ställa in motorn för att skapa en tabell och överför sedan data till den kompatibla tabellen innan migreringen.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestanda rekommendationer för import och export
-   Skapa grupperade index och primär nycklar innan du läser in data. Läs in data i primär nyckel ordning.
-   Fördröj skapandet av sekundära index tills data har lästs in. Skapa alla sekundära index efter inläsning.
-   Inaktivera begränsningar för sekundär nyckel före inläsning. Att inaktivera kontroller av sekundär nyckel ger avsevärda prestanda vinster. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle innebära att du träffar en resurs gräns och övervaka resurser genom att använda de mått som är tillgängliga i Azure Portal.
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importera och exportera med hjälp av MySQL Workbench
Det finns två sätt att exportera och importera data i MySQL Workbench. Var och en har ett annat syfte.

> [!NOTE]
> Om du lägger till en anslutning till MySQL Single eller Flexible Server (för hands version) på MySQL Workbench kontrollerar du att:
> - För MySQL Single Server måste användar namnet anges i det här username@servername formatet:
> - För MySQL-flexibla Server kan du bara använda "username" om du använder " username@servername " för att ansluta.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Guide för export och import av tabell data från objekt webbläsarens snabb meny
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Guide för MySQL-Workbench på objekt läsarens snabb meny":::

Guiderna för tabell data stöder import-och export åtgärder med hjälp av CSV-och JSON-filer. De innehåller flera konfigurations alternativ, till exempel avgränsare, val av kolumn och kodning. Du kan utföra varje guide mot lokala eller fjärranslutna MySQL-servrar. Import åtgärden inkluderar tabell-, kolumn-och typ mappning.

Du kan komma åt dessa guider från snabb menyn i objekt läsaren genom att högerklicka på en tabell. Välj antingen guiden **tabell data export** eller **guiden tabell data import**.

#### <a name="table-data-export-wizard"></a>Guiden Exportera tabell data
I följande exempel exporteras tabellen till en CSV-fil:
1. Högerklicka på tabellen i databasen som ska exporteras.
2. Välj **guiden för tabell data export**. Markera de kolumner som ska exporteras, rad förskjutning (om sådana finns) och antal (om det finns några).
3. På sidan **Välj data för export** klickar du på **Nästa**. Välj fil Sök väg, CSV eller JSON-filtypen. Välj också rad avgränsaren, metoden för att omsluta strängar och fält avgränsare.
4. På sidan **Välj sökväg för utdatafil** klickar du på **Nästa**.
5. På sidan **Exportera data** klickar du på **Nästa**.

#### <a name="table-data-import-wizard"></a>Guiden Importera tabell data
I följande exempel importeras tabellen från en CSV-fil:
1. Högerklicka på tabellen i databasen som ska importeras.
2. Bläddra till och välj den CSV-fil som ska importeras och klicka sedan på **Nästa**.
3. Välj mål tabellen (ny eller befintlig) och markera eller avmarkera kryss rutan **trunkera tabellen innan du importerar** . Klicka på **Nästa**.
4. Välj kodning och kolumner som ska importeras och klicka sedan på **Nästa**.
5. På sidan **Importera data** klickar du på **Nästa**. Guiden importerar data enligt detta.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL data export-och import guider från fönstret navigatör
Använd en guide för att exportera eller importera SQL som skapats av MySQL Workbench eller genererats från mysqldump-kommandot. Öppna de här guiderna från fönstret **Navigator** eller genom att välja **Server** på huvud menyn. Välj sedan **data export** eller **data import**.

#### <a name="data-export"></a>Dataexport
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="MySQL Workbench data export med hjälp av fönstret navigatör":::

Du kan använda fliken **data export** för att exportera MySQL-data.
1. Välj varje schema som du vill exportera, om du vill kan du välja vissa schema objekt/tabeller från varje schema och generera exporten. Konfigurations alternativen omfattar export till en projektmapp eller en fristående SQL-fil, dumpa lagrade rutiner och händelser eller hoppa över tabell data.

   Du kan också använda **Exportera en resultat uppsättning** för att exportera en angiven resultat uppsättning i SQL-redigeraren till ett annat format, till exempel CSV, JSON, HTML och XML.
3. Välj de databas objekt som ska exporteras och konfigurera de relaterade alternativen.
4. Klicka på **Uppdatera** för att läsa in de aktuella objekten.
5. Du kan också öppna fliken **Avancerade alternativ** om du vill förfina export åtgärden. Du kan till exempel lägga till tabell lås, använda Ersätt i stället för INSERT-instruktioner och citat identifierare med tecken med autoskalning.
6. Klicka på **starta export** för att starta export processen.


#### <a name="data-import"></a>Data import
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="MySQL Workbench data import med Management Navigator":::

Du kan använda fliken **data import** för att importera eller återställa exporterade data från data export åtgärden eller från mysqldump-kommandot.
1. Välj projektmappen eller den fristående SQL-filen, Välj det schema som du vill importera till eller Välj **ny** för att definiera ett nytt schema.
2. Klicka på **Starta import** för att starta import processen.

## <a name="next-steps"></a>Nästa steg
- Som en annan metod för migrering läser [du migrera MySQL-databasen med dump och Återställ i Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [Guide för databas migrering](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).