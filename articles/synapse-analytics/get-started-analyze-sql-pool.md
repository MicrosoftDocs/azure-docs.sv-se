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
ms.date: 03/24/2021
ms.openlocfilehash: 4588eee721a58a7e4f3366d0d325b48de0f56ae5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259820"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analysera data med dedikerade SQL-pooler

I den här självstudien använder du NYC taxi-data för att utforska en dedikerad SQL-Pools funktioner.

## <a name="create-a-dedicated-sql-pool"></a>Skapa en dedikerad SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler** i det vänstra fönstret.
1. Välj **nytt**
1. Välj **SQLPOOL1** för **SQL-poolnamn**
1. Välj **DW100C** för **prestanda nivå**
1. Välj **Granska + skapa** > **Skapa**. Din dedikerade SQL-pool är klar om några minuter. 

Din dedikerade SQL-pool är kopplad till en SQL-databas som också kallas **SQLPOOL1**.
1. Navigera till **data**  >  **arbets ytan**.
1. Du bör se en databas med namnet **SQLPOOL1**. Om du inte ser det klickar du på **Uppdatera**.

En dedikerad SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

> [!NOTE] 
> När du skapar en ny dedikerad SQL-pool (tidigare SQL DW) på din arbets yta öppnas sidan dedikerad etablering av SQL-pool. Etableringen sker på den logiska SQL-servern.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Läs in NYC taxi-data till SQLPOOL1

1. Navigera till **utveckla** hubben i Synapse Studio, klicka på **+** knappen för att lägga till en ny resurs och skapa sedan ett nytt SQL-skript.
1. Välj poolen "SQLPOOL1" (pool som skapades i [steg 1](./get-started-create-workspace.md) i den här själv studie kursen) i list rutan "Anslut till" ovanför skriptet.
1. Ange följande kod:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Klicka på knappen Kör för att köra skriptet.
1. Detta skript slutförs på mindre än 60 sekunder. Den läser in 2 000 000 rader av NYC taxi-data i en tabell med namnet **dbo. Resan**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Utforska NYC taxi-data i den dedikerade SQL-poolen

1. Gå till **datahubben** i Synapse Studio.
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
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data i ett Azure Storage konto](get-started-analyze-storage.md)
