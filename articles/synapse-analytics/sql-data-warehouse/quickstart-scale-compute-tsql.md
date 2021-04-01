---
title: 'Snabb start: skala beräkning i dedikerad SQL-pool (tidigare SQL DW)-T-SQL'
description: Skala beräkning i dedikerad SQL-pool (tidigare SQL DW) med T-SQL och SQL Server Management Studio (SSMS). Skala ut beräkning för bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ffffeb38aeb9d1f01f376d58a52323bb7b84b306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676331"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Snabb start: skala beräkning för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av T-SQL

Skala beräkning i dedikerad SQL-pool (tidigare SQL DW) med T-SQL och SQL Server Management Studio (SSMS). [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala upp beräkning för att spara kostnader.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Skapa en dedikerad SQL-pool (tidigare SQL DW)

Använd [snabb start: skapa och Anslut – Portal](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool (tidigare SQL DW) med namnet **mySampleDataWarehouse**. Slutför snabb starten för att se till att du har en brand Väggs regel och kan ansluta till din dedikerade SQL-pool (tidigare SQL DW) inifrån SQL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

I det här avsnittet används [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) för att upprätta en anslutning till Azure SQL-servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Servertyp | Databasmotor | Det här värdet är obligatoriskt |
   | Servernamn | Fullständigt kvalificerat servernamn | Här är ett exempel: **mySampleDataWarehouseservername.Database.Windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi konfigurerar i den här självstudiekursen. |
   | Inloggning | Serveradministratörskontot | Kontot som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Det lösen ord som du angav när du skapade servern. |

    ![Anslut till servern](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS.

4. Expandera **Databaser** i Object Explorer. Expandera sedan **mySampleDataWarehouse** för att visa objekten i den nya databasen.

    ![Databas objekt](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Visa tjänstmål

Inställningen tjänst mål innehåller antalet informations lager enheter för den dedikerade SQL-poolen (tidigare SQL DW).

Visa de aktuella data lager enheterna för din dedikerade SQL-pool (tidigare SQL DW):

1. Under anslutningen till **mySampleDataWarehouseservername.Database.Windows.net** expanderar du **system databaser**.
2. Högerklicka på **huvuddatabas** och välj **Ny fråga**. Ett nytt frågefönster öppnas.
3. Kör följande fråga för att välja från den dynamiska hanteringsvyn sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Följande resultat visar att **mySampleDataWarehouse** har tjänstmålet DW400.

    ![a-Current-DWU](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Skala beräkning

I dedikerad SQL-pool (tidigare SQL DW) kan du öka eller minska beräknings resurserna genom att justera informations lager enheter. I [Skapa och ansluta – portal](create-data-warehouse-portal.md) skapades **mySampleDataWarehouse** och initierades med 400 DWU. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Högerklicka på **huvuddatabas** och välj **Ny fråga**.
2. Använd T-SQL-instruktionen [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att ändra tjänstmålet. Kör följande fråga för att ändra tjänstmålet till DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Övervaka ändringsbegäran för skalning

Om du vill visa förloppet för den tidigare ändringsbegäran kan du använda T-SQL-syntaxen `WAITFORDELAY` för att göra en avsökning av den dynamiska hanteringsvyn (DMV) sys.dm_operation_status.

Så här avsöker du serviceobjektets ändringsstatus:

1. Högerklicka på **huvuddatabas** och välj **Ny fråga**.
2. Gör en avsökning av den dynamiska hanteringsvyn sys.dm_operation_status genom att köra följande fråga.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. Resultatet innehåller en logg över statusavsökningen.

    ![Åtgärdsstatus](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>Kontrol lera dedikerad SQL-pool (tidigare SQL DW)

När en dedikerad SQL-pool (tidigare SQL DW) har pausats kan du inte ansluta till den med T-SQL. Om du vill se aktuell status för den dedikerade SQL-poolen (tidigare SQL DW) kan du använda en PowerShell-cmdlet. Ett exempel finns i [kontrol lera dedikerad SQL-pool (tidigare SQL DW) – PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>Kontrollera åtgärdsstatus

Om du vill returnera information om olika hanterings åtgärder på den dedikerade SQL-poolen (tidigare SQL DW) kör du följande fråga på [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) DMV. Den returnerar exempelvis åtgärden och åtgärdens tillstånd, som är IN_PROGRESS eller COMPLETED.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du skalar beräkningen för din dedikerade SQL-pool (tidigare SQL DW). Om du vill veta mer om Azure Synapse Analytics kan du fortsätta till självstudien för att läsa in data.

> [!div class="nextstepaction"]
>[Läs in data i en dedikerad SQL-pool](./load-data-from-azure-blob-storage-using-copy.md)