---
title: Fråga Azure Cosmos DB data med en server lös SQL-pool i Azure Synapse-länk
description: I den här artikeln får du lära dig hur du frågar Azure Cosmos DB med hjälp av en server lös SQL-pool i Azure Synapse-länken.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: a574cacbabf1c0d1730430153a3c0afcad6582c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694367"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Fråga Azure Cosmos DB data med en server lös SQL-pool i Azure Synapse-länk

Med en server lös SQL-pool kan du analysera data i Azure Cosmos DB behållare som är aktiverade med [Azure Synapse-länk](../../cosmos-db/synapse-link.md) i nära real tid utan att påverka prestandan för dina transaktions arbets belastningar. Den erbjuder en välkänd T-SQL-syntax för att fråga data från [analys lagret](../../cosmos-db/analytical-store-introduction.md) och integrerad anslutning till en mängd olika Business Intelligence (BI) och ad hoc-frågemeddelanden via T-SQL-gränssnittet.

För att skicka frågor till Azure Cosmos DB stöds det fullständiga [Select](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) -området via funktionen [OpenRowSet](develop-openrowset.md) , som innehåller merparten av [SQL Functions och operatorer](overview-features.md). Du kan också lagra resultat från frågan som läser data från Azure Cosmos DB tillsammans med data i Azure Blob Storage eller Azure Data Lake Storage genom att använda [skapa extern tabell som Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). För närvarande kan du inte lagra frågeresultat för SQL-pooler för server utan att Azure Cosmos DB med hjälp av CETAS.

I den här artikeln får du lära dig hur du skriver en fråga med en server lös SQL-pool som kommer att fråga efter data från Azure Cosmos DB behållare som är aktiverade med Azure Synapse länk. Du kan sedan lära dig mer om hur du skapar SQL-pooler utan server över Azure Cosmos DB behållare och ansluter dem till Power BI modeller i [den här självstudien](./tutorial-data-analyst.md). I den här självstudien används en behållare med ett [väldefinierat schema för Azure Cosmos DB](../../cosmos-db/analytical-store-introduction.md#schema-representation).

## <a name="overview"></a>Översikt

Med Server lös SQL-pool kan du fråga Azure Cosmos DB analys lagring med hjälp av `OPENROWSET` funktion. 
- `OPENROWSET` med infogad nyckel. Du kan använda den här syntaxen för att fråga Azure Cosmos DB samlingar utan att behöva förbereda autentiseringsuppgifter.
- `OPENROWSET` den refererade autentiseringsuppgiften som innehåller Cosmos DB konto nyckeln. Du kan använda den här syntaxen för att skapa vyer för Azure Cosmos DB samlingar.

### <a name="openrowset-with-key"></a>[OpenRowSet med nyckel](#tab/openrowset-key)

För att stödja frågor och analys av data i ett Azure Cosmos DB analys lager, använder en server lös SQL-pool följande `OPENROWSET` syntax:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Anslutnings strängen Azure Cosmos DB anger Azure Cosmos DB konto namn, databas namn, huvud nyckel för databas konto och ett valfritt region namn till `OPENROWSET` funktionen.

Anslutnings strängen har följande format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Namnet på Azure Cosmos DB containern anges utan citat tecken i `OPENROWSET` syntaxen. Om behållar namnet innehåller specialtecken, till exempel ett bindestreck (-), ska namnet omslutas inom hakparenteser ( `[]` ) i `OPENROWSET` syntaxen.

### <a name="openrowset-with-credential"></a>[OpenRowSet med autentiseringsuppgift](#tab/openrowset-credential)

Du kan använda `OPENROWSET` syntax som refererar till autentiseringsuppgifter:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Den Azure Cosmos DB anslutnings strängen innehåller inte nyckel i det här fallet. Anslutnings strängen har följande format:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Huvud nyckeln för databas kontot placeras i autentiseringsuppgifter på server nivå eller databas omfattning. 

---

> [!IMPORTANT]
> Kontrol lera att du använder en viss UTF-8-databas sortering, till exempel `Latin1_General_100_CI_AS_SC_UTF8` eftersom sträng värden i ett Azure Cosmos DB analys lager kodas som UTF-8-text.
> Ett matchnings fel mellan text kodning i filen och sorteringen kan orsaka oväntade text konverterings fel.
> Du kan enkelt ändra standard sorteringen för den aktuella databasen med hjälp av instruktionen T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> En server utan SQL-pool har inte stöd för att skicka frågor till en Azure Cosmos DB transaktions lagring.

## <a name="sample-dataset"></a>Exempel data uppsättning

Exemplen i den här artikeln baseras på data från [Europeiska centrum för sjukdoms förebyggande och kontroll (ECDC) COVID-19 fall](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) och [COVID-19 Open Research data uppsättning (kabel-19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Du kan se licensen och strukturen för data på dessa sidor. Du kan också hämta exempel data för [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) -och [kablar-19-](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) datauppsättningarna.

Om du vill följa med i den här artikeln som demonstrerar hur du frågar Azure Cosmos DB data med en server lös SQL-pool ser du till att du skapar följande resurser:

* Ett Azure Cosmos DB databas konto som är [Azure Synapse-länk aktiverat](../../cosmos-db/configure-synapse-link.md).
* En Azure Cosmos DB databas med namnet `covid` .
* Två Azure Cosmos DB behållare med namnet `Ecdc` och `Cord19` lästs in med föregående exempel data uppsättningar.

Du kan använda följande anslutnings sträng för testnings syfte: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Observera att den här anslutningen inte garanterar prestanda eftersom det här kontot kan finnas i fjärrregionen jämfört med din Synapse SQL-slutpunkt.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Utforska Azure Cosmos DB data med automatisk schema härledning

Det enklaste sättet att utforska data i Azure Cosmos DB är att använda funktionen för automatiskt schema härledning. Genom att utelämna- `WITH` satsen från `OPENROWSET` -instruktionen kan du instruera den serverbaserade SQL-poolen att automatiskt identifiera (härleda) schemat för den Azure Cosmos DB behållarens analys arkiv.

### <a name="openrowset-with-key"></a>[OpenRowSet med nyckel](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OpenRowSet med autentiseringsuppgift](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

I föregående exempel instruerade vi den serverbaserade SQL-poolen att ansluta till `covid` databasen i det Azure Cosmos DB konto `MyCosmosDbAccount` som autentiserats med hjälp av Azure Cosmos DBS nyckeln (provdocka i föregående exempel). Vi har sedan till gång till `Ecdc` behållarens analys Arkiv i `West US 2` regionen. Eftersom det inte finns någon projektion av vissa egenskaper, `OPENROWSET` returnerar funktionen alla egenskaper från Azure Cosmos DB objekt.

Förutsatt att objekten i Azure Cosmos DB-behållaren har `date_rep` , `cases` , och `geo_id` egenskaper, visas resultatet av den här frågan i följande tabell:

| date_rep | fall | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Om du behöver utforska data från den andra behållaren i samma Azure Cosmos DB databas kan du använda samma anslutnings sträng och referera till den nödvändiga behållaren som den tredje parametern:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Ange ett schema explicit

Även om funktionen för automatisk schema härledning i `OPENROWSET` tillhandahåller en enkel, lättanvänd querience, kan dina affärs scenarier kräva att du uttryckligen anger schemat till skrivskyddade relevanta egenskaper från Azure Cosmos db data.

Med `OPENROWSET` funktionen kan du uttryckligen ange vilka egenskaper du vill läsa från data i behållaren och ange deras data typer.

Anta att vi har importerat några data från [ECDC COVID-datauppsättningen](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) med följande struktur till Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Dessa enkla JSON-dokument i Azure Cosmos DB kan representeras som en uppsättning rader och kolumner i Synapse SQL. Med `OPENROWSET` funktionen kan du ange en delmängd av de egenskaper som du vill läsa och de exakta kolumn typerna i- `WITH` satsen:

### <a name="openrowset-with-key"></a>[OpenRowSet med nyckel](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OpenRowSet med autentiseringsuppgift](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
Resultatet av den här frågan kan se ut som i följande tabell:

| date_rep | fall | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Mer information om de SQL-typer som ska användas för Azure Cosmos DB värden finns i [reglerna för SQL-typnamn](#azure-cosmos-db-to-sql-type-mappings) i slutet av artikeln.

## <a name="create-view"></a>Skapa vy

Det är inte rekommenderat eller stöd för att skapa vyer i huvud databasen eller standard databaserna. Så du måste skapa en användar databas för dina vyer.

När du har identifierat schemat kan du förbereda en vy ovanpå dina Azure Cosmos DB data. Du bör placera din Azure Cosmos DB konto nyckel i en separat autentiseringsuppgift och referera till den här autentiseringsuppgiften från- `OPENROWSET` funktionen. Behåll inte din konto nyckel i vydefinitionen.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Använd inte `OPENROWSET` utan explicit definierat schema eftersom det kan påverka prestandan. Se till att du använder de minsta möjliga storlekarna för dina kolumner (till exempel VARCHAR (100) i stället för standard-VARCHAR (8000)). Du bör använda en UTF-8-sortering som standard sortering i databasen eller ange den som explicit kolumn sortering för att undvika [UTF-8-konverterings problem](../troubleshoot/reading-utf8-text.md). Sortering `Latin1_General_100_BIN2_UTF8` ger bästa prestanda när Yu filtrerar data med hjälp av vissa sträng kolumner.

## <a name="query-nested-objects-and-arrays"></a>Fråga kapslade objekt och matriser

Med Azure Cosmos DB kan du representera mer komplexa data modeller genom att skriva dem som kapslade objekt eller matriser. Funktionen för automatisk synkronisering av Azure Synapse-länken för Azure Cosmos DB hanterar schema representationen i analys lagret, som omfattar hantering av kapslade data typer som tillåter omfattande frågor från SQL-poolen utan server.

Till exempel har [kabeln-19-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) datauppsättningen JSON-dokument som följer den här strukturen:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

De kapslade objekten och matriserna i Azure Cosmos DB representeras som JSON-strängar i frågeresultatet när `OPENROWSET` funktionen läser dem. Du kan ange sökvägar till kapslade värden i objekten när du använder- `WITH` satsen:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Resultatet av den här frågan kan se ut som i följande tabell:

| paper_id | title | metadata | författaren |
| --- | --- | --- |
| bb11206963e831f... | Extra information ett eko-epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Användning av convalescent serum i immun-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) knöl och B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Lär dig mer om att analysera [komplexa data typer i Azure Synapse-länk](../how-to-analyze-complex-schema.md) och [kapslade strukturer i en server lös SQL-pool](query-parquet-nested-types.md).

> [!IMPORTANT]
> Om du ser oväntade tecken i din text `MÃƒÂ©lade` , t. ex. i stället för `Mélade` , är din databas sortering inte inställd på [UTF-8-](/sql/relational-databases/collations/collation-and-unicode-support#utf8) sortering.
> [Ändra sorteringen av databasen](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) till UTF-8-sortering genom att använda ett SQL-uttryck som `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Förenkla kapslade matriser

Azure Cosmos DB data kan ha kapslade undermatriser som författarens matris från en [kabel-19-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) datauppsättning:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

I vissa fall kan du behöva "koppla" egenskaperna från det översta objektet (metadata) med alla element i matrisen (författarna). Med en server lös SQL-pool kan du förenkla inkapslade strukturer genom att använda `OPENJSON` funktionen på den kapslade matrisen:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Resultatet av den här frågan kan se ut som i följande tabell:

| title | författaren | förstagångskörningen | pågå | anknytning |
| --- | --- | --- | --- | --- |
| Extra information ett eko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Extra information ett eko-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Extra information ett eko-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Extra information ett eko-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Om du ser oväntade tecken i din text `MÃƒÂ©lade` , t. ex. i stället för `Mélade` , är din databas sortering inte inställd på [UTF-8-](/sql/relational-databases/collations/collation-and-unicode-support#utf8) sortering. [Ändra sorteringen av databasen](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) till UTF-8-sortering genom att använda ett SQL-uttryck som `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB mappningar av SQL-typ

Även om Azure Cosmos DB transaktions lager är schema-oberoende, är analys lagret schematiserade för att optimera prestandan för analytiska frågor. Med funktionen för automatisk synkronisering av Azure Synapse-länken hanterar Azure Cosmos DB schema representationen i det analytiska lagret, som omfattar hantering av kapslade data typer. Eftersom en server utan SQL-pool frågar analys lagret är det viktigt att förstå hur du mappar Azure Cosmos DB indatatyper till SQL-datatyper.

Azure Cosmos DB konton av SQL-API (Core) stöder JSON-egenskapsvärde av typen Number, String, Boolean, null, nested Object eller array. Du måste välja SQL-typer som matchar dessa JSON-typer om du använder- `WITH` satsen i `OPENROWSET` . I följande tabell visas de typer av SQL-kolumner som ska användas för olika egenskaps typer i Azure Cosmos DB.

| Azure Cosmos DB egenskaps typ | SQL-kolumn typ |
| --- | --- |
| Boolesk | bit |
| Integer | bigint |
| Decimal | flyt |
| Sträng | varchar (UTF-8-databas sortering) |
| Datum tid (ISO-formaterad sträng) | varchar (30) |
| Datum tid (UNIX-tidsstämpel) | bigint |
| Null | `any SQL type` 
| Kapslat objekt eller matris | varchar (max) (UTF-8-databas sortering), serialiserad som JSON-text |

## <a name="full-fidelity-schema"></a>Schema för fullständig åter givning

Azure Cosmos DB full Fidelity schema registrerar både värden och deras bästa matchnings typer för varje egenskap i en behållare. `OPENROWSET`Funktionen på en behållare med schemat för fullständig åter givning ger både typen och det faktiska värdet i varje cell. Vi antar att följande fråga läser objekten från en behållare med schemat med fullständig åter givning:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Resultatet av den här frågan returnerar typer och värden formaterade som JSON-text:

| date_rep | fall | geo_id |
| --- | --- | --- |
| {"datum": "2020-08-13"} | {"Int32": "254"} | {"sträng": "RS"} |
| {"datum": "2020-08-12"} | {"Int32": "235"}| {"sträng": "RS"} |
| {"datum": "2020-08-11"} | {"Int32": "316"} | {"sträng": "RS"} |
| {"datum": "2020-08-10"} | {"Int32": "281"} | {"sträng": "RS"} |
| {"datum": "2020-08-09"} | {"Int32": "295"} | {"sträng": "RS"} |
| {"sträng": "2020/08/08"} | {"Int32": "312"} | {"sträng": "RS"} |
| {"datum": "2020-08-07"} | {"float64":"339.0"} | {"sträng": "RS"} |

För varje värde kan du se vilken typ som identifieras i ett Azure Cosmos DB Container objekt. De flesta värden för `date_rep` egenskapen innehåller `date` värden, men en del av dem lagras felaktigt som strängar i Azure Cosmos dB. Schemat för fullständig åter givning returnerar både korrekt skrivna `date` värden och felaktigt formaterade `string` värden.
Antalet ärenden är information som lagras som ett `int32` värde, men det finns ett värde som anges som ett decimal tal. Det här värdet är av `float64` typen. Om det finns några värden som överstiger det största `int32` antalet lagras de som `int64` typen. Alla `geo_id` värden i det här exemplet lagras som- `string` typer.

> [!IMPORTANT]
> `OPENROWSET`Funktionen utan en `WITH` sats visar båda värdena med förväntade typer och värden med felaktigt angivna typer. Den här funktionen är avsedd för data utforskning och inte för rapportering. Parsa inte JSON-värden som returneras från den här funktionen för att bygga rapporter. Använd en explicit [with-sats](#query-items-with-full-fidelity-schema) för att skapa dina rapporter. Du bör rensa värdena som har felaktiga typer i Azure Cosmos DB containern för att tillämpa korrigeringar i analys lagret med fullständig åter givning.

Om du behöver fråga Azure Cosmos DB konton i Mongo DB API-typen, kan du lära dig mer om schemat för fullständig åter givning i analys lagringen och de utökade egenskaps namnen som ska användas i [Vad är Azure Cosmos DB analys lager?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Fråga objekt med schemat med fullständig åter givning

Vid frågor om schemat för fullständig åter givning måste du uttryckligen ange SQL-typ och den förväntade Azure Cosmos DB egenskaps typen i- `WITH` satsen.

I följande exempel förutsätter vi att `string` är rätt typ för `geo_id` egenskapen och `int32` är rätt typ för `cases` egenskapen:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Värden för `geo_id` och `cases` som har andra typer kommer att returneras som `NULL` värden. Den här frågan refererar endast `cases` till den angivna typen i uttrycket ( `cases.int32` ).

Om du har värden med andra typer ( `cases.int64` , `cases.float64` ) som inte kan rensas i en Azure Cosmos DB behållare, måste du uttryckligen referera till dem i en `WITH` sats och kombinera resultaten. Följande fråga aggregerar både `int32` , `int64` , och `float64` lagras i `cases` kolumnen:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

I det här exemplet lagras antalet fall som `int32` -, `int64` -eller- `float64` värden. Alla värden måste extraheras för att beräkna antalet ärenden per land.

## <a name="known-issues"></a>Kända problem

- En server utan SQL-pool returnerar en kompileringsfel om `OPENROWSET` kolumn sorteringen inte har UTF-8-kodning. Du kan enkelt ändra standard sorteringen för alla `OPENROWSET` funktioner som körs i den aktuella databasen med hjälp av instruktionen T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Möjliga fel och fel söknings åtgärder visas i följande tabell.

| Fel | Rotorsak |
| --- | --- |
| Syntaxfel:<br/> -Felaktig syntax nära `Openrowset`<br/> - `...` är inte ett känt `BULK OPENROWSET` Provider-alternativ.<br/> -Felaktig syntax nära `...` | Möjliga rotor orsaker:<br/> – Använder inte CosmosDB som första parameter.<br/> – Använder en stränglitteral i stället för en identifierare i den tredje parametern.<br/> – Anger inte den tredje parametern (container Name). |
| Ett fel uppstod i CosmosDB-anslutningssträngen. | -Kontot, databasen eller nyckeln har inte angetts. <br/> – Det finns ett alternativ i en anslutnings sträng som inte känns igen.<br/> -Ett semikolon ( `;` ) placeras i slutet av en anslutnings sträng. |
| Det gick inte att matcha CosmosDB Sök väg med felet "felaktigt konto namn" eller "ogiltigt databas namn". | Det angivna konto namnet, databas namnet eller behållaren kan inte hittas, eller så har inte analytisk lagring Aktiver ATS för den angivna samlingen.|
| Det gick inte att matcha CosmosDB-sökvägen med felet "felaktigt hemligt värde" eller "hemlighet är null eller tomt". | Konto nyckeln är inte giltig eller saknas. |
| Kolumnen `column name` av typen `type name` är inte kompatibel med den externa data typen `type name` . | Den angivna kolumn typen i `WITH` satsen matchar inte typen i Azure Cosmos DB containern. Försök att ändra kolumn typen som den beskrivs i avsnittet [Azure Cosmos dB till SQL-typ mappningar](#azure-cosmos-db-to-sql-type-mappings)eller Använd `VARCHAR` typen. |
| Kolumnen innehåller `NULL` värden i alla celler. | Eventuellt ett kolumn namn eller ett Sök vägs uttryck i- `WITH` satsen. Kolumn namnet (eller Sök vägs uttrycket efter kolumn typen) i- `WITH` satsen måste överensstämma med ett egenskaps namn i Azure Cosmos DB samlingen. Jämförelse är *SKIFT* läges känslig. Till exempel `productCode` och `ProductCode` är olika egenskaper. |

Du kan rapportera förslag och problem på [feedback-sidan för Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Använd Power BI-och Server lös SQL-pool med Azure Synapse-länk](../../cosmos-db/synapse-link-power-bi.md)
- [Skapa och använda vyer i en SQL-pool utan Server](create-use-views.md)
- [Självstudie om hur du skapar SQL-pooler utan server över Azure Cosmos DB och ansluter dem till Power BI modeller via DirectQuery](./tutorial-data-analyst.md)
