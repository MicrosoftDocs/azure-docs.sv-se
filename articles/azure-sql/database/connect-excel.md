---
title: Anslut med Excel
description: Lär dig hur du ansluter Microsoft Excel till en databas i Azure SQL Database eller en hanterad Azure SQL-instans. Importera data till Excel för rapportering och dataundersökning.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91444038"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Anslut Excel till en databas i Azure SQL Database eller Azure SQL-hanterad instans och skapa en rapport
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Du kan ansluta Excel till en databas och sedan importera data och skapa tabeller och diagram baserat på värden i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du måste skapa en databas innan du börjar. Om du inte har någon kan du läsa [skapa en databas i Azure SQL Database](single-database-create-quickstart.md) och [skapa IP-brandvägg på server nivå](firewall-create-server-level-portal-quickstart.md) för att få en databas med exempel data igång på några få minuter.

I den här artikeln ska du importera exempel data till Excel från den artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-and-load-data"></a>Ansluta Excel och läsa in data

1. Om du vill ansluta Excel till en databas i SQL Database öppnar du Excel och skapar en ny arbets bok eller öppnar en befintlig Excel-arbetsbok.
2. Välj fliken **data** på Meny raden överst på sidan, Välj **Hämta data**, Välj från Azure och välj sedan **från Azure SQL Database**.

   ![Välj data Källa: Anslut Excel till SQL Database.](./media/connect-excel/excel_data_source.png)

3. I dialog rutan **SQL Server databas** skriver du det **Server namn** som du vill ansluta till i formatet <*servername* > **. Database.Windows.net**. Till exempel **msftestserver.Database.Windows.net**. Du kan också ange namnet på din databas. Välj **OK** för att öppna fönstret autentiseringsuppgifter.

   ![Dialog rutan Anslut till databas server](./media/connect-excel/server-name.png)

4. I dialog rutan **SQL Server databas** väljer du **databas** till vänster och anger sedan **användar namn** och **lösen ord** för den server som du vill ansluta till. Välj **Anslut** för att öppna **navigatören**.

   ![Ange servernamn och inloggningsuppgifter](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > Beroende på din nätverks miljö kanske du inte kan ansluta eller så kan du förlora anslutningen om servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](firewall-configure.md) för mer information.

5. I **navigerings** fönstret väljer du den databas som du vill arbeta med i listan, väljer de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**) och väljer sedan **load** för att flytta data från databasen till ditt Excel-kalkylblad.

    ![Välj en databas och en tabell.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram

Nu när du har upprättat anslutningen har du flera olika alternativ för hur du läser in data. Följande steg skapar till exempel ett pivot-diagram baserat på de data som finns i databasen i SQL Database.

1. Följ stegen i föregående avsnitt, men i stället för att välja **Läs** in väljer **du Läs in till** **från List rutan för inläsning.**
2. Välj sedan hur du vill visa data i din arbets bok. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Välja dataformat i Excel](./media/connect-excel/import-data.png)

    Kalkylbladet har nu en tom pivottabell och diagram.
3. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.

    ![Konfigurera databasrapporten.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkyl blad till databasen väljer du fliken **data** och väljer sedan **senaste källor** för att starta dialog rutan **senaste källor** . Därifrån väljer du den anslutning som du skapade i listan och klickar sedan på **Öppna**.
> ![Dialog rutan senaste källor](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Skapa en permanent anslutning med hjälp av. ODC-fil

Om du vill spara anslutnings informationen permanent kan du skapa en. ODC-fil och göra anslutningen till ett valbart alternativ i dialog rutan **befintliga anslutningar** .

1. I meny raden överst på sidan väljer du fliken **data** och väljer sedan **befintliga anslutningar** för att starta dialog rutan **befintliga anslutningar** .
   1. Välj **Bläddra för mer** för att öppna dialog rutan **Välj data källa** .
   2. Välj filen **+ NewSqlServerConnection. ODC** och välj sedan **Öppna** för att öppna **guiden data anslutning**.

      ![Dialog rutan ny anslutning](./media/connect-excel/new-connection.png)

2. I **guiden data anslutning** anger du Server namnet och dina SQL Database autentiseringsuppgifter. Välj **Nästa**.
   1. Välj den databas som innehåller dina data från List rutan.
   2. Välj den tabell eller vy som du är intresse rad av. Vi valde vGetAllCategories.
   3. Välj **Nästa**.

      ![Guiden data anslutning](./media/connect-excel/data-connection-wizard.png)

3. Välj platsen för filen, **fil namnet** och det **egna namnet** på nästa skärm i guiden data anslutning. Du kan också välja att spara lösen ordet i filen, men det kan eventuellt exponera dina data till oönskad åtkomst. Välj **Slutför** när du är klar.

    ![Spara data anslutning](./media/connect-excel/save-data-connection.png)

4. Välj hur du vill importera dina data. Vi valde att göra en pivottabell. Du kan också ändra egenskaperna för anslutningen genom att välja **Egenskaper**. Välj **OK** när du är klar. Om du inte väljer att spara lösen ordet med filen uppmanas du att ange dina autentiseringsuppgifter.

    ![Importera data](./media/connect-excel/import-data2.png)

5. Kontrol lera att din nya anslutning har sparats genom att expandera fliken **data** och välja **befintliga anslutningar**.

    ![Befintlig anslutning](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter och frågar med SQL Server Management Studio](connect-query-ssms.md) för avancerade frågor och analyser.
* Lär dig mer om fördelarna med [elastiska pooler](elastic-pool-overview.md).
* Lär dig hur du [skapar ett webb program som ansluter till Azure SQL Database på Server](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md)sidan.
