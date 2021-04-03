---
title: Fråga mappar och flera filer med en server lös SQL-pool
description: SQL-poolen utan Server stöder läsning av flera filer/mappar med jokertecken, som liknar de jokertecken som används i Windows OS.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462623"
---
# <a name="query-folders-and-multiple-files"></a>Efterfråga mappar och flera filer  

I den här artikeln får du lära dig hur du skriver en fråga med en server lös SQL-pool i Azure Synapse Analytics.

SQL-poolen utan Server stöder läsning av flera filer/mappar med jokertecken, som liknar de jokertecken som används i Windows OS. Det finns dock större flexibilitet eftersom flera jokertecken är tillåtna.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att **skapa en databas** där du ska köra frågorna. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

Du använder mappen *CSV/taxi* för att följa exempel frågorna. Den innehåller NYC taxi-Yellow taxi-resa registrerar data från 2016 juli till 2018 juni. Filerna i *CSV/taxi* har namnet efter år och månad med följande mönster: yellow_tripdata_ <year> - <month> . csv

## <a name="read-all-files-in-folder"></a>Läsa alla filer i mappen

Exemplet nedan läser alla NYC gula taxi-datafiler från *CSV/taxi-* mappen och returnerar det totala antalet passagerare och sina val per år. Den visar också användningen av mängd funktioner.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

### <a name="read-subset-of-files-in-folder"></a>Läs delmängd av filer i mappen

Exemplet nedan läser de 2017 NYC gula taxi-datafilerna från *CSV/taxi-* mappen med ett jokertecken och returnerar det totala avgifts beloppet per betalnings typ.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

## <a name="read-folders"></a>Läsa mappar

Den sökväg som du anger till OpenRowSet kan också vara en sökväg till en mapp. Följande avsnitt innehåller dessa frågetyper.

### <a name="read-all-files-from-specific-folder"></a>Läs alla filer från en speciell mapp

Du kan läsa alla filer i en mapp med hjälp av jokertecken på filnivå som visas i [mappen Läs alla filer i mappen](#read-all-files-in-folder). Men det finns ett sätt att fråga en mapp och använda alla filer i mappen.

Om sökvägen i OpenRowSet pekar på en mapp, kommer alla filer i mappen att användas som källa för frågan. Följande fråga läser alla filer i mappen *CSV/taxi* .

> [!NOTE]
> Observera att det finns en/i slutet av sökvägen i frågan nedan. Den anger en mapp. Om/utelämnas, kommer frågan att rikta en fil med namnet *taxi* i stället.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

### <a name="read-all-files-from-multiple-folders"></a>Läs alla filer från flera mappar

Det går att läsa filer från flera mappar med hjälp av ett jokertecken. Följande fråga läser alla filer från alla mappar som finns i *CSV* -mappen med namn som börjar med *t* och slutar med *i*.

> [!NOTE]
> Observera att det finns en/i slutet av sökvägen i frågan nedan. Den anger en mapp. Om/utelämnas, kommer frågan att skicka filer med namnet *t &ast; i* stället.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

Eftersom du bara har en mapp som matchar villkoren är frågeresultatet detsamma som [i mappen läsa alla filer i mappen](#read-all-files-in-folder).

## <a name="traverse-folders-recursively"></a>Bläddra mappar rekursivt

SQL-poolen utan server kan förflytta sig rekursivt i mappar om du anger/* * i slutet av sökvägen. Följande fråga läser alla filer från alla mappar och undermappar som finns i *CSV* -mappen.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

## <a name="multiple-wildcards"></a>Flera jokertecken

Du kan använda flera jokertecken på olika Sök vägs nivåer. Du kan till exempel utöka tidigare fråga till att endast läsa filer med 2017-data från alla mappar som namnen börjar med *t* och slutar med *i*.

> [!NOTE]
> Observera att det finns en/i slutet av sökvägen i frågan nedan. Den anger en mapp. Om/utelämnas, kommer frågan att skicka filer med namnet *t &ast; i* stället.
> Det finns en övre gräns på 10 jokertecken per fråga.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alla filer som används med den enda OpenRowSet-rad uppsättningen måste ha samma struktur (d.v.s. antalet kolumner och deras data typer).

Eftersom du bara har en mapp som matchar villkoren, är frågeresultatet detsamma som att [läsa delmängd av filer i mappen](#read-subset-of-files-in-folder) och [läsa alla filer från en viss mapp](#read-all-files-from-specific-folder). Mer komplexa scenarier för användning av jokertecken beskrivs i [fråga Parquet-filer](query-parquet-files.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i artikeln [query Specific Files](query-specific-files.md) .
