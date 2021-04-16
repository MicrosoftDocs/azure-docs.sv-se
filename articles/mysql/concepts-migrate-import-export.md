---
title: Importera och exportera – Azure Database for MySQL
description: Den här artikeln beskriver vanliga sätt att importera och exportera databaser i Azure Database for MySQL, med hjälp av verktyg som MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389782"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera MySQL-databasen med hjälp av import och export

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Den här artikeln beskriver två vanliga metoder för att importera och exportera data till en Azure Database for MySQL-server med MySQL Workbench.

Detaljerad och omfattande vägledning för migrering finns i resurser [i migreringsguiden.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) 

Andra migreringsscenarier finns i Database Migration Guide (Guide [för databasmigrering).](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera MySQL-databasen måste du:
- Skapa en [Azure Database for MySQL-server med hjälp av Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Ladda ned och [installera MySQL Workbench eller](https://dev.mysql.com/downloads/workbench/) något annat MySQL-verktyg från tredje part för import och export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure Database for MySQL servern
Skapa en tom databas på Azure Database for MySQL servern med MySQL Workbench, Toad eller Navicat. Databasen kan ha samma namn som databasen som innehåller de importdata, eller så kan du skapa en databas med ett annat namn.

Anslut genom att göra följande:

1. I Azure Portal du anslutningsinformationen i fönstret **Översikt** i Azure Database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Skärmbild av Azure Database for MySQL serveranslutningsinformation i Azure Portal.":::

1. Lägg till anslutningsinformationen i MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Skärmbild av anslutningssträngen för MySQL Workbench.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Avgöra när import- och exporttekniker ska användas

> [!TIP]
> För scenarier där du vill dumpa och återställa hela databasen använder du [dump- och återställningsmetod](concepts-migrate-dump-restore.md) i stället.

I följande scenarier använder du MySQL-verktyg för att importera och exportera databaser till din MySQL-databas. För andra verktyg går du till avsnittet "Migreringsmetoder" (sida 22) i [migreringsguiden för MySQL till Azure Database.](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf) 

- När du selektivt behöver välja några tabeller att importera från en befintlig MySQL-databas till din Azure MySQL-databas är det bäst att använda import- och exporttekniken. På så sätt kan du utelämna alla tabeller som inte behövs från migreringen för att spara tid och resurser. Använd till exempel `--include-tables` växeln `--exclude-tables` eller med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)och `--tables` växeln med [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar andra databasobjekt än tabeller skapar du uttryckligen dessa objekt. Inkludera begränsningar (primärnyckel, främmande nyckel och index), vyer, funktioner, procedurer, utlösare och andra databasobjekt som du vill migrera.
- När du migrerar data från andra externa datakällor än en MySQL-databas skapar du flata filer och importerar dem med [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Både enskild server och flexibel server stöder endast InnoDB-lagringsmotorn. Se till att alla tabeller i databasen använder InnoDB-lagringsmotorn när du läser in data i din Azure-databas för MySQL.
>
> Om källdatabasen använder en annan lagringsmotor konverterar du till InnoDB-motorn innan du migrerar databasen. Om du till exempel har en WordPress- eller webbapp som använder MyISAM-motorn konverterar du först tabellerna genom att migrera data till InnoDB-tabeller. Använd `ENGINE=INNODB` -satsen för att ange motorn för att skapa en tabell och sedan överföra data till den kompatibla tabellen före migreringen.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestandarekommendationer för import och export

För optimala prestanda vid import och export av data rekommenderar vi att du gör följande:
-   Skapa klustrade index och primära nycklar innan du läser in data. Läs in data i primärnyckelordning.
-   Fördröj skapandet av sekundära index tills data har lästs in.
-   Inaktivera begränsningar för främmande nycklar innan du läser in data. Om du inaktiverar externa nyckelkontroller får du betydande prestandaförbättringar. Aktivera begränsningarna och verifiera data efter inläsningen för att säkerställa referensintegriteten.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle göra att du kommer till en resursgräns och övervaka resurser med hjälp av de mått som är tillgängliga i Azure Portal.
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importera och exportera data med MySQL Workbench
Det finns två sätt att exportera och importera data i MySQL Workbench: från objektets webbläsares snabbmeny eller från fönstret Navigatör. Varje metod har olika syften.

> [!NOTE]
> Om du lägger till en anslutning till MySQL – enskild server eller flexibel server (förhandsversion) på MySQL Workbench gör du följande:
> - För MySQL – enskild server kontrollerar du att användarnamnet har formatet *\<username@servername>* .
> - Använd endast för MySQL – flexibel *\<username>* server. Om du *\<username@servername>* använder för att ansluta misslyckas anslutningen.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Kör tabellguiden för dataexport och -import från objektwebbläsarens snabbmeny

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Skärmbild av export- och importkommandona för MySQL Workbench på objektwebbläsarens snabbmeny.":::

Tabelldataguiderna stöder import- och exportåtgärder med hjälp av CSV- och JSON-filer. Guiderna innehåller flera konfigurationsalternativ, till exempel avgränsare, kolumnval och kodningsval. Du kan köra varje guide mot lokala eller fjärranslutna MySQL-servrar. Importåtgärden innehåller mappning av tabeller, kolumner och typer.

Om du vill komma åt de här guiderna från objektwebbläsaren högerklickar du på en tabell och väljer sedan guiden Exportera tabelldata **eller** **guiden Importera tabelldata.**

#### <a name="the-table-data-export-wizard"></a>Guiden Exportera tabelldata

Så här exporterar du en tabell till en CSV-fil:

1. Högerklicka på tabellen i databasen som ska exporteras.
1. Välj **guiden Exportera tabelldata.** Välj de kolumner som ska exporteras, radförskjutning (om det finns några) och antal (om det finns några).
1. I fönstret **Välj data för export** väljer du **Nästa.** Välj filsökväg, CSV- eller JSON-filtyp. Välj även radavgränsare, metod för att omsluta strängar och fältavgränsare.
1. I fönstret **Välj plats för utdatafil** väljer du **Nästa.**
1. I fönstret **Exportera data** väljer du **Nästa.**

#### <a name="the-table-data-import-wizard"></a>Guiden importera tabelldata

Så här importerar du en tabell från en CSV-fil:

1. Högerklicka på tabellen för databasen som ska importeras.
1. Leta upp och välj den CSV-fil som ska importeras och välj sedan **Nästa.**
1. Markera måltabellen (ny eller befintlig), markera eller avmarkera kryssrutan **Trunkera** tabell före import och välj sedan **Nästa.**
1. Välj kodningen och kolumnerna som ska importeras och välj sedan **Nästa.**
1. I fönstret **Importera data** väljer du **Nästa.** Guiden importerar data.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Kör sql-dataexport- och importguiderna från fönstret Navigatör
Använd en guide för att exportera eller importera SQL-data som genereras från MySQL Workbench eller från mysqldump-kommandot. Du kan komma åt guiderna från **fönstret Navigatör** eller så kan **du välja Server** på huvudmenyn.

#### <a name="export-data"></a>Exportera data

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Skärmbild av hur du använder fönstret Navigatör för att visa fönstret Dataexport i MySQL Workbench.":::

Du kan använda fönstret **Dataexport** för att exportera MySQL-data.

1. I Fönstret **Navigatör** i MySQL Workbench väljer du **Dataexport.**

1. I fönstret **Dataexport** väljer du varje schema som du vill exportera.
 
   För varje schema kan du välja specifika schemaobjekt eller tabeller som ska exporteras. Konfigurationsalternativen omfattar export till en projektmapp eller en fristående SQL-fil, dumpar lagrade rutiner och händelser eller hoppar över tabelldata.

   Du kan också använda **Exportera en resultatuppsättning för** att exportera en specifik resultatuppsättning i SQL-redigeraren till ett annat format, till exempel CSV, JSON, HTML och XML.

1. Välj de databasobjekt som ska exporteras och konfigurera de relaterade alternativen.
1. Välj **Uppdatera för** att läsa in de aktuella objekten.
1. Du kan också välja **Avancerade alternativ längst** upp till höger för att förfina exportåtgärden. Du kan till exempel lägga till tabelllås, `replace` använda i stället för `insert` -instruktioner och offertidentifierare med bakåtklickstecken.
1. Välj **Starta export** för att påbörja exportprocessen.


#### <a name="import-data"></a>Importera data

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Skärmbild av hur du använder fönstret Navigatör för att visa fönstret Dataimport i MySQL Workbench.":::

Du kan använda **fönstret Dataimport** för att importera eller återställa exporterade data från dataexportåtgärden eller från kommandot mysqldump.

1. I Fönstret **Navigatör** i MySQL Workbench väljer du **Dataexport/återställ.**
1. Välj projektmappen eller den fristående SQL-filen, välj det schema som du vill importera till eller välj **knappen Nytt** för att definiera ett nytt schema.
1. Välj **Starta import** för att starta importen.

## <a name="next-steps"></a>Nästa steg
- En annan migreringsmetod finns i [Migrera din MySQL-databas till en Azure-databas för MySQL](concepts-migrate-dump-restore.md)med hjälp av dumpning och återställning.
- Mer information om hur du migrerar databaser till en Azure-databas för MySQL finns i [Guiden för databasmigrering.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)
