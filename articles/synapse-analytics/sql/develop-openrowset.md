---
title: Så här använder du OPENROWSET i en serverlös SQL-pool
description: Den här artikeln beskriver syntaxen för OPENROWSET i en serverlös SQL-pool och förklarar hur du använder argument.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 28c54865ab9c2876d998896f5f536a11088962f8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566434"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Använda OPENROWSET med hjälp av serverlös SQL-pool i Azure Synapse Analytics

Med `OPENROWSET(BULK...)` funktionen kan du komma åt filer i Azure Storage. `OPENROWSET` funktionen läser innehållet i en fjärrdatakälla (till exempel en fil) och returnerar innehållet som en uppsättning rader. I den serverlösa SQL-poolresursen nås OPENROWSET-massraderingsprovidern genom att anropa OPENROWSET-funktionen och ange alternativet BULK.  

Funktionen `OPENROWSET` kan refereras till i -satsen `FROM` i en fråga som om det vore ett tabellnamn `OPENROWSET` . Den stöder massåtgärder via en inbyggd BULK-provider som gör att data från en fil kan läsas och returneras som en raduppsättning.

## <a name="data-source"></a>Datakälla

Funktionen OPENROWSET i Synapse SQL läser innehållet i filen/filarna från en datakälla. Datakällan är ett Azure Storage-konto som du uttryckligen refererar till i funktionen eller kan dynamiskt härledas från URL:en för de filer `OPENROWSET` som du vill läsa.
Funktionen `OPENROWSET` kan även innehålla en parameter för att ange `DATA_SOURCE` datakällan som innehåller filer.
- `OPENROWSET` utan `DATA_SOURCE` kan användas för att direkt läsa innehållet i filerna från den URL-plats som anges som `BULK` alternativ:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Det här är ett snabbt och enkelt sätt att läsa innehållet i filerna utan förkonfiguration. Med det här alternativet kan du använda det grundläggande autentiseringsalternativet för att få åtkomst till lagringen (Azure AD-genomströmning för Azure AD-inloggningar och SAS-token för SQL-inloggningar). 

- `OPENROWSET` med `DATA_SOURCE` kan användas för att komma åt filer på det angivna lagringskontot:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Med det här alternativet kan du konfigurera lagringskontots plats i datakällan och ange den autentiseringsmetod som ska användas för åtkomst till lagringen. 
    
    > [!IMPORTANT]
    > `OPENROWSET` utan `DATA_SOURCE` ger ett snabbt och enkelt sätt att komma åt lagringsfilerna, men erbjuder begränsade autentiseringsalternativ. Till exempel kan Azure AD-huvudnamn endast komma åt filer med hjälp av deras [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity) eller offentligt tillgängliga filer. Om du behöver kraftfullare autentiseringsalternativ använder du `DATA_SOURCE` alternativet och definierar de autentiseringsuppgifter som du vill använda för att få åtkomst till lagringen.


## <a name="security"></a>Säkerhet

En databasanvändare måste ha `ADMINISTER BULK OPERATIONS` behörighet att använda `OPENROWSET` funktionen.

Lagringsadministratören måste också ge en användare åtkomst till filerna genom att ange en giltig SAS-token eller aktivera Azure AD-huvudkontot för åtkomst till lagringsfiler. Läs mer om åtkomstkontroll för lagring i den [här artikeln.](develop-storage-files-storage-access-control.md)

`OPENROWSET` använd följande regler för att fastställa hur du autentiserar till lagring:
- I `OPENROWSET` utan `DATA_SOURCE` autentiseringsmekanism beror på anropartypen.
  - Alla användare kan använda `OPENROWSET` utan att läsa offentligt tillgängliga filer i Azure `DATA_SOURCE` Storage.
  - Azure AD-inloggningar kan komma åt skyddade filer med sin egen [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) om Azure Storage tillåter att Azure AD-användaren får åtkomst till underliggande filer (till exempel om anroparen har behörighet `Storage Reader` i Azure Storage).
  - SQL-inloggningar kan också användas utan åtkomst till offentligt tillgängliga filer, filer som skyddas med hjälp av SAS-token eller hanterad `OPENROWSET` `DATA_SOURCE` identitet för Synapse-arbetsytan. Du måste skapa [serveromfångsbaserade autentiseringsuppgifter för att](develop-storage-files-storage-access-control.md#examples) tillåta åtkomst till lagringsfiler. 
- I `OPENROWSET` med `DATA_SOURCE` autentiseringsmekanism definieras i databasomfångsautentiseringsuppgifter som tilldelats till den refererade datakällan. Med det här alternativet kan du komma åt offentligt tillgänglig lagring eller få åtkomst till lagring med hjälp av SAS-token, hanterad identitet för arbetsyta eller Azure AD-identitet för [anroparen](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (om anroparen är Azure AD-huvudnamn). Om refererar till Azure Storage som inte är offentlig måste du skapa databasomfångsbaserade autentiseringsuppgifter och referera till den i för att tillåta `DATA_SOURCE` åtkomst till [](develop-storage-files-storage-access-control.md#examples) `DATA SOURCE` lagringsfiler.

Anroparen måste ha `REFERENCES` behörighet för autentiseringsuppgifter för att använda den för att autentisera till lagring.

## <a name="syntax"></a>Syntax

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
```

## <a name="arguments"></a>Argument

Du har två alternativ för indatafiler som innehåller måldata för frågor. Giltiga värden är:

- "CSV" – Innehåller alla avgränsade textfiler med rad-/kolumnavgränsare. Alla tecken kan användas som fältavgränsare, till exempel TSV: FIELDTERMINATOR = tab.

- "PARQUET" – binärfil i Parquet-format 

**"unstructured_data_path"**

Den unstructured_data_path som upprättar en sökväg till data kan vara en absolut eller relativ sökväg:
- Absoluta sökvägar i formatet \<prefix> " :// " gör det möjligt för en användare att läsa filerna \<storage_account_path> / \<storage_path> direkt.
- Relativ sökväg i formatet "<storage_path>" som måste användas med parametern och beskriver filmönstret i det <storage_account_path> `DATA_SOURCE` som definieras i `EXTERNAL DATA SOURCE` . 

Nedan hittar du relevanta värden <storage account path> som länkar till din specifika externa datakälla. 

| Extern datakälla       | Prefix | Sökväg till lagringskonto                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Anger en sökväg i lagringen som pekar på den mapp eller fil som du vill läsa. Om sökvägen pekar på en container eller mapp läses alla filer från den specifika containern eller mappen. Filer i undermappar tas inte med. 

Du kan använda jokertecken för att rikta flera filer eller mappar. Användning av flera icke-secutiva jokertecken tillåts.
Nedan visas ett exempel som läser alla *csv-filer som* börjar med *populationen* från alla mappar som börjar med */csv/population:*  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Om du anger unstructured_data_path som en mapp hämtar en serverlös SQL-poolfråga filer från den mappen. 

Du kan instruera en serverlös SQL-pool att bläddra bland mappar genom att ange /* i slutet av sökvägen som i följande exempel: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar inte serverlös SQL-pool undermappar om du inte anger /** i slutet av sökvägen. Precis som Hadoop och PolyBase returnerar den inte filer som filnamnet börjar med en understrykning (_) eller en punkt (.).

I exemplet nedan, om unstructured_data_path= `https://mystorageaccount.dfs.core.windows.net/webdata/` returnerar en serverlös SQL-poolfråga rader från mydata.txt. Den returnerar inte mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Med WITH-satsen kan du ange kolumner som du vill läsa från filer.

- Om du vill läsa alla kolumner för CSV-datafiler anger du kolumnnamn och deras datatyper. Om du vill ha en delmängd kolumner använder du ordningstal för att välja kolumnerna från de ursprungliga datafilerna efter ordningstal. Kolumner binds av ordningstalsbeteckningen. Om HEADER_ROW = TRUE används görs kolumnbindningen efter kolumnnamn i stället för ordningstal.
    > [!TIP]
    > Du kan utelämna WITH-satsen för CSV-filer också. Datatyper härförs automatiskt från filinnehåll. Du kan använda HEADER_ROW argument för att ange förekomsten av rubrikrad, där kolumnnamnen läses från rubrikraden. Mer information finns i [automatisk schemaidentifiering.](#automatic-schema-discovery)
    
- För Parquet-datafiler anger du kolumnnamn som matchar kolumnnamnen i de ursprungliga datafilerna. Kolumnerna binds efter namn och är fallkänsliga. Om WITH-satsen utelämnas returneras alla kolumner från Parquet-filer.
    > [!IMPORTANT]
    > Kolumnnamn i Parquet-filer är fallkänsliga. Om du anger kolumnnamn med ett annat hölje än kolumnnamnet i Parquet-filen returneras NULL-värden för den kolumnen.


column_name = Namn på utdatakolumnen. Om det anges åsidosätter det här namnet kolumnnamnet i källfilen och kolumnnamnet som anges i JSON-sökvägen om det finns ett. Om json_path inte anges läggs den automatiskt till som $.column_name. Kontrollera json_path argument för beteende.

column_type = Datatyp för utdatakolumnen. Den implicita datatypskonverteringen sker här.

column_ordinal = Ordningstal för kolumnen i källfilerna. Det här argumentet ignoreras för Parquet-filer eftersom bindningen görs efter namn. I följande exempel returneras endast en andra kolumn från en CSV-fil:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [JSON-sökvägsuttryck](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) till kolumn eller kapslad egenskap. [Standardsökvägsläget](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) är sn.

> [!NOTE]
> I strikt läge misslyckas frågan med fel om den angivna sökvägen inte finns. I mode lyckas frågan och JSON-sökvägsuttrycket utvärderas till NULL.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Anger fältparenteraren som ska användas. Standardfältavseparatorn är ett kommatecken ("**,**").

ROWTERMINATOR ='row_terminator''

Anger radparenteraren som ska användas. Om radterminatorn inte anges används en av standardparenterna. Standardterminatorer för PARSER_VERSION = "1.0" är \r\n, \n och \r. Standardterminatorer för PARSER_VERSION = "2.0" är \r\n och \n.

ESCAPE_CHAR = 'char'

Anger tecknet i filen som används för att undvika sig själv och alla avgränsarvärden i filen. Om escape-tecknet följs av ett annat värde än sig självt, eller något av avgränsarvärdena, tas escape-tecknet bort när värdet läss. 

Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktiverat. Det används inte för att undvika citattecknet. Citattecknet måste vara rymt med ett annat citattecken. Citattecken kan bara visas i kolumnvärdet om värdet är inkapslat med citattecken.

FIRSTROW = "first_row" 

Anger antalet för den första raden som ska läsas in. Standardvärdet är 1 och anger den första raden i den angivna datafilen. Radnumren bestäms genom att räkna radterminatorerna. FIRSTROW är 1-baserad.

FIELDQUOTE = "field_quote" 

Anger ett tecken som ska användas som citattecken i CSV-filen. Om inget anges används citattecknet ("). 

DATA_COMPRESSION = "data_compression_method"

Anger komprimeringsmetod. Stöds endast PARSER_VERSION='1.0'. Följande komprimeringsmetod stöds:

- Gzip

PARSER_VERSION = "parser_version"

Anger parserversion som ska användas vid läsning av filer. CSV-parserversioner som stöds för närvarande är 1.0 och 2.0:

- PARSER_VERSION = 1,0
- PARSER_VERSION = 2.0

CSV-parser version 1.0 är standard och funktionsrik. Version 2.0 har skapats för prestanda och stöder inte alla alternativ och kodningar. 

Information om CSV-parser version 1.0:

- Följande alternativ stöds inte: HEADER_ROW.

Information om CSV-parser version 2.0:

- Alla datatyper stöds inte.
- Den maximala teckenkolumnens längd är 8 000.
- Den maximala gränsen för radstorlek är 8 MB.
- Följande alternativ stöds inte: DATA_COMPRESSION.
- Den tomma strängen ("") tolkas som en tom sträng.
- Format som stöds för DATAtypen DATUM: YYYY-MM-DD
- Format som stöds för TIME-datatyp: HH:MM:SS[.fractional seconds]
- Format som stöds för DATETIME2-datatypen: YYYY-MM-DD HH:MM:SS[.fractional seconds]

HEADER_ROW = { TRUE | FALSE }

Anger om CSV-filen innehåller rubrikraden. Standardvärdet är FALSE. Stöds i PARSER_VERSION='2.0'. Om det är TRUE läses kolumnnamnen från den första raden enligt FIRSTROW-argumentet. Om TRUE och schema anges med WITH görs bindningen av kolumnnamn efter kolumnnamn, inte ordningstalspositioner.

DATAFILETYPE = { 'char' | "widechar" }

Anger kodning: char används för UTF8, widechar används för UTF16-filer.

CODEPAGE = { 'ACP' | OEM-| RAW-| "code_page" }

Anger teckensidan för data i datafilen. Standardvärdet är 65001 (UTF-8-kodning). Mer information om det här alternativet [finns här.](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)

## <a name="fast-delimited-text-parsing"></a>Snabb parsning av avgränsad text

Det finns två avgränsade textparserversioner som du kan använda. CSV-parser version 1.0 är standard och funktionsrik medan parser version 2.0 har skapats för prestanda. Prestandaförbättringar i parser 2.0 kommer från avancerade parsningstekniker och flertrådsteknik. Skillnaden i hastighet blir större när filstorleken växer.

## <a name="automatic-schema-discovery"></a>Automatisk schemaidentifiering

Du kan enkelt köra frågor mot både CSV- och Parquet-filer utan att känna till eller ange schema genom att utelämna WITH-satsen. Kolumnnamn och datatyper härförs från filer.

Parquet-filer innehåller kolumnmetadata som ska läsas. Typmappningar finns i [typmappningar för Parquet](#type-mapping-for-parquet). Läs [Parquet-filer utan att ange schema](#read-parquet-files-without-specifying-schema) för exempel.

Kolumnnamn kan läsas från rubrikraden för CSV-filer. Du kan ange om rubrikraden finns med HEADER_ROW argument. Om HEADER_ROW = FALSE används generiska kolumnnamn: C1, C2, ... Cn där n är antalet kolumner i filen. Datatyper härförs från de första 100 dataraderna. Läs [CSV-filer utan att ange schema](#read-csv-files-without-specifying-schema) för exempel.

> [!IMPORTANT]
> Det finns fall där lämplig datatyp inte kan härledas på grund av brist på information och större datatyp används i stället. Detta medför prestandakostnader och är särskilt viktigt för teckenkolumner som härlednings som varchar(8000). För optimala prestanda kontrollerar du [härdade datatyper](best-practices-sql-on-demand.md#check-inferred-data-types) och [använder lämpliga datatyper.](best-practices-sql-on-demand.md#use-appropriate-data-types)

### <a name="type-mapping-for-parquet"></a>Typmappning för Parquet

Parquet-filer innehåller typbeskrivningar för varje kolumn. I följande tabell beskrivs hur Parquet-typer mappas till interna SQL-typer.

| Parquet-typ | Logisk parquet-typ (anteckning) | SQL-datatyp |
| --- | --- | --- |
| Boolean | | bit |
| BINÄR/BYTE_ARRAY | | varbinary |
| DOUBLE | | flyt |
| Flyta | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \* (UTF8-sortering) |
| BINARY |Sträng |varchar \* (UTF8-sortering) |
| BINARY |Enum|varchar \* (UTF8-sortering) |
| FIXED_LEN_BYTE_ARRAY |Uuid |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(8000) \* (UTF8-sortering) |
| BINARY |BSON | Stöds inte |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |Intervall | Stöds inte |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLAS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TID (MIKRO) |time – TIME(NANOS) stöds inte |
|INT64 |TIDSSTÄMPEL (MAN/MIKRO) |datetime2 – TIMESTAMP(NANOS) stöds inte |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Lista |varchar(8000), serialiserat till JSON |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Karta|varchar(8000), serialiserat till JSON |

## <a name="examples"></a>Exempel

### <a name="read-csv-files-without-specifying-schema"></a>Läsa CSV-filer utan att ange schema

I följande exempel läses CSV-filen som innehåller rubrikraden utan att kolumnnamn och datatyper anges: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

I följande exempel läses CSV-filen som inte innehåller rubrikrad utan att kolumnnamn och datatyper anges: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Läsa Parquet-filer utan att ange schema

I följande exempel returneras alla kolumner på den första raden från censusdatauppsättningen, i Parquet-format och utan att ange kolumnnamn och datatyper: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Läsa specifika kolumner från CSV-fil

I följande exempel returneras bara två kolumner med ordningstalen 1 och 4 från populationen*.csv-filer. Eftersom det inte finns någon rubrikrad i filerna börjar den läsa från den första raden:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Läsa specifika kolumner från Parquet-fil

I följande exempel returneras endast två kolumner av den första raden från censusdatauppsättningen i Parquet-format: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Ange kolumner med JSON-sökvägar

I följande exempel visas hur du kan använda [JSON-sökvägsuttryck](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) i WITH-satsen och demonstrera skillnaden mellan strikta sökvägslägen och lägen för sökvägar: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [snabbstarten](query-data-storage.md) för lagring av frågedata för att lära dig hur du använder för att läsa `OPENROWSET` [filformaten CSV,](query-single-csv-file.md) [PARQUET](query-parquet-files.md)och [JSON.](query-json-files.md) Kontrollera [metodtipsen](best-practices-sql-on-demand.md) för att uppnå optimala prestanda. Du kan också lära dig hur du sparar resultatet av din fråga för att Azure Storage hjälp [av CETAS](develop-tables-cetas.md).