---
title: Fråga Parquet-filer med Server lös SQL-pool
description: I den här artikeln får du lära dig hur du frågar Parquet-filer med en server lös SQL-pool.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: cce4c6aff986c2e8c3d879d962714e13f6b2e7ae
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694692"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Fråga Parquet-filer med Server lös SQL-pool i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med en server lös SQL-pool som ska läsa Parquet-filer.

## <a name="quickstart-example"></a>Exempel på snabb start

`OPENROWSET` funktionen gör att du kan läsa innehållet i Parquet-filen genom att ange URL: en till filen.

### <a name="read-parquet-file"></a>Läs Parquet-fil

Det enklaste sättet att se innehållet i `PARQUET` filen är att ange fil-URL för att kunna `OPENROWSET` fungera och ange Parquet `FORMAT` . Om filen är offentligt tillgänglig eller om din Azure AD-identitet har åtkomst till den här filen bör du kunna se innehållet i filen med frågan som visas i följande exempel:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Se till att du har åtkomst till den här filen. Om filen skyddas med SAS-nyckel eller anpassad Azure-identitet måste du konfigurera [autentiseringsuppgifter på server nivå för SQL-inloggning](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Se till att du använder en databas sortering med UTF-8 (till exempel `Latin1_General_100_BIN2_UTF8` ) eftersom sträng värden i PARQUET-filer är kodade med UTF-8-kodning.
> Ett matchnings fel mellan text kodningen i PARQUET-filen och sorteringen kan orsaka oväntade konverterings fel.
> Du kan enkelt ändra standard sorteringen för den aktuella databasen med hjälp av följande T-SQL-uttryck: `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Data källans användning

I föregående exempel används den fullständiga sökvägen till filen. Alternativt kan du skapa en extern data källa med den plats som pekar på lagrings platsens rotmapp och använda denna data källa och den relativa sökvägen till filen i `OPENROWSET` funktionen:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Om en data källa är skyddad med SAS-nyckel eller anpassad identitet kan du konfigurera [data källan med databasens begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Ange ett schema explicit

`OPENROWSET` med kan du uttryckligen ange vilka kolumner som du vill läsa från filen med hjälp av `WITH` satsen:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Se till att du explicilty anger vissa UTF-8-sortering (till exempel `Latin1_General_100_BIN2_UTF8` ) för alla String-kolumner i- `WITH` satsen eller ange vissa UTF-8-sortering på databas nivå.
> Matchnings fel mellan text kodning i kolumn sortering av fil och sträng kan orsaka oväntade konverterings fel.
> Du kan enkelt ändra standard sorteringen för den aktuella databasen med hjälp av följande T-SQL-uttryck: `alter database current collate Latin1_General_100_BIN2_UTF8`
> Du kan enkelt ställa in sorteringen på pris-typerna med följande definition: `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

I följande avsnitt kan du se hur du frågar olika typer av PARQUET-filer.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att **skapa en databas** med en data källa som refererar till [NYC gul taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -lagrings konto. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="dataset"></a>Datamängd

[NYC gul taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -datauppsättning används i det här exemplet. Du kan fråga Parquet-filer på samma sätt som du [läser CSV-filer](query-parquet-files.md). Den enda skillnaden är att `FILEFORMAT` parametern ska vara inställd på `PARQUET` . Exemplen i den här artikeln visar information om hur du läser Parquet-filer.

## <a name="query-set-of-parquet-files"></a>Fråga uppsättning Parquet-filer

Du kan bara ange intressanta kolumner när du frågar Parquet-filer.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatisk schema härledning

Du behöver inte använda OpenRowSet WITH-satsen vid läsning av Parquet-filer. Kolumn namn och data typer läses automatiskt från Parquet-filer.

Exemplet nedan visar de automatiska schema härlednings funktionerna för Parquet-filer. Det returnerar antalet rader i september 2017 utan att ange ett schema.

> [!NOTE]
> Du behöver inte ange kolumner i OpenRowSet WITH-satsen när du läser Parquet-filer. I så fall använder Server lös SQL-poolens fråge tjänst metadata i Parquet-filen och bind kolumner efter namn.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Fråga partitionerade data

Den data mängd som anges i det här exemplet är uppdelad (partitionerad) i separata undermappar. Du kan fokusera på specifika partitioner med funktionen filsökväg. I det här exemplet visas pris belopp per år, månad och payment_type under de första tre månaderna av 2017.

> [!NOTE]
> Frågan om SQL-poolen utan server är kompatibel med Hive/Hadoop-partitionerings schema.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Typ mappning

För Parquet typ mappning till SQL-typ [Mappning av typ mappning för Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig att [fråga Parquet-kapslade typer](query-parquet-nested-types.md).
