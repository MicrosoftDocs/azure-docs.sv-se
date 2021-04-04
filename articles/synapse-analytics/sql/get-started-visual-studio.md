---
title: Anslut och fråga Synapse SQL med Visual Studio och SSDT
description: Använd Visual Studio för att fråga en dedikerad SQL-pool med Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451590"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Ansluta till Synapse SQL med Visual Studio och SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [SQLCMD](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Använd Visual Studio för att fråga en dedikerad SQL-pool med Azure Synapse Analytics. Den här metoden använder tillägget SQL Server Data Tools (SSDT) i Visual Studio 2019. 

> [!NOTE]
> SQL-poolen utan Server stöds inte av SSDT.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här självstudien måste du ha följande komponenter:

* En befintlig dedikerad SQL-pool. Om du inte har en sådan, se [skapa en dedikerad SQL-pool](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att slutföra den här förutsättningen.
* SSDT för Visual Studio. Om du har Visual Studio har du förmodligen redan den här komponenten. Installationsinstruktioner och alternativ finns i [Installera Visual Studio och SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Det fullständigt kvalificerade servernamnet. För att hitta det här Server namnet, se [Anslut till en dedikerad SQL-pool](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. Anslut till en dedikerad SQL-pool
1. Öppna Visual Studio 2019.
2. Öppna SQL Server Object Explorer genom att välja **Visa**  >  **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Klicka på ikonen **lägg till SQL Server**.
   
    ![Lägg till SQL Server](./media/get-started-visual-studio/add-server.png)
4. Fyll i fälten i fönstret anslut till server.
   
    ![Anslut till server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Server namn**: Ange det **Server namn** som du identifierade tidigare.
   * **Autentisering**: Välj **SQL Server autentisering** eller **Active Directory integrerad autentisering**:
   * **Användar namn** och **lösen ord**: Ange ditt användar namn och lösen ord om SQL Server autentisering valdes ovan.
   * Klicka på **Anslut**.
5. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.
   
    ![Utforska AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. kör en exempel fråga
Nu när en anslutning har upprättats till databasen skriver du en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **Ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga](./media/get-started-visual-studio/new-query2.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan genom att klicka på den gröna pilen eller Använd följande genväg: `CTRL` + `SHIFT` + `E` .
   
    ![Kör frågan](./media/get-started-visual-studio/run-query.png)
5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan du prova [att visualisera data med Power BI](get-started-power-bi-professional.md).
Information om hur du konfigurerar din miljö för Azure Active Directory autentisering finns i [autentisera till dedikerad SQL-pool](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 