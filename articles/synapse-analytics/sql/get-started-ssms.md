---
title: Ansluta till Synapse SQL med SQL Server Management Studio (SSMS)
description: Använd SQL Server Management Studio (SSMS) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 222505b03aac5bfc4e8d00b8c8977bece34dee85
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567527"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Ansluta till Synapse SQL med SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Du kan använda [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics antingen via en serverlös SQL-pool eller dedikerade SQL-poolresurser. 

### <a name="supported-tools-for-serverless-sql-pool"></a>Verktyg som stöds för serverlös SQL-pool

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) stöds fullt ut från och med version 1.18.0. SSMS stöds delvis från och med version 18.5. Du kan använda det för att ansluta och endast fråga.

> [!NOTE]
> Om AAD-inloggningen har en öppen anslutning i mer än en timme vid tidpunkten för frågekörningen misslyckas alla frågor som förlitar sig på AAD. Detta inkluderar frågor om lagring med hjälp av AAD-genomsnidering och instruktioner som interagerar med AAD (t.ex. CREATE EXTERNAL PROVIDER). Detta påverkar alla verktyg som håller anslutningen öppen, som i frågeredigeraren i SSMS och ADS. Verktyg som öppnar en ny anslutning för att köra frågor påverkas inte, till exempel Synapse Studio.
> Du kan starta om SSMS eller ansluta och koppla från ADS för att åtgärda det här problemet. .
## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande krav innan du börjar:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* För dedikerad SQL-pool behöver du ett befintligt informationslager. Information om hur du skapar en finns [i Skapa en dedikerad SQL-pool.](../quickstart-create-sql-pool-portal.md) För serverlös SQL-pool har en redan etablerats, med namnet Inbyggd, på din arbetsyta när den skapas. 
* Det fullständigt kvalificerade SQL Server namnet. Information om hur du hittar det här [namnet finns i Anslut till Synapse SQL](connect-overview.md).

## <a name="connect"></a>Ansluta

### <a name="dedicated-sql-pool"></a>Dedikerad SQL-pool

Följ dessa steg Synapse SQL ansluta till en dedikerad SQL-pool: 

1. Öppna SQL Server Management Studio (SSMS). 
1. I **dialogrutan Anslut till** server fyller du i fälten och väljer sedan **Anslut:** 
  
    ![Anslut till Server 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Servernamn:** Ange det **servernamn som har identifierats** tidigare.
   * **Autentisering:** Välj en autentiseringstyp, till exempel **SQL Server autentisering** eller **Active Directory-integrerad autentisering.**
   * **Användarnamn och** **lösenord:** Ange ditt användarnamn och lösenord om du SQL Server autentisering valdes ovan.

1. Expandera din Azure SQL Server i **Object Explorer**. Du kan visa databaserna som är associerade med servern, till exempel AdventureWorksDW-exempeldatabasen. Du kan expandera databasen för att se tabellerna:
   
    ![Utforska AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Serverlös SQL-pool

Följ dessa steg Synapse SQL ansluta till en serverlös SQL-pool: 

1. Öppna SQL Server Management Studio (SSMS).
1. I **dialogrutan Anslut till** server fyller du i fälten och väljer sedan **Anslut:** 
   
    ![Anslut till Server 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Servernamn:** Ange det **servernamn som har identifierats** tidigare.
   * **Autentisering:** Välj en autentiseringstyp, till exempel **SQL Server eller** **Active Directory-integrerad autentisering:**
   * **Användarnamn och** **lösenord:** Ange ditt användarnamn och lösenord om du SQL Server autentisering valdes ovan.
   * Välj **Anslut**.

4. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera *demo* för att se innehållet i exempeldatabasen.
   
    ![Utforska AdventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Kör en exempelfråga

### <a name="dedicated-sql-pool"></a>Dedikerad SQL-pool

Nu när en databasanslutning har upprättats kan du köra frågor mot data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **Ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Kör frågan genom att `Execute` välja eller använda följande genväg: `F5` .
   
    ![Kör fråga 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Titta på frågeresultaten. I följande exempel har tabellen FactInternetSales 60398 rader.
   
    ![Frågeresultat 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Serverlös SQL-pool

Nu när du har upprättat en databasanslutning kan du köra frågor mot data.

1. Högerklicka på din databas i SQL Server Object Explorer.
2. Välj **Ny fråga**. Ett nytt frågefönster öppnas.
   
    ![Ny fråga 2](./media/get-started-ssms/new-query.png)
3. Kopiera följande T-SQL-fråga till frågefönstret:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Kör frågan genom att `Execute` välja eller använda följande genväg: `F5` .
   
    ![Kör fråga 2](./media/get-started-ssms/execute-query.png)
5. Titta på frågeresultaten. I det här exemplet har usPopulationView-vyn 3664512 rader.
   
    ![Frågeresultat 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan ansluta och fråga kan [du visualisera data med hjälp av Power BI](get-started-power-bi-professional.md).

Information om hur du konfigurerar din miljö Azure Active Directory autentisering finns [i Autentisera till Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

