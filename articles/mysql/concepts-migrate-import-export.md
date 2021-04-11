---
title: Importera och exportera – Azure Database for MySQL
description: I den här artikeln beskrivs vanliga sätt att importera och exportera databaser i Azure Database for MySQL med hjälp av verktyg som MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625151"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera MySQL-databasen med hjälp av import och export

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

I den här artikeln beskrivs två vanliga metoder för att importera och exportera data till en Azure Database for MySQL-server med hjälp av MySQL Workbench.

Detaljerad vägledning för migrering finns i [resurser för migrerings guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

För andra scenarier för migrering, se [Guide för databas migrering](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera MySQL-databasen måste du:
- Skapa en [Azure Database for MySQL-server med hjälp av Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Hämta och installera [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) eller ett annat mysql-verktyg från tredje part för import och export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure Database for MySQL-servern
Skapa en tom databas på Azure Database for MySQL server med MySQL Workbench, TOAD eller Navicat. Databasen kan ha samma namn som databasen som innehåller de dumpade data, eller så kan du skapa en databas med ett annat namn.

Gör så här för att ansluta:

1. I Azure Portal kan du leta efter anslutnings informationen i **översikts** fönstret i Azure Database för MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Skärm bild av information om Azure Database for MySQL Server-anslutningen i Azure Portal.":::

1. Lägg till anslutnings informationen till MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Anslutnings sträng för MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Avgöra när import-och export tekniker ska användas

> [!TIP]
> Använd [dumpnings-och återställnings](concepts-migrate-dump-restore.md) metoden i stället för scenarier där du vill dumpa och återställa hela databasen.

I följande scenarier använder du MySQL-verktyg för att importera och exportera databaser till MySQL-databasen. För andra verktyg går du till avsnittet "autentiseringsmetoder" (sidan 22) i [guiden MySQL till Azure Database migration](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- När du behöver selektivt välja några tabeller som ska importeras från en befintlig MySQL-databas till din Azure MySQL-databas, är det bäst att använda import-och export teknik.  Genom att göra det kan du utelämna eventuella tabeller som inte behövs från migreringen för att spara tid och resurser. Använd t `--include-tables` `--exclude-tables` . ex. växeln eller med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) och `--tables` växeln med [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar andra databas objekt än tabeller, skapar du explicit dessa objekt. Ta med begränsningar (primär nyckel, sekundär nyckel och index), vyer, funktioner, procedurer, utlösare och andra databas objekt som du vill migrera.
- När du migrerar data från externa data källor som inte är en MySQL-databas skapar du Flat Files och importerar dem med hjälp av [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Både en server och en flexibel Server stöder *bara InnoDB-lagrings motorn*. Se till att alla tabeller i databasen använder InnoDB lagrings motor när du läser in data i Azure Database för MySQL.
>
> Om käll databasen använder en annan lagrings motor konverterar du till InnoDB-motorn innan du migrerar databasen. Om du till exempel har en WordPress-eller webbapp som använder sig av en ISAM-motor måste du först konvertera tabellerna genom att migrera data till InnoDB-tabeller. Använd satsen för `ENGINE=INNODB` att ställa in motorn för att skapa en tabell och överför sedan data till den kompatibla tabellen innan migreringen.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestanda rekommendationer för import och export

För optimala data import-och export prestanda rekommenderar vi att du gör följande:
-   Skapa grupperade index och primär nycklar innan du läser in data. Läs in data i primär nyckel ordning.
-   Fördröj skapandet av sekundära index tills data har lästs in.
-   Inaktivera sekundär nyckel begränsningar innan du läser in data. Att inaktivera kontroller av sekundär nyckel ger avsevärda prestanda vinster. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle innebära att du träffar en resurs gräns och övervaka resurser genom att använda de mått som är tillgängliga i Azure Portal.
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importera och exportera data med hjälp av MySQL Workbench
Det finns två sätt att exportera och importera data i MySQL Workbench: från snabb menyn objekt läsare eller från navigerings fönstret. Varje metod har ett annat syfte.

> [!NOTE]
> Om du lägger till en anslutning till MySQL Single Server eller flexibel Server (för hands version) på MySQL Workbench gör du följande:
> - Kontrol lera att användar namnet är i formatet för MySQL Single Server *\<username@servername>* .
> - Använd endast för MySQL-flexibel Server *\<username>* . Om du använder *\<username@servername>* för att ansluta fungerar inte anslutningen.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Kör guiden Exportera och Importera tabell data från snabb menyn objekt läsare

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Skärm bild av kommandona i guiden för export och import av MySQL Workbench på snabb menyn i objekt läsaren.":::

Guiden tabell data stöder import-och export åtgärder med hjälp av CSV-och JSON-filer. I guiderna finns flera konfigurations alternativ, till exempel avgränsare, val av kolumn och kodning. Du kan köra varje guide mot lokala eller fjärranslutna MySQL-servrar. Import åtgärden inkluderar tabell-, kolumn-och typ mappning.

Om du vill komma åt dessa guider från snabb menyn objekt läsare högerklickar du på en tabell och väljer sedan guiden för att **Exportera tabell data** eller **guiden tabell data import**.

#### <a name="the-table-data-export-wizard"></a>Guiden tabell data export

Så här exporterar du en tabell till en CSV-fil:

1. Högerklicka på tabellen i databasen som ska exporteras.
1. Välj **guiden för tabell data export**. Markera de kolumner som ska exporteras, rad förskjutning (om sådana finns) och antal (om det finns några).
1. I fönstret **Välj data för export** väljer du **Nästa**. Välj fil Sök väg, CSV eller JSON-filtypen. Välj också rad avgränsaren, metoden för att omsluta strängar och fält avgränsare.
1. I fönstret **Välj sökväg för utdatafil** väljer du **Nästa**.
1. I fönstret **Exportera data** väljer du **Nästa**.

#### <a name="the-table-data-import-wizard"></a>Guiden Importera tabell data

Så här importerar du en tabell från en CSV-fil:

1. Högerklicka på tabellen i databasen som ska importeras.
1. Leta upp och markera den CSV-fil som ska importeras och välj sedan **Nästa**.
1. Välj mål tabellen (ny eller befintlig), markera eller avmarkera kryss rutan **trunkera tabellen före import** och välj sedan **Nästa**.
1. Välj kodningen och de kolumner som ska importeras och välj sedan **Nästa**.
1. I fönstret **Importera data** väljer du **Nästa**. Guiden importerar data.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Köra guiderna för SQL data export och import från fönstret navigatör
Använd en guide för att exportera eller importera SQL-data som genereras från MySQL Workbench eller från mysqldump-kommandot. Du kan komma åt guiderna från fönstret **Navigator** eller välja **Server** från huvud menyn.

#### <a name="export-data"></a>Exportera data

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Skärm bild som visar fönstret data export i MySQL Workbench med hjälp av fönstret navigatör.":::

Du kan använda fönstret **data export** för att exportera MySQL-data.

1. I MySQL Workbench väljer du **data export** i fönstret **Navigator** .

1. I fönstret **data export** väljer du varje schema som du vill exportera.
 
   För varje schema kan du välja vissa schema objekt eller tabeller som ska exporteras. Konfigurations alternativen är exportera till en projektmapp eller en fristående SQL-fil, dumpa lagrade rutiner och händelser eller hoppa över tabell data.

   Du kan också använda **Exportera en resultat uppsättning** för att exportera en angiven resultat uppsättning i SQL-redigeraren till ett annat format, till exempel CSV, JSON, HTML och XML.

1. Välj de databas objekt som ska exporteras och konfigurera de relaterade alternativen.
1. Välj **Uppdatera** för att läsa in de aktuella objekten.
1. Alternativt kan du välja **Avancerade alternativ** längst upp till höger för att förfina export åtgärden. Du kan till exempel lägga till tabell lås, använda Ersätt i stället för INSERT-instruktioner och citat identifierare med tecken med autoskalning.
1. Välj **starta export** för att starta export processen.


#### <a name="import-data"></a>Importera data

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Skärm bild som visar fönstret data import i MySQL Workbench med hjälp av fönstret navigatör.":::

Du kan använda fönstret **data import** för att importera eller återställa exporterade data från data export åtgärden eller från mysqldump-kommandot.

1. I MySQL Workbench väljer du **data export/återställning** i fönstret **Navigator** .
1. Välj projektmappen eller den fristående SQL-filen, Välj det schema som du vill importera till eller Välj knappen **nytt** för att definiera ett nytt schema.
1. Klicka på **Starta import** för att starta importen.

## <a name="next-steps"></a>Nästa steg
- En annan metod för migrering finns i [migrera MySQL-databasen till en Azure Database för MySQL med hjälp av dumpa och Återställ](concepts-migrate-dump-restore.md).
- Mer information om hur du migrerar databaser till en Azure Database för MySQL finns i [Database migration guide](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
