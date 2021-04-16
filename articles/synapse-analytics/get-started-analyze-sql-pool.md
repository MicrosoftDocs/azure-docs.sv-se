---
title: 'Självstudie: Kom igång med att analysera data med dedikerade SQL-pooler'
description: I den här självstudien använder du exempeldata från NYC Taxi för att utforska SQL-poolens analysfunktioner.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536331"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analysera data med dedikerade SQL-pooler

I den här självstudien använder du nyc-taxidata för att utforska en dedikerad SQL-pools funktioner.

## <a name="create-a-dedicated-sql-pool"></a>Skapa en dedikerad SQL-pool

1. I Synapse Studio väljer du Hantera SQL-pooler i **fönstret**  >  **till vänster.**
1. Välj **Ny**
1. För **SQL-poolnamn** väljer **du SQLPOOL1**
1. För **Prestandanivå väljer** du **DW100C**
1. Välj **Granska + skapa** > **Skapa**. Din dedikerade SQL-pool är klar om några minuter. 

Din dedikerade SQL-pool är associerad med en SQL-databas som även kallas **SQLPOOL1.**
1. Gå till **Dataarbetsyta.**  >  
1. Du bör se en databas med **namnet SQLPOOL1.** Om du inte ser det klickar du på **Uppdatera**.

En dedikerad SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

> [!NOTE] 
> När du skapar en ny dedikerad SQL-pool (tidigare SQL DW) på arbetsytan öppnas sidan för dedikerad SQL-pooletablering. Etablering sker på den logiska SQL-servern.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Läsa in NYC-taxidata i SQLPOOL1

1. I Synapse Studio du till hubben **Utveckla,** klickar på knappen för **+** att lägga till en ny resurs och skapar sedan ett nytt SQL-skript.
1. Välj poolen "SQLPOOL1" (poolen som skapades i STEG [1](./get-started-create-workspace.md) i den här självstudien) i listrutan "Anslut till" ovanför skriptet.
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

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Klicka på knappen Kör för att köra skriptet.
1. Det här skriptet slutförs på mindre än 60 sekunder. Den läser in 2 miljoner rader med data från taxibilar i en tabell som **heter dbo. Resa**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Utforska NYC Taxi-data i den dedikerade SQL-poolen

1. I Synapse Studio du till **datahubben.**
1. Gå till **SQLPOOL1-tabeller.**  >   
3. Högerklicka på **dbo. NYCTripTripSmall-tabell** och välj **Nytt SQL-skript**  >  **Välj DE 100 översta raderna.**
4. Vänta medan ett nytt SQL-skript skapas och körs.
5. Observera att längst upp i SQL-skriptet **Anslut till** anges automatiskt till SQL-poolen med namnet **SQLPOOL1.**
6. Ersätt texten i SQL-skriptet med den här koden och kör den.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Den här frågan visar hur det totala reseavståndet och det genomsnittliga reseavståndet relaterar till antalet passagerare.
1. I resultatfönstret för SQL-skript ändrar du **Visa** till **Diagram** för att se en visualisering av resultatet som ett linjediagram.
    
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data i ett Azure Storage konto](get-started-analyze-storage.md)
