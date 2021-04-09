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
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225599"
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

Frågan nedan visar hur du använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) för att hämta skalära värden ( `date_rep` , `countries_and_territories` , `cases` ) från ett JSON-dokument:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
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

När du extraherar JSON-egenskaper från ett JSON-dokument kan du definiera kolumnalias och eventuellt omvandla textvärdet till någon typ.

### <a name="query-json-files-using-openjson"></a>Fråga JSON-filer med openjson

Följande fråga använder [openjson](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true). Den hämtar COVID statistik som rapporteras i Serbien:

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
Resultatet är detsamma som de resultat som returneras med `JSON_VALUE` funktionen. I vissa fall kan du använda `OPENJSON` `JSON_VALUE` :
- I- `WITH` satsen kan du uttryckligen ange kolumnalias och typer för varje egenskap. Du behöver inte ställa in `CAST` funktionen i varje kolumn i `SELECT` listan.
- `OPENJSON` kan vara snabbare om du returnerar ett stort antal egenskaper. Om du returnerar precis 1-2 egenskaper `OPENJSON` kan funktionen bli överordnad.
- Du måste använda `OPENJSON` funktionen om du behöver parsa matrisen från varje dokument och koppla den till den överordnade raden.

## <a name="next-steps"></a>Nästa steg

Nästa artiklar i den här serien visar hur du:

- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
- [Skapa och använda vyer](create-use-views.md)
