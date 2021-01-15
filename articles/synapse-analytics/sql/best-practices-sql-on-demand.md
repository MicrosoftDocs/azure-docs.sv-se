---
title: Metod tips för Server lös SQL-pool
description: Rekommendationer och metod tips för att arbeta med en server lös SQL-pool.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c752bc6ae49f009056067545fde292dc29027d5d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208139"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Metod tips för Server lös SQL-pool i Azure Synapse Analytics

I den här artikeln hittar du en samling bästa metoder för att använda SQL-poolen utan server. SQL-poolen utan server är en resurs i Azure Synapse Analytics.

## <a name="general-considerations"></a>Generella saker att tänka på

Med Server lös SQL-pool kan du söka efter filer i dina Azure Storage-konton. Den har inte funktioner för lokal lagring eller inmatning. Alla filer som frågan riktar sig till är externa för SQL-poolen utan server. Allt som rör läsning av filer från lagring kan påverka frågans prestanda.

## <a name="colocate-your-storage-and-serverless-sql-pool"></a>Samplacera din lagrings-och Server lös SQL-pool

Du kan minimera svars tiden genom att samplacera ditt Azure Storage-konto eller CosmosDB-analytiska lagrings utrymme och den serverbaserade SQL-adresspoolen. Lagrings konton och slut punkter som tillhandahålls när arbets ytan skapas befinner sig i samma region.

För optimala prestanda bör du kontrol lera att de finns i samma region om du har åtkomst till andra lagrings konton med en server lös SQL-pool. Om de inte finns i samma region ökar svars tiden för data överföring mellan den fjärranslutna regionen och slut punktens region.

## <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagrings konto. Lagrings begränsning sker när den sammanlagda IOPS eller data flödet som genereras av program, tjänster och arbets belastningen för en server fri SQL-pool överskrider lagrings kontots gränser. Därför får du en betydande negativ effekt på frågans prestanda.

När begränsningen har identifierats har SQL-poolen utan server inbyggd hantering för att lösa det. SQL-poolen utan Server gör begär anden till lagringen långsammare tills begränsningen har lösts.

> [!TIP]
> För optimal frågekörning ska du inte stressa lagrings kontot med andra arbets belastningar under frågekörningen.

## <a name="prepare-files-for-querying"></a>Förbered filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera stor CSV och JSON till Parquet. Parquet är ett kolumn format. Eftersom fil storleken är komprimerad är fil storleken mindre än CSV eller JSON-filer som innehåller samma data. SQL-poolen utan server kan hoppa över kolumner och rader som inte behövs i frågan om du läser Parquet-filer. SQL-poolen utan server behöver mindre tid och färre lagrings begär Anden att läsa den.
- Om en fråga är riktad mot en enda stor fil, drar du nytta av att dela upp den i flera mindre filer.
- Försök att behålla storleken på CSV-filen mellan 100 MB och 10 GB.
- Det är bättre att ha lika stora filer för en enskild OpenRowSet-sökväg eller en extern tabell plats.
- Partitionera dina data genom att lagra partitioner i olika mappar eller fil namn. Se [använda fil namns-och fil Sök vägar för att fokusera på specifika partitioner](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Jokertecken för push-meddelanden till lägre nivåer i sökvägen

Du kan använda jokertecken i din sökväg för att [fråga flera filer och mappar](query-data-storage.md#query-multiple-files-or-folders). SQL-pool utan server listar filer i ditt lagrings konto, från och med den första * använda Storage API. Den eliminerar filer som inte matchar den angivna sökvägen. Att minska den inledande listan över filer kan förbättra prestanda om det finns många filer som matchar den angivna sökvägen upp till det första jokertecknet.

## <a name="use-appropriate-data-types"></a>Använd lämpliga data typer

De data typer som du använder i frågan påverkar prestanda. Du kan få bättre prestanda om du följer dessa rikt linjer: 

- Använd den minsta data storlek som ska hantera det största möjliga värdet.
  - Om den maximala tecken längden är 30 tecken, använder du tecken data typen 30.
  - Om alla tecken kolumn värden har fast storlek använder du **char** eller **nchar**. Annars använder du **varchar** eller **nvarchar**.
  - Om värdet för heltals kolumn är 500 använder du **smallint** eftersom det är den minsta data typen som kan hantera det här värdet. Du kan hitta data typs intervall av typen Integer i [den här artikeln](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Använd om möjligt **varchar** och **char** i stället för **nvarchar** och **nchar**.
- Använd Integer-baserade data typer om möjligt. Åtgärder för att sortera, ansluta och gruppera efter slutförs snabbare med heltal än på Character data.
- Om du använder schema härledning, [kontrollerar du härledda data typer](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Kontrol lera härledda data typer

[Schema härledning](query-parquet-files.md#automatic-schema-inference) hjälper dig att snabbt skriva frågor och utforska data utan att känna till fil scheman. Kostnaden för den här bekvämligheten är att härledda data typer kan vara större än de faktiska data typerna. Detta inträffar när det inte finns tillräckligt med information i källfilerna för att se till att rätt datatyp används. Parquet-filer innehåller till exempel inte metadata om maximal tecken kolumn längd. Så Server lös SQL-poolen härleds den som varchar (8000).

Du kan använda [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) för att kontrol lera de resulterande data typerna i din fråga.

I följande exempel visas hur du kan optimera härledda data typer. Den här proceduren används för att Visa härledda data typer: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Här är resultat uppsättningen:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

När du känner till de härledda data typerna för frågan kan du ange lämpliga data typer:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Använd filename-och filename-funktioner för att fokusera på specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera Server lös SQL-poolen att fråga specifika mappar och filer. Då minskas antalet filer och mängden data som frågan behöver läsa och bearbeta. En extra bonus är att du får bättre prestanda.

Mer information finns i avsnittet om [fil namn](query-data-storage.md#filename-function) och fil [Sök väg](query-data-storage.md#filepath-function) och se exemplen för att [fråga efter vissa filer](query-specific-files.md).

> [!TIP]
> Skicka alltid resultatet av sökvägen och fil namns funktionerna till lämpliga data typer. Om du använder tecken data typer måste du se till att du använder rätt längd.

> [!NOTE]
> Funktioner som används för partitions Eli minering, sökväg och fil namn stöds för närvarande inte för externa tabeller, förutom de som skapats automatiskt för varje tabell som skapats i Apache Spark för Azure Synapse Analytics.

Om dina lagrade data inte är partitionerade kan du partitionera dem. På så sätt kan du använda dessa funktioner för att optimera frågor som riktar sig mot dessa filer. När du [frågar partitionerade Apache Spark för Azure Synapse-tabeller](develop-storage-files-spark-tables.md) från en server lös SQL-pool, kommer frågan automatiskt att rikta in sig på de nödvändiga filerna.

## <a name="use-parser_version-20-to-query-csv-files"></a>Använd PARSER_VERSION 2,0 för att fråga CSV-filer

Du kan använda en Prestandaoptimerad parser när du frågar CSV-filer. Mer information finns i [PARSER_VERSION](develop-openrowset.md).

## <a name="manually-create-statistics-for-csv-files"></a>Skapa statistik för CSV-filer manuellt

SQL-poolen utan server använder statistik för att generera optimala fråge körnings planer. Statistik skapas automatiskt för kolumner i Parquet-filer vid behov. Nu skapas statistik inte automatiskt för kolumner i CSV-filer och du bör skapa statistik manuellt för kolumner som du använder i frågor, särskilt de som används i DISTINCT, JOIN, WHERE, ORDER BY och GROUP BY. Mer information finns [i statistik i SQL-poolen utan server](develop-tables-statistics.md#statistics-in-serverless-sql-pool) .

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågornas prestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna som finns i SQL-poolen utan server. CETAS är en parallell åtgärd som skapar externa tabellens metadata och exporterar URVALs resultatet till en uppsättning filer i ditt lagrings konto.

Du kan använda CETAS för att lagra ofta använda delar av frågor som kopplade referens tabeller till en ny uppsättning filer. Du kan sedan ansluta till den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor.

När CETAS genererar Parquet-filer skapas statistik automatiskt när den första frågan riktar sig mot den här externa tabellen, vilket resulterar i förbättrade prestanda för efterföljande frågor som mål tabellen genererar med CETAS.

## <a name="azure-ad-pass-through-performance"></a>Prestanda för Azure AD-vidarekoppling

Med Server lös SQL-poolen kan du komma åt filer i lagringen med hjälp av Azure Active Directory (Azure AD) genom strömnings-eller SAS-autentiseringsuppgifter. Du kan uppleva sämre prestanda med Azure AD genom strömning än med SAS.

Om du behöver bättre prestanda kan du försöka använda SAS-autentiseringsuppgifter för att komma åt lagrings utrymmet tills Azure AD-vidarekoppling har förbättrats.

## <a name="next-steps"></a>Nästa steg

Läs [fel söknings](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) artikeln för lösningar på vanliga problem. Om du arbetar med en dedikerad SQL-pool i stället för en server lös SQL-pool, se [metod tips för dedikerade SQL-pooler](best-practices-sql-pool.md) för särskilda rikt linjer.
