---
title: Lagra frågeresultaten från en server lös SQL-pool
description: I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med hjälp av SQL-poolen utan server.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 12841c747116cc9e14f348dfcf81acaa5da5e8c9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165373"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Lagra frågeresultat till lagring med hjälp av Server lös SQL-pool i Azure Synapse Analytics

I den här artikeln får du lära dig hur du lagrar frågeresultat till lagring med hjälp av SQL-poolen utan server.

## <a name="prerequisites"></a>Krav

Ditt första steg är att **skapa en databas** där du ska köra frågorna. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används för att läsa data i dessa exempel.

Följ anvisningarna i den här artikeln för att skapa data källor, autentiseringsuppgifter för databaser och externa fil format som används för att skriva data till utmatnings lagringen.

## <a name="create-external-table-as-select"></a>Skapa extern tabell som Välj

Du kan använda instruktionen skapa extern tabell som SELECT (CETAS) för att lagra frågeresultatet till Storage.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> Du måste ändra det här skriptet och ändra mål platsen för att köra det igen. Det går inte att skapa externa tabeller på den plats där du redan har data.

## <a name="use-the-external-table"></a>Använd den externa tabellen

Du kan använda den externa tabellen som skapats via CETAS som en vanlig extern tabell.

> [!NOTE]
> Ändra den första raden i frågan, t. ex. [mydbname], så att du använder den databas som du har skapat.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="remarks"></a>Kommentarer

När du har lagrat resultaten går det inte att ändra data i den externa tabellen. Du kan inte upprepa det här skriptet eftersom CETAS inte skriver över underliggande data som skapats i föregående körning. Rösta på följande feedback-objekt om några av dessa krävs i dina scenarier, eller föreslå nya på Azure feedback-webbplatsen:
- [Aktivera infogning av nya data i extern tabell](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/32981347-polybase-allow-insert-new-data-to-existing-exteran)
- [Aktivera borttagning av data från extern tabell](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/15158034-polybase-delete-from-external-tables)
- [Ange partitioner i CETAS](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/19520860-polybase-partitioned-by-functionality-when-creati)
- [Ange fil storlek och antal](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/42263617-cetas-specify-number-of-parquet-files-file-size)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du frågar olika filtyper finns i artikeln [fråga en enkel CSV-fil](query-single-csv-file.md), [fråga Parquet-filer](query-parquet-files.md)och [fråga JSON-filer](query-json-files.md) .
