---
title: 'Självstudie: läsa in New York Taxidata-data'
description: Självstudier använder Azure Portal och SQL Server Management Studio för att läsa in New York Taxidata-data från en Azure-Blob för Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ec577e2a70e2b354b8d2013fe259aa9ea622c50e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120146"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Självstudie: Läs in New York Taxidata-datauppsättningen

I den här självstudien används [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) för att läsa in New York taxidata data uppsättning från ett Azure Blob Storage-konto. I självstudierna används [Azure-portalen](https://portal.azure.com) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) för att:

> [!div class="checklist"]
>
> * Skapa en användare som utsetts för att läsa in data
> * Skapa tabellerna för exempel data uppsättningen 
> * Använd instruktionen COPY T-SQL för att läsa in data i informations lagret
> * Visa dataförloppet vid hämtning

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar med de här självstudierna ska du ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).  

Den här självstudien förutsätter att du redan har skapat en dedikerad SQL-pool från följande [självstudie](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin).

## <a name="create-a-user-for-loading-data"></a>Skapa en användare för att läsa in data

Serveradministratörskontot är avsett för att utföra hanteringsåtgärder och är inte lämpligt för att köra frågor på användardata. Datainläsning är en minneskrävande åtgärd. Högsta minnes storlek definieras enligt de konfigurerade [data lager enheterna](what-is-a-data-warehouse-unit-dwu-cdwu.md) och [resurs klassen](resource-classes-for-workload-management.md) .

Det är bäst att skapa en särskild inloggning och en särskild användare för inläsning av data. Lägg sedan till inläsningsanvändaren i en [resursklass](resource-classes-for-workload-management.md) som möjliggör en lämplig maximal minnesallokering.

Anslut som Server administratör så att du kan skapa inloggningar och användare. Följ dessa steg för att skapa en inloggning och användare som kallas för **LoaderRC20**. Tilldela användaren resursklassen **staticrc20**.

1. I SSMS högerklickar du på **huvud** för att visa en nedrullningsbar meny och väljer **ny fråga**. Ett nytt frågefönster öppnas.

    ![Ny fråga i huvuddatabas](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. I frågefönstret anger du dessa T-SQL-kommandon för att skapa en inloggning och en användare som kallas för LoaderRC20 och ersätter 123STRONGpassword! med ditt eget lösenord.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Välj **Kör**.

4. Högerklicka på **mySampleDataWarehouse** och välj **Ny fråga**. Ett nytt frågefönster öppnas.  

    ![Ny fråga på exempelinformationslagret](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Ange följande T-SQL-kommandon för att skapa en databasanvändare som kallas för LoaderRC20 till inloggningen LoaderRC20. Den andra raden ger den nya användaren kontrollbehörighet på det nya informationslagret.  Dessa behörigheter påminner om att göra användaren till databasens ägare. Den tredje raden lägger till den nya användaren som medlem i [resursklassen](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Välj **Kör**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Ansluta till servern som inläsningsanvändare

Första steget mot att läsa in data är att logga in som LoaderRC20.  

1. I Object Explorer väljer du den nedrullningsbara menyn **Anslut** och väljer **databas motor**. Dialogrutan **Anslut till server** visas.

    ![Ansluta med ny inloggning](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Ange det fullständigt kvalificerade servernamnet och ange **LoaderRC20** som inloggning.  Ange lösenordet för LoaderRC20.

3. Välj **Anslut**.

4. När anslutningen är klar visas två serveranslutningar i Object Explorer. En anslutning som Serveradmin och en anslutning som MedRCLogin.

    ![Anslutningen lyckades](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Skapa tabeller för exempel data

Du är redo att börja läsa in data till ditt nya informationslager. Den här delen av självstudien visar hur du använder COPY-instruktionen för att läsa in New York City Cab-datauppsättningen från en Azure Storage-blob. Information om hur du hämtar dina data till Azure Blob Storage eller läser in dem direkt från din källa finns i [Översikt över inläsning](design-elt-data-loading.md).

Kör följande SQL-skript och ange information om de data du vill läsa in. Informationen omfattar var informationen finns, formatet för innehållet i aktuella data och tabelldefinitionen för dessa data.

1. I det föregående avsnittet loggade du in på ditt informationslager som LoaderRC20. Högerklicka på anslutningen LoaderRC20 i SSMS, och välj **Ny fråga**.  Ett nytt frågefönster visas.

    ![Nytt fönster för inläsning av fråga](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Jämför ditt frågefönster med föregående bild.  Kontrollera att ditt nya frågefönster körs som LoaderRC20 och kör frågor på MySampleDataWarehouse-databasen. Använd det här frågefönstret för att utföra alla inläsningssteg.

7. Kör följande T-SQL-uttryck för att skapa tabellerna:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Läsa in data till informationslagret

I det här avsnittet används [kopierings instruktionen för att läsa in](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) exempel data från Azure Storage blob.  

> [!NOTE]
> De här självstudierna läser in data direkt till den slutliga tabellen. Du skulle vanligt vis läsa in i en mellanlagrings tabell för dina produktions arbets belastningar. Du kan utföra alla nödvändiga omvandlingar när data är i mellanlagringstabellen. 

1. Kör följande instruktioner för att läsa in data:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Visa data som laddas. Du läser in flera GB data och komprimerar dem till högpresterande grupperade columnstore-index. Kör följande fråga som använder en dynamisk hanteringsvy (DMV) för att visa status för belastningen.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Visa alla systemfrågor.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Se hur dina data läses in i ditt informationslager.

    ![Visa inlästa tabeller](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för beräkningsresurser och data som du har läst in i ditt informationslager. Dessa faktureras separat.

* Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkningen kommer du bara att debiteras för datalagringen och du kan återuppta beräkningen när du är redo att arbeta med dina data.
* Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj ditt informations lager.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort data lagret så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

4. Om du vill ta bort den server som du har skapat väljer du **mynewserver-20180430.Database.Windows.net** i föregående bild och väljer sedan **ta bort**.  Var försiktig: om du tar bort servern tas nämligen alla databaser som servern har tilldelats bort.

5. Om du vill ta bort resurs gruppen väljer du **myResourceGroup** och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skapa ett informationslager och skapa en användare för att läsa in data. Du använde den enkla [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) för att läsa in data i informations lagret.

Du gjorde detta:
> [!div class="checklist"]
>
> * Skapade ett informationslager på Azure-portalen
> * Skapade en brandväggsregel på servernivå på Azure-portalen
> * Anslöt till informationslagret med SSMS
> * Skapade en användare för inläsning av data
> * Skapade tabellerna för exempel data
> * Använde instruktionen COPY T-SQL för att läsa in data i informations lagret
> * Visade förloppet för data under inläsning

Gå vidare till utvecklings översikten och lär dig hur du migrerar en befintlig databas till Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Design beslut för att migrera en befintlig databas till Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Läs följande dokumentation om du vill läsa mer om exempel och referenser:

- [Referens dokumentation för KOPIERINGs instruktion](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Kopiera exempel för varje autentiseringsmetod](./quickstart-bulk-load-copy-tsql-examples.md)
- [Kopiera snabb start för en enskild tabell](./quickstart-bulk-load-copy-tsql.md)