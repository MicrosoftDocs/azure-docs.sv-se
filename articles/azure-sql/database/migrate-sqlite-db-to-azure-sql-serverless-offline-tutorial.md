---
title: 'Självstudie: så här migrerar du din SQLite-databas till Azure SQL Database Server lös'
description: Lär dig att utföra en offline-migrering från SQLite till Azure SQL Database Server lös genom att använda Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790414"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Migrera din SQLite-databas till Azure SQL Database Server lös
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

För många användare ger SQLite sin första upplevelse av databaser och SQL-programmering. Den ingår i många operativ system och populära program gör SQLite till en mest distribuerad och använda databas motorer i världen. Och eftersom det är troligt att den första databas motorn många användare använder, kan den ofta bli en central del av projekt eller program. I sådana fall där projektet eller programmet ökar den inledande SQLite-implementeringen, kan utvecklare behöva migrera sina data till ett tillförlitligt centraliserat data lager.

Azure SQL Database utan server är en beräknings nivå för enskilda databaser som automatiskt skalar beräkning baserat på arbets belastnings behov och fakturerar för mängden data bearbetning som används per sekund. På den serverlösa beräkningsnivån pausas databaser automatiskt under inaktiva perioder och då faktureras endast lagring. Databaserna startas igen automatiskt när aktiviteten återupptas.

När du har följt stegen nedan kommer databasen att migreras till Azure SQL Database Server lös, så att du kan göra databasen tillgänglig för andra användare eller program i molnet och bara betala för det du använder, med minimala program kod ändringar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration
- SQLite2-eller SQLite3-databas som du vill migrera
- En Windows-miljö
  - Om du inte har en lokal Windows-miljö kan du använda en virtuell Windows-dator i Azure för migreringen. Flytta och gör din SQLite-databasfil tillgänglig på den virtuella datorn med hjälp av Azure Files och Storage Explorer.

## <a name="steps"></a>Steg

1. Etablera en ny Azure SQL Database i Server lös beräknings nivån.

    ![Skärm bild av Azure Portal som visar etablerings exemplet för Azure SQL Database utan Server](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Se till att du har din SQLite-databasfil tillgänglig i Windows-miljön. Installera en SQLite ODBC-drivrutin om du inte redan har en (det finns många tillgängliga i öppen källkod, till exempel http://www.ch-werner.de/sqliteodbc/) .

3. Skapa en system-DSN för-databasen. Se till att du använder den data källans administratörs program som matchar system arkitekturen (32-bitars vs 64-bit). Du kan ta reda på vilken version som körs i systeminställningarna.

    - Öppna ODBC-administratör för data källa i din miljö.
    - Klicka på fliken System-DSN och klicka på Lägg till
    - Välj den SQLite ODBC-anslutning som du har installerat och ge anslutningen ett meningsfullt namn, till exempel sqlitemigrationsource
    - Ange databas namnet till. DB-filen
    - Spara och avsluta

4. Ladda ned och installera integration runtime med egen värd. Det enklaste sättet att göra detta är alternativet snabb installation enligt beskrivningen i dokumentationen. Om du väljer att installera manuellt måste du ange programmet med en autentiseringsnyckel, som kan finnas i din Data Factory instans genom att:

    - Starta ADF (författare och övervakare från tjänsten i Azure Portal)
    - Klicka på fliken "författare" (blå penna) till vänster
    - Klicka på anslutningar (längst ned till vänster) och sedan på integration runtime
    - Lägg till ny Self-Hosted Integration Runtime, ge den ett namn och välj *Alternativ 2*.

5. Skapa en ny länkad tjänst för käll-SQLite-databasen i din Data Factory.

    ![Skärm bild som visar tomma blad för länkade tjänster i Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. I **anslutningar**, under **länkad tjänst**, klickar du på **ny**.

7. Sök efter och välj "ODBC"-anslutningen

   ![Skärm bild som visar ODBC Connector-logotypen på bladet länkade tjänster i Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Ge den länkade tjänsten ett beskrivande namn, till exempel "sqlite_odbc". Välj integration runtime från List rutan "Anslut via integration runtime". Ange följande i anslutnings strängen, Ersätt den inledande katalog variabeln med fil Sök vägen för. DB-filen och DSN med namnet på system-DSN-anslutningen:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Ange autentiseringstypen Anonym

10. Testa anslutningen

    ![Skärm bild som visar lyckad anslutning i Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Skapa en annan länkad tjänst för server utan SQL-mål. Välj databasen med guiden länkad tjänst och ange autentiseringsuppgifterna för SQL-autentisering.

    ![Skärm bild som visar Azure SQL Database valt i Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extrahera CREATE TABLE-satserna från din SQLite-databas. Du kan göra detta genom att köra det tidigare python-skriptet på databas filen.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Skapa landnings tabellerna i din server utan SQL-mål miljö genom att kopiera instruktionen CREATE tables från CreateTables. SQL-filen och köra SQL-instruktionerna i Frågeredigeraren i Azure Portal.

14. Gå tillbaka till Start skärmen för din Data Factory och klicka på "Kopiera data" för att köra guiden skapa jobb.

    ![Skärm bild som visar Kopiera data-guidens logo typ i Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Välj alla tabeller från käll-SQLite-databasen med hjälp av kryss rutorna och mappa dem till mål tabellerna i Azure SQL. När jobbet har körts har du migrerat dina data från SQLite till Azure SQL!

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [snabb start: skapa en enda databas i Azure SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md).
- För resurs gränser, se [resurs gränser för Server lös beräknings nivå](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).