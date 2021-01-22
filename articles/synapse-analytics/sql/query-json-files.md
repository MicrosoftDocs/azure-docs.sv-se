---
title: Fråga JSON-filer med Server lös SQL-pool
description: I det här avsnittet beskrivs hur du läser JSON-filer med Server lös SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dc07a3aa954a74ba594eb99da1ea3ee59610c9b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678329"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Fråga JSON-filer med Server lös SQL-pool i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med en server lös SQL-pool i Azure Synapse Analytics. Frågans mål är att läsa JSON-filer med hjälp av [OpenRowSet](develop-openrowset.md). 
- Standard-JSON-filer där flera JSON-dokument lagras som en JSON-matris.
- Tabbavgränsade JSON-filer, där JSON-dokument skiljs åt med rad matnings tecknen. Vanliga tillägg för dessa typer av filer är `jsonl` , `ldjson` och `ndjson` .

## <a name="read-json-documents"></a>Läsa JSON-dokument

Det enklaste sättet att se innehållet i JSON-filen är att ge filen URL till `OPENROWSET` funktionen, ange CSV `FORMAT` och ange värden `0x0b` för `fieldterminator` och `fieldquote` . Om du behöver läsa tabbavgränsade JSON-filer räcker detta. Om du har en klassisk JSON-fil måste du ange värden `0x0b` för `rowterminator` . `OPENROWSET` funktionen kommer att parsa JSON och returnera varje dokument i följande format:

| inköpsdok |
| --- |
|{"date_rep": "2020-07-24", "Day": 24, "månad": 7, "Year": 2020, "Cases": 3, "dödsfall": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "dag": 25, "månad": 7, "år": 2020, "Cases": 7, "dödsfall": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "dag": 26, "månad": 7, "år": 2020, "Cases": 4, "dödsfall": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "Day": 27, "månad": 7, "Year": 2020, "Cases": 8, "dödsfall": 0, "geo_id": "AF"}|

Om filen är offentligt tillgänglig eller om din Azure AD-identitet har åtkomst till den här filen bör du se innehållet i filen med hjälp av frågan som visas i följande exempel.

### <a name="read-json-files"></a>Läsa JSON-filer

Följande exempel fråga läser JSON-och Line-delimited JSON-filer och returnerar varje dokument som en separat rad.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

JSON-dokumentet i föregående exempel fråga innehåller en matris med objekt. Frågan returnerar varje objekt som en separat rad i resultat uppsättningen. Se till att du har åtkomst till den här filen. Om filen skyddas med SAS-nyckel eller anpassad identitet måste du konfigurera [autentiseringsuppgifter på server nivå för SQL-inloggning](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Data källans användning

I föregående exempel används den fullständiga sökvägen till filen. Alternativt kan du skapa en extern data källa med den plats som pekar på lagrings platsens rotmapp och använda denna data källa och den relativa sökvägen till filen i `OPENROWSET` funktionen:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Om en data källa är skyddad med SAS-nyckel eller anpassad identitet kan du konfigurera [data källan med databasens begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

I följande avsnitt kan du se hur du frågar olika typer av JSON-filer.

## <a name="parse-json-documents"></a>Parsa JSON-dokument

Frågorna i föregående exempel returnerar varje JSON-dokument som en enskild sträng i en separat rad i resultat uppsättningen. Du kan använda Functions `JSON_VALUE` och `OPENJSON` för att parsa värdena i JSON-dokument och returnera dem som Relations värden, som de visas i följande exempel:

| datum \_ rep | fall | Geo \_ -ID |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Exempel-JSON-dokument

Fråge exemplen läser *JSON* -filer som innehåller dokument med följande struktur:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Om dessa dokument lagras som Line-delimited JSON måste du ange `FIELDTERMINATOR` och `FIELDQUOTE` 0x0B. Om du har ett standard-JSON-format måste du ange `ROWTERMINATOR` till 0x0B.

### <a name="query-json-files-using-json_value"></a>Fråga JSON-filer med JSON_VALUE

Frågan nedan visar hur du använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att hämta skalära värden (title, Publisher) från ett JSON-dokument:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Fråga JSON-filer med openjson

Följande fråga använder [openjson](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Den hämtar COVID statistik som rapporteras i Serbien:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Nästa steg

Nästa artiklar i den här serien visar hur du:

- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
- [Skapa och använda vyer](create-use-views.md)
