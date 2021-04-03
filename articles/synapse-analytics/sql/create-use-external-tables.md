---
title: Skapa och Använd externa tabeller i SQL-poolen utan Server
description: I det här avsnittet får du lära dig hur du skapar och använder externa tabeller i SQL-poolen utan server.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462309"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Skapa och Använd externa tabeller med Server lös SQL-pool i Azure Synapse Analytics

I det här avsnittet får du lära dig hur du skapar och använder [externa tabeller](develop-tables-external-tables.md) i SQL-poolen utan server. Externa tabeller är användbara när du vill kontrol lera åtkomsten till externa data i SQL-poolen utan server och om du vill använda verktyg som Power BI, tillsammans med SQL-poolen utan server. Externa tabeller har åtkomst till två typer av lagring:
- Offentlig lagring där användare kommer åt offentliga lagringsfiler.
- Skyddad lagring där användare får åtkomst till lagrings filer med SAS-autentiseringsuppgifter, Azure AD-identitet eller hanterad identitet för Synapse-arbetsytan.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att skapa en databas där tabellerna ska skapas. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Detta installations skript kommer att skapa följande objekt som används i det här exemplet:
- DATABASens begränsade AUTENTISERINGSUPPGIFTER `sqlondemand` som ger åtkomst till SAS-skyddade `https://sqlondemandstorage.blob.core.windows.net` Azure Storage-konton.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERN DATA källa `sqlondemanddemo` som refererar till demonstrations lagrings kontot som skyddas med SAS-nyckel och extern data källa `YellowTaxi` som hänvisar till offentligt tillgängligt Azure Storage-konto på platsen `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Fil format `QuotedCSVWithHeaderFormat` och `ParquetFormat` som beskriver fil typerna CSV och Parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Frågorna i den här artikeln utförs i exempel databasen och använder dessa objekt. 

## <a name="create-an-external-table-on-protected-data"></a>Skapa en extern tabell på skyddade data

Du kan skapa externa tabeller som har åtkomst till data på ett Azure Storage-konto som ger åtkomst till användare med en viss Azure AD-identitet eller SAS-nyckel. Du kan skapa externa tabeller på samma sätt som du skapar vanliga SQL Server externa tabeller. 

Följande fråga skapar en extern tabell som läser *population.csv* -filen från SynapseSQL demo Azure Storage-konto som refereras till med `sqlondemanddemo` data källan och skyddas med databasens begränsade autentiseringsuppgifter anropad `sqlondemand` . 

Autentiseringsuppgifter för data källa och databas omfattning skapas i [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Skapa en extern tabell för offentliga data

Du kan skapa externa tabeller som läser data från filerna som placeras på offentligt tillgängliga Azure-lagring. Det här [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) skapar offentlig extern data källa och Parquet fil formats definition som används i följande fråga:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Använd en extern tabell

Du kan använda [externa tabeller](develop-tables-external-tables.md) i dina frågor på samma sätt som du använder dem i SQL Server frågor.

Följande fråga visar detta med den externa *populations* tabellen som vi skapade i föregående avsnitt. Namnet på land/region returneras med deras population i 2019 i fallande ordning.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Nästa steg

Information om hur du lagrar resultat från en fråga till lagringen hittar du i [Sök resultaten för butiken till lagrings](../sql/create-external-table-as-select.md) artikeln.
