---
title: 'Självstudie: komma igång med att analysera data med dedikerade SQL-pooler'
description: I den här självstudien använder du NYC taxi-exempel data för att utforska SQL-poolens analys funktioner.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 54b650d598cf19e061465b3a4fa18d50808e7f29
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426169"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analysera data med dedikerade SQL-pooler

Azure Synapse Analytics ger dig möjlighet att analysera data med en särskild SQL-pool. I den här självstudien använder du NYC taxi-data för att utforska en dedikerad SQL-Pools funktioner.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Läs in NYC taxi-data till SQLPOOL1

1. Navigera till **utveckla** hubben i Synapse Studio, klicka på **+** knappen för att lägga till en ny resurs och skapa sedan ett nytt SQL-skript.
1. Välj poolen "SQLPOOL1" (pool som skapades i [steg 1](./get-started-create-workspace.md) i den här själv studie kursen) i list rutan "Anslut till" ovanför skriptet.
1. Ange följande kod:
    ```
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

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
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
1. Klicka på knappen Kör för att köra skriptet.
1. Detta skript slutförs på mindre än 60 sekunder. Den läser in 2 000 000 rader av NYC taxi-data i en tabell med namnet **dbo. Resan**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Utforska NYC taxi-data i den dedikerade SQL-poolen

1. Gå till **datahubben** i Synapse Studio.
1. Du bör se en databas med namnet **SQLPOOL1**. Om du inte ser det klickar du på **Uppdatera**.
1. Gå till **SQLPOOL1**-  >  **tabeller**. 
3. Högerklicka på **dbo. Rese** tabell och välj **nytt SQL-skript**  >  **Markera de 100 översta raderna**.
4. Vänta medan ett nytt SQL-skript skapas och körs.
5. Observera att överst i SQL-skriptet **Connect to** anges automatiskt till SQL-poolen med namnet **SQLPOOL1**.
6. Ersätt texten i SQL-skriptet med den här koden och kör den.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Den här frågan visar hur det totala antalet rese avstånd och det genomsnittliga rese avståndet avser antalet passagerare.
1. I resultat fönstret för SQL-skript ändrar du **vyn** till **diagram** för att visa en visualisering av resultaten som ett linje diagram.
    
    > [!NOTE]
    > En fast arbets yta som är aktive rad SQL-pool (tidigare SQL DW) kan identifieras via verktygs tipset i data hubben.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med Spark](get-started-analyze-spark.md)
