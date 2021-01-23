---
title: Använda OpenRowSet i SQL-poolen utan Server
description: Den här artikeln beskriver syntaxen för OpenRowSet i SQL-poolen utan server och förklarar hur du använder argument.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2ead7291f52f33c271768ae2f470af65aca98030
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734755"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Använda OpenRowSet med Server lös SQL-pool i Azure Synapse Analytics

`OPENROWSET(BULK...)`Funktionen gör att du kan komma åt filer i Azure Storage. `OPENROWSET` funktionen läser innehåll i en fjärrdatakälla (till exempel fil) och returnerar innehållet som en uppsättning rader. I den serverbaserade SQL-poolen kan du komma åt OpenRowSet-providern för OpenRowSet genom att anropa funktionen OpenRowSet och ange alternativet för Mass rad uppsättning.  

`OPENROWSET`Funktionen kan refereras i- `FROM` satsen i en fråga som om den vore ett tabell namn `OPENROWSET` . Det stöder Mass åtgärder via en inbyggd Mass leverantör som gör det möjligt att läsa och returnera data från en fil som en rad uppsättning.

## <a name="data-source"></a>Datakälla

OpenRowSet-funktionen i Synapse SQL läser innehållet i filen/filerna från en data källa. Data källan är ett Azure Storage-konto och det kan uttryckligen refereras till i `OPENROWSET` funktionen eller kan dynamiskt härledas från URL: en för de filer som du vill läsa.
`OPENROWSET`Funktionen kan också innehålla en `DATA_SOURCE` parameter för att ange den data källa som innehåller filer.
- `OPENROWSET` utan `DATA_SOURCE` kan användas för att läsa innehållet i filerna direkt från den URL-plats som anges som `BULK` alternativ:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Det här är ett snabbt och enkelt sätt att läsa innehållet i filerna utan för konfigurering. Med det här alternativet kan du använda alternativet grundläggande autentisering för att komma åt lagringen (Azure AD-genomströmning för Azure AD-inloggningar och SAS-token för SQL-inloggningar). 

- `OPENROWSET` med `DATA_SOURCE` kan användas för att komma åt filer på ett angivet lagrings konto:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Med det här alternativet kan du konfigurera lagrings kontots plats i data källan och ange den autentiseringsmetod som ska användas för åtkomst till lagringen. 
    
    > [!IMPORTANT]
    > `OPENROWSET` utan `DATA_SOURCE` ger ett snabbt och enkelt sätt att komma åt lagringsfiler, men erbjuder alternativ för begränsad autentisering. Till exempel kan Azure AD-huvudobjekten bara komma åt filer med sin [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity) eller offentligt tillgängliga filer. Om du behöver mer kraftfulla autentiseringsalternativ använder du `DATA_SOURCE` alternativet och definierar de autentiseringsuppgifter som du vill använda för att komma åt lagringen.


## <a name="security"></a>Säkerhet

En databas användare måste ha `ADMINISTER BULK OPERATIONS` behörighet att använda `OPENROWSET` funktionen.

Lagrings administratören måste också göra det möjligt för en användare att komma åt filerna genom att tillhandahålla en giltig SAS-token eller aktivera Azure AD-huvudobjektet för åtkomst till lagringsfiler Läs mer om åtkomst kontroll för lagring i [den här artikeln](develop-storage-files-storage-access-control.md).

`OPENROWSET` Använd följande regler för att avgöra hur du ska autentisera till lagring:
- I `OPENROWSET` utan `DATA_SOURCE` autentiseringsmekanism beror på samtals typ.
  - Alla användare kan använda `OPENROWSET` utan `DATA_SOURCE` att läsa offentligt tillgängliga filer i Azure Storage.
  - Azure AD-inloggningar kan komma åt skyddade filer med sin egen [Azure AD-identitet](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) om Azure Storage gör det möjligt för Azure AD-användaren att komma åt underliggande filer (till exempel om anroparen har `Storage Reader` behörighet för Azure Storage).
  - SQL-inloggningar kan också använda `OPENROWSET` utan `DATA_SOURCE` åtkomst till offentligt tillgängliga filer, filer som skyddas med SAS-token eller hanterad identitet för Synapse-arbetsytan. Du måste [skapa server-begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md#examples) för att tillåta åtkomst till lagringsfiler. 
- I `OPENROWSET` med autentiseringsmekanismen `DATA_SOURCE` definieras den autentiseringsuppgifter som tilldelats den refererade data källan i databasens begränsade autentiseringsuppgifter. Med det här alternativet kan du få åtkomst till offentligt tillgängligt lagrings utrymme, eller åtkomst till lagring med SAS-token, hanterad identitet för arbets ytan eller [Azure AD-identiteten](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (om anroparen är Azure AD-huvudobjekt). Om `DATA_SOURCE` du refererar till Azure Storage som inte är offentligt måste du [skapa databasens begränsade autentiseringsuppgifter](develop-storage-files-storage-access-control.md#examples) och referera till den i `DATA SOURCE` för att tillåta åtkomst till lagringsfiler.

Anroparen måste ha `REFERENCES` behörighet för autentiseringsuppgifter för att kunna använda den för att autentisera till lagring.

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
```

## <a name="arguments"></a>Argument

Det finns två alternativ för indatafiler som innehåller mål data för frågor. Giltiga värden är:

- ' CSV ' – innehåller en avgränsad textfil med avgränsare för rad/kolumn. Valfritt tecken kan användas som fält avgränsare, till exempel TSV: FIELDTERMINATOR = tab.

- ' PARQUET '-binär fil i Parquet-format 

**unstructured_data_path**

Unstructured_data_path som upprättar en sökväg till data kan vara en absolut eller relativ sökväg:
- Absoluta sökvägar i formatet " \<prefix> :// \<storage_account_path> / \<storage_path> " gör att en användare kan läsa filerna direkt.
- Relativ sökväg i formatet <storage_path> som måste användas med `DATA_SOURCE` parametern och beskriver fil mönstret i <storage_account_path> plats som definierats i `EXTERNAL DATA SOURCE` . 

Nedan hittar du relevanta <storage account path> värden som länkar till din specifika externa data källa. 

| Extern data Källa       | Prefix | Sökväg till lagrings konto                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http [s]  | \<storage_account>. dfs.core.windows.net/Path/File   |
| Azure Data Lake Store Gen2 | aufs [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Anger en sökväg i lagrings utrymmet som pekar på den mapp eller fil som du vill läsa. Om sökvägen pekar på en behållare eller mapp kommer alla filer att läsas från den aktuella behållaren eller mappen. Filer i undermappar tas inte med. 

Du kan använda jokertecken för att rikta in flera filer eller mappar. Användning av flera jokertecken som inte är i följd tillåts.
Nedan visas ett exempel som läser alla *CSV* -filer som börjar med *ifyllning* från alla mappar som börjar med */CSV/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Om du anger att unstructured_data_path ska vara en mapp, kommer en server lös SQL-pool fråga att hämta filer från den mappen. 

Du kan instruera Server lös SQL-poolen att bläddra igenom mappar genom att ange/* i slutet av sökvägen som i exempel: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar inte Server lös SQL-poolen undermappar om du inte anger/* * i slutet av sökvägen. Till skillnad från Hadoop och PolyBase returnerar även Server fri SQL-pool de filer som fil namnet börjar med en understrykning (_) eller en punkt (.).

I exemplet nedan, om unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , kommer en server lös SQL-pool fråga returnera rader från mydata.txt och _hidden.txt. Den returnerar inte mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

MED WITH-satsen kan du ange kolumner som du vill läsa från filer.

- För CSV-datafiler, för att läsa alla kolumner, anger du kolumn namn och deras data typer. Om du vill använda en delmängd av kolumner använder du ordnings tal för att välja kolumner från de ursprungliga datafilerna enligt ordnings tal. Kolumnerna binds enligt ordnings beteckningen. Om HEADER_ROW = TRUE används, så görs kolumn bindningen efter kolumn namn i stället för ordnings tal.
    > [!TIP]
    > Du kan även utesluta WITH-satsen för CSV-filer. Data typer härleds automatiskt från fil innehållet. Du kan använda HEADER_ROW argument om du vill ange förekomst av rubrik raden i vilket fall kolumn namn ska läsas från rubrik raden. Mer information finns i [Automatisk schema identifiering](#automatic-schema-discovery).
    
- För Parquet-datafiler, ange kolumn namn som matchar kolumn namnen i de ursprungliga datafilerna. Kolumnerna är kopplade till namn och är Skift läges känsliga. Om WITH-satsen utelämnas returneras alla kolumner från Parquet-filer.
    > [!IMPORTANT]
    > Kolumn namn i Parquet-filer är Skift läges känsliga. Om du anger ett kolumn namn med versaler som skiljer sig från kolumnens namn Skift läge i Parquet-filen returneras NULL-värden för den kolumnen.


column_name = namn för kolumnen utdata. Om det här namnet anges åsidosätts kolumn namnet i käll filen och kolumn namnet som anges i JSON-sökväg om det finns ett. Om json_path inte anges kommer den automatiskt att läggas till som $ .column_name. Kontrol lera json_path argumentet för beteende.

column_type = datatyp för kolumnen utdata. Konvertering av implicit datatyp sker här.

column_ordinal = ordnings talet för kolumnen i käll filen/källfilerna. Det här argumentet ignoreras för Parquet-filer eftersom bindningen görs efter namn. I följande exempel returneras endast en andra kolumn från en CSV-fil:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [JSON Path-uttryck](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) till kolumn-eller kapslad egenskap. Standard [Sök vägs läget](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) är lax.

> [!NOTE]
> Frågan i strikt läge Miss fungerar med ett fel om den angivna sökvägen inte finns. Frågan kommer att lyckas i lax-läge och uttryck för JSON-sökväg kommer att utvärderas till NULL.

**\<bulk_options>**

FIELDTERMINATOR = field_terminator

Anger vilken fält avslutning som ska användas. Standard fält avslutning är ett kommatecken ("**,**").

ROWTERMINATOR = row_terminator

Anger den rad avslutning som ska användas. Om ingen rad avgränsare anges används en av standardinställningarna. Standard avslutare för PARSER_VERSION = ' 1,0 ' är \r\n, \n och \r. Standard avslutare för PARSER_VERSION = ' 2,0 ' är \r\n och \n.

ESCAPE_CHAR = char

Anger det tecken i filen som används för att undanta sig själv och alla avgränsare värden i filen. Om Escape-symbolen följs av ett annat värde än sig själv, eller någon av avgränsarna, ignoreras escape-tecken vid läsning av värdet. 

Parametern ESCAPE_CHAR tillämpas oavsett om FIELDQUOTE är eller inte är aktive rad. Den används inte för att undanta citat tecken. Citat tecken måste föregås av ett annat citat tecken. Citat tecken får bara förekomma i kolumn värden om värdet är kapslat med citat tecken.

FIRSTROW = first_row 

Anger numret på den första raden som ska läsas in. Standardvärdet är 1 och anger den första raden i den angivna data filen. Rad numren bestäms genom att räkna rad avbrotten. FIRSTROW är 1-baserad.

FIELDQUOTE = field_quote 

Anger ett tecken som ska användas som citat tecken i CSV-filen. Om inget anges används citat tecknet ("). 

DATA_COMPRESSION = data_compression_method

Anger komprimerings metod. Stöds endast i PARSER_VERSION = 1.0. Följande komprimerings metod stöds:

- GZIP

PARSER_VERSION = parser_version

Anger vilken parser-version som ska användas vid läsning av filer. För närvarande finns versioner för CSV-parser som stöds 1,0 och 2,0:

- PARSER_VERSION = ' 1,0 '
- PARSER_VERSION = ' 2,0 '

CSV-parser version 1,0 är standard och Rich-funktionen. Version 2,0 är konstruerad för prestanda och har inte stöd för alla alternativ och kodningar. 

CSV-parser version 1,0-information:

- Följande alternativ stöds inte: HEADER_ROW.

CSV-parser version 2,0-information:

- Det finns inte stöd för alla typer av data.
- Maximal rad storleks gräns är 8 MB.
- Följande alternativ stöds inte: DATA_COMPRESSION.
- En tom sträng med citat tecken ("") tolkas som en tom sträng.
- Format som stöds för datum data typ: ÅÅÅÅ-MM-DD
- Format som stöds för tids data typen: tt: MM: SS [. bråktal sekunder]
- Format som stöds för DATETIME2-data typen: ÅÅÅÅ-MM-DD HH: MM: SS [. fraktions sekunder]

HEADER_ROW = {TRUE | !

Anger om CSV-filen innehåller en rubrik rad. Standardvärdet är FALSe. Stöds i PARSER_VERSION = ' 2.0 '. Om värdet är TRUE kommer kolumn namn att läsas från första raden enligt FIRSTROW-argumentet. Om sant och schema anges med WITH, utförs bindning av kolumn namn med hjälp av kolumn namn, inte ordnings positioner.

DATAFILETYPE = {' char ' | ' widechar '}

Anger encoding: char används för UTF8, widechar används för UTF16-filer.

## <a name="fast-delimited-text-parsing"></a>Snabb avgränsad text tolkning

Det finns två avgränsade versioner av text tolkare som du kan använda. CSV-parser version 1,0 är standard och funktions rik medan parser version 2,0 har skapats för prestanda. Prestanda förbättring i parsa 2,0 kommer från avancerade tolknings tekniker och multi-threading. Skillnaden i hastigheten blir större när fil storleken ökar.

## <a name="automatic-schema-discovery"></a>Automatisk schema identifiering

Du kan enkelt fråga både CSV-och Parquet-filer utan att veta eller ange schema genom att utesluta WITH-satsen. Kolumn namn och data typer kommer att härledas från filer.

Parquet-filer innehåller kolumn-metadata som ska läsas, typ mappningar kan hittas i [typ mappningar för Parquet](#type-mapping-for-parquet). Kontrol lera [att läsa Parquet-filer utan att ange schema](#read-parquet-files-without-specifying-schema) för exempel.

Kolumn namn för CSV-filer kan läsas från rubrik raden. Du kan ange om rubrik raden finns med HEADER_ROW argument. Om HEADER_ROW = falskt används generiska kolumn namn: C1, C2,... CN där n är antalet kolumner i filen. Data typer härleds från första 100 data rader. Kontrol lera [att läsa CSV-filer utan att ange schema](#read-csv-files-without-specifying-schema) för exempel.

> [!IMPORTANT]
> Det finns fall när lämplig datatyp inte kan härledas på grund av bristande information och större data typ används i stället. Detta ger bättre prestanda och är särskilt viktigt för Character-kolumner som kommer att härledas som varchar (8000). För optimala prestanda [kontrollerar du härledda data typer](best-practices-sql-on-demand.md#check-inferred-data-types) och [använder lämpliga data typer](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Typ mappning för Parquet

Parquet-filer innehåller typ beskrivningar för varje kolumn. I följande tabell beskrivs hur Parquet-typer mappas till inbyggda SQL-typer.

| Typ av Parquet | Parquet logiska typ (anteckning) | SQL-datatyp |
| --- | --- | --- |
| BOOLESKT | | bit |
| BINÄR/BYTE_ARRAY | | varbinary |
| DOUBLE | | flyt |
| FLYTA | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \* (utf8-sortering) |
| BINARY |NOLLÄNGD |varchar \* (utf8-sortering) |
| BINARY |RÄKNING|varchar \* (utf8-sortering) |
| FIXED_LEN_BYTE_ARRAY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (8000) \* (utf8-sortering) |
| BINARY |BSON | Stöds inte |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALL | Stöds inte |
| INT32 |INT (8, sant) |smallint |
| INT32 |INT (16, sant) |smallint |
| INT32 |INT (32, sant) |int |
| INT32 |INT (8, falskt) |tinyint |
| INT32 |INT (16, falskt) |int |
| INT32 |INT (32, falskt) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TID (MILLIS)|time |
| INT64 |INT (64, sant) |bigint |
| INT64 |INT (64, falskt) |decimal (20, 0) |
| INT64 |DECIMAL |decimal |
| INT64 |TID (MICROS) |Time-TIME (NANO) stöds inte |
|INT64 |TIDSSTÄMPEL (MILL/MICROS) |datetime2 – TIMESTAMP (NANO) stöds inte |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar (8000), serialiserad till JSON |
|[Komplex typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPPA|varchar (8000), serialiserad till JSON |

## <a name="examples"></a>Exempel

### <a name="read-csv-files-without-specifying-schema"></a>Läsa CSV-filer utan att ange schema

I följande exempel läser CSV-filen som innehåller rubrik raden utan att ange kolumn namn och data typer: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

I följande exempel läser CSV-filen som inte innehåller rubrik raden utan att ange kolumn namn och data typer: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Läsa Parquet-filer utan att ange schema

I följande exempel returneras alla kolumner för den första raden från data uppsättningen för inventering i Parquet-format, och utan att ange kolumn namn och data typer: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Läsa vissa kolumner från CSV-fil

I följande exempel returneras bara två kolumner med ordnings tal 1 och 4 från population*. csv-filerna. Eftersom det inte finns någon rubrik rad i filerna börjar den läsa från den första raden:

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

### <a name="read-specific-columns-from-parquet-file"></a>Läsa vissa kolumner från Parquet-filen

I följande exempel returneras bara två kolumner för den första raden från data uppsättningen för inventering i Parquet-format: 

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

I följande exempel visas hur du kan använda [uttryck för JSON-sökvägar](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) i With-satsen och visa skillnaden mellan strikt och lax Sök vägs lägen: 

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

Fler exempel finns i snabb starten för [fråga data Storage](query-data-storage.md) och lär dig hur du `OPENROWSET` använder för att läsa [CSV](query-single-csv-file.md)-, [PARQUET](query-parquet-files.md)-och [JSON](query-json-files.md) -filformat. Se [metod tips](best-practices-sql-on-demand.md) för att uppnå optimala prestanda. Du kan också lära dig hur du sparar resultatet av din fråga till Azure Storage med [CETAS](develop-tables-cetas.md).