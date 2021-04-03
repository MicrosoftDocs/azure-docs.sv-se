---
title: Fråga CSV-filer med Server lös SQL-pool
description: I den här artikeln får du lära dig att fråga enkla CSV-filer med olika fil format med hjälp av SQL-poolen utan server.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462599"
---
# <a name="query-csv-files"></a>Köra frågor mot CSV-filer

I den här artikeln får du lära dig hur du frågar en enkel CSV-fil med en server lös SQL-pool i Azure Synapse Analytics. CSV-filer kan ha olika format: 

- Med och utan en rubrik rad
- Kommatecken och Tabbavgränsade värden
- Windows-och UNIX-format rads slut
- Värden som inte är citerade och citerade och som överlappar tecken

Alla ovanstående variationer kommer att täckas av nedan.

## <a name="quickstart-example"></a>Exempel på snabb start

`OPENROWSET` funktionen gör att du kan läsa innehållet i CSV-filen genom att ange URL: en till filen.

### <a name="read-a-csv-file"></a>Läs en CSV-fil

Det enklaste sättet att se innehållet i `CSV` filen är att ange fil-URL: en för `OPENROWSET` funktionen, ange csv och `FORMAT` 2,0 `PARSER_VERSION` . Om filen är offentligt tillgänglig eller om din Azure AD-identitet har åtkomst till den här filen bör du kunna se innehållet i filen med frågan som visas i följande exempel:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Alternativet `firstrow` används för att hoppa över den första raden i CSV-filen som representerar rubriken i det här fallet. Se till att du har åtkomst till den här filen. Om filen skyddas med SAS-nyckel eller anpassad identitet måste du konfigurera [autentiseringsuppgifter på server nivå för SQL-inloggning](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Om CSV-filen innehåller UTF-8-tecken, se till att du använder en databas sortering med UTF-8 (till exempel `Latin1_General_100_CI_AS_SC_UTF8` ).
> Ett matchnings fel mellan text kodning i filen och sorteringen kan orsaka oväntade konverterings fel.
> Du kan enkelt ändra standard sorteringen för den aktuella databasen med hjälp av följande T-SQL-uttryck: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Data källans användning

I föregående exempel används den fullständiga sökvägen till filen. Alternativt kan du skapa en extern data källa med platsen som pekar på rotmappen för lagringen:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

När du har skapat en data källa kan du använda den data källan och den relativa sökvägen till filen i `OPENROWSET` funktionen:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Om en data källa är skyddad med SAS-nyckel eller anpassad identitet kan du konfigurera [data källan med databasens begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Ange ett schema explicit

`OPENROWSET` med kan du uttryckligen ange vilka kolumner som du vill läsa från filen med hjälp av `WITH` satsen:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Siffrorna efter en datatyp i `WITH` satsen representerar kolumn index i CSV-filen.

> [!IMPORTANT]
> Om CSV-filen innehåller UTF-8-tecken ser du till att du explicilty anger vissa UTF-8-sortering (till exempel `Latin1_General_100_CI_AS_SC_UTF8` ) för alla kolumner i- `WITH` satsen eller anger vissa UTF-8-sortering på databas nivå.
> Matchnings fel mellan text kodning i filen och sorteringen kan orsaka oväntade konverterings fel.
> Du kan enkelt ändra standard sorteringen för den aktuella databasen med hjälp av följande T-SQL-uttryck: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Du kan enkelt ställa in sorteringen på pris-typerna med följande definition: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

I följande avsnitt kan du se hur du frågar olika typer av CSV-filer.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att **skapa en databas** där tabellerna ska skapas. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="windows-style-new-line"></a>Ny rad i Windows-format

Följande fråga visar hur du läser en CSV-fil utan en rubrik rad, med en ny rad med Windows-typ och kommaavgränsade kolumner.

Fil för hands version:

![De första 10 raderna i CSV-filen utan sidhuvud, ny rad i Windows-format.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Ny rad med UNIX-format

Följande fråga visar hur du läser en fil utan en rubrik rad, med en ny rad med UNIX-format och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen utan rubrik rad och med Unix-Style ny rad.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Rubrik rad

Följande fråga visar hur du kan läsa en fil med en rubrik rad, med en ny rad med UNIX-typ och kommaavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med Unix-Style ny rad.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Anpassat citat tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och offererade värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med Unix-Style nya rad-och offererade värden.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Den här frågan returnerar samma resultat om du utelämnade parametern FIELDQUOTE eftersom standardvärdet för FIELDQUOTE är ett dubbelt citat tecken.

## <a name="escape-characters"></a>Escape-tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-typ, kommaavgränsade kolumner och ett escape-tecken som används för fält avgränsare (komma) i värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med Unix-Style ny rad och escape-tecken som används för fält avgränsare.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Den här frågan kan inte utföras om ESCAPECHAR inte anges eftersom kommatecknet i "slov, enia" behandlas som fält avgränsare i stället för en del av landets/regionens namn. "Slov, enia" behandlas som två kolumner. Därför skulle den aktuella raden ha en kolumn mer än de andra raderna, och en kolumn mer än du definierade i WITH-satsen.

### <a name="escape-quoting-characters"></a>Undanta citat tecken

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och ett escapeed Double citat tecken inom värden. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-format, kommaavgränsade kolumner och ett escapeed Double citat tecken inom värden.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Citat tecken måste föregås av ett annat citat tecken. Citat tecken får bara förekomma i kolumn värden om värdet är kapslat med citat tecken.

## <a name="tab-delimited-files"></a>Tabbavgränsade filer

Följande fråga visar hur du läser en fil med en rubrik rad, med en ny rad med UNIX-typ och tabbavgränsade kolumner. Observera den olika platsen för filen jämfört med de andra exemplen.

Fil för hands version:

![De första 10 raderna i CSV-filen med rubrik raden och med Unix-Style nya rad-och TABB-avgränsare.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Returnera en delmängd av kolumner

Hittills har du angett CSV-filschemat med med och visar alla kolumner. Du kan bara ange kolumner som du faktiskt behöver i frågan genom att använda ett ordnings tal för varje kolumn som behövs. Du kommer också att utelämna kolumner utan intresse.

Följande fråga returnerar antalet distinkta lands-/region namn i en fil och anger bara de kolumner som behövs:

> [!NOTE]
> Ta en titt på WITH-satsen i frågan nedan och Observera att det finns "2" (utan citat tecken) i slutet av raden där du definierar kolumnen *[country_name]* . Det innebär att kolumnen *[country_name]* är den andra kolumnen i filen. Frågan kommer att ignorera alla kolumner i filen förutom den andra.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Nästa steg

I nästa artiklar visas hur du:

- [Fråga Parquet-filer](query-parquet-files.md)
- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
