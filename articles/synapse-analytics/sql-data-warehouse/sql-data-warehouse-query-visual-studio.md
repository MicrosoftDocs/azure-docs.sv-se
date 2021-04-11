---
title: Anslut till dedikerad SQL-pool (tidigare SQL DW) med VSTS
description: Fråga dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med Visual Studio.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584150"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Ansluta till dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med Visual Studio och SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [SQLCMD](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Använd Visual Studio för att fråga en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse på bara några minuter. Den här metoden använder tillägget SQL Server Data Tools (SSDT) i Visual Studio 2019. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att använda de här självstudierna:

* En befintlig dedikerad SQL-pool (tidigare SQL DW). Om du vill skapa en, se [skapa en dedikerad SQL-pool (tidigare SQL DW)](create-data-warehouse-portal.md).
* SSDT för Visual Studio. Om du har Visual Studio har du förmodligen redan SSDT för Visual Studio. Installationsinstruktioner och alternativ finns i [Installera Visual Studio och SSDT](sql-data-warehouse-install-visual-studio.md).
* Det fullständigt kvalificerade servernamnet. Information om hur du hittar den här informationen finns i [Anslut till en dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Anslut till din dedikerade SQL-pool (tidigare SQL DW)
1. Öppna Visual Studio 2019.
2. Öppna SQL Server Object Explorer genom att välja **Visa**  >  **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klicka på ikonen **lägg till SQL Server**.
   
    ![Lägg till SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Server namn**. Ange det **servernamn** som du identifierade tidigare.
   * **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
   * **Användar namn** och **lösen ord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
   * Klicka på **Anslut**.
5. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. kör en exempel fråga
När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **Ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan genom att klicka på den gröna pilen eller Använd följande genväg: `CTRL` + `SHIFT` + `E` .
   
    ![Kör frågan](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Information om hur du konfigurerar din miljö för Azure Active Directory autentisering finns i [autentisera till dedikerad SQL-pool (tidigare SQL DW)](sql-data-warehouse-authentication.md).