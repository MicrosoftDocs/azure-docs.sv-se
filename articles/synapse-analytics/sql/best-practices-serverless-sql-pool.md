---
title: Metodtips för serverlös SQL-pool
description: Rekommendationer och metodtips för att arbeta med serverlös SQL-pool.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477476"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Metodtips för serverlös SQL-pool i Azure Synapse Analytics

I den här artikeln hittar du en samling metodtips för att använda serverlös SQL-pool. Serverlös SQL-pool är en resurs i Azure Synapse Analytics.

Med en serverlös SQL-pool kan du köra frågor mot filer i dina Azure-lagringskonton. Den har inte funktioner för lokal lagring eller inmatning. Därför är alla filer som frågemålen är externa för serverlösa SQL-pooler. Allt som rör läsning av filer från lagring kan påverka frågeprestanda.

Här är några allmänna riktlinjer:
- Kontrollera att dina klientprogram är samplacerade med den serverlösa SQL-poolen.
  - Om du använder klientprogram utanför Azure (till exempel Power BI Desktop, SSMS, ADS) kontrollerar du att du använder den serverlösa poolen i en region som ligger nära klientdatorn.
- Kontrollera att lagringen (Azure Data Lake, Cosmos DB) och den serverlösa SQL-poolen finns i samma region.
- Försök att [optimera lagringslayouten](#prepare-files-for-querying) genom att partitionera och hålla filerna i intervallet mellan 100 MB och 10 GB.
- Om du returnerar ett stort antal resultat kontrollerar du att du använder SSMS eller ADS och inte Synapse Studio. Synapse Studio är ett webbverktyg som inte är utformat för stora resultatmängder. 
- Om du filtrerar resultat efter strängkolumn kan du försöka använda `BIN2_UTF8` sortering.
- Försök att cachelagra resultaten på klientsidan genom att använda Power BI importläge eller Azure Analysis Services uppdatera dem regelbundet. De serverlösa SQL-poolerna kan inte ge interaktiv upplevelse i Power BI Direct Query-läge om du använder komplexa frågor eller bearbetar en stor mängd data.

## <a name="client-applications-and-network-connections"></a>Klientprogram och nätverksanslutningar

Kontrollera att klientprogrammet är anslutet till närmaste möjliga Synapse-arbetsyta med optimal anslutning.
- Samordna ett klientprogram med Synapse-arbetsytan. Om du använder program som Power BI eller Azure Analysis Service kontrollerar du att de finns i samma region där du har placerat Synapse-arbetsytan. Om det behövs skapar du de separata arbetsytorna som är parkopplade med dina klientprogram. Att placera ett klientprogram och Synapse-arbetsytan i en annan region kan orsaka längre svarstider och långsammare strömning av resultat.
- Om du läser data från ditt lokala program kontrollerar du att Synapse-arbetsytan finns i den region som ligger nära din plats.
- Se till att du inte har problem med nätverksbandbredden när du läser en stor mängd data.
- Använd inte Synapse Studio för att returnera stora mängder data. Synapse Studio är ett webbverktyg som använder HTTPS-protokoll för att överföra data. Använd Azure Data Studio eller SQL Server Management Studio för att läsa en stor mängd data.

## <a name="storage-and-content-layout"></a>Lagrings- och innehållslayout

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Samplacera din lagrings- och serverlösa SQL-pool

Du kan minimera svarstiden genom att samplacera ditt Azure Storage-konto eller CosmosDB-analyslagring och slutpunkten för din serverlösa SQL-pool. Lagringskonton och slutpunkter som etablerades när arbetsytan skapades finns i samma region.

Om du har åtkomst till andra lagringskonton med serverlös SQL-pool kontrollerar du att de finns i samma region för optimala prestanda. Om de inte finns i samma region ökar svarstiden för datanätverksöverföringen mellan fjärrregionen och slutpunktens region.

### <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagringskonto. Lagringsbegränsning inträffar när det kombinerade IOPS eller dataflödet som genereras av program, tjänster och serverlösa SQL-poolarbetsbelastningar överskrider gränserna för lagringskontot. Därför får du en betydande negativ inverkan på frågeprestanda.

När begränsning identifieras har serverlös SQL-pool inbyggd hantering för att lösa det. Serverlös SQL-pool gör begäranden till lagringen i långsammare takt tills begränsningen har lösts.

> [!TIP]
> För optimal frågekörning ska du inte stressa lagringskontot med andra arbetsbelastningar under frågekörningen.

### <a name="azure-ad-pass-through-performance"></a>Direktprestanda för Azure AD

Med en serverlös SQL-pool kan du komma åt filer i lagringen med hjälp Azure Active Directory (Azure AD) direkt- eller SAS-autentiseringsuppgifter. Prestandan kan bli långsammare med Azure AD-direktuppspelande än med SAS.

Om du behöver bättre prestanda kan du prova att använda SAS-autentiseringsuppgifter för att få åtkomst till lagring.

### <a name="prepare-files-for-querying"></a>Förbereda filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera stor CSV och JSON till Parquet. Parquet är ett kolumnformat. Eftersom filen är komprimerad är filstorlekarna mindre än CSV- eller JSON-filer som innehåller samma data. Serverlös SQL-pool kan hoppa över de kolumner och rader som inte behövs i frågan om du läser Parquet-filer. Serverlös SQL-pool behöver mindre tid och färre lagringsbegäranden för att läsa den.
- Om en fråga är inriktad på en enda stor fil kan du dra nytta av att dela upp den i flera mindre filer.
- Försök att hålla CSV-filstorleken mellan 100 MB och 10 GB.
- Det är bättre att ha filer med samma storlek för en enda OPENROWSET-sökväg eller en extern tabellPLATS.
- Partitionera dina data genom att lagra partitioner i olika mappar eller filnamn. Se [Använda funktioner för filnamn och filökväg för specifika partitioner.](#use-filename-and-filepath-functions-to-target-specific-partitions)

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Samplacera din CosmosDB-analyslagring och serverlösa SQL-pool

Kontrollera att CosmosDB-analyslagringen är placerad i samma region som Synapse-arbetsytan. Frågor mellan regioner kan orsaka stora svarstider. Använd regionsegenskapen i anslutningssträngen för att uttryckligen ange den region där analysarkivet placeras (se fråga [CosmosDb med serverlös SQL-pool](query-cosmos-db-analytical-store.md#overview)):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV-optimeringar

### <a name="use-parser_version-20-to-query-csv-files"></a>Använda PARSER_VERSION 2.0 för att fråga CSV-filer

Du kan använda en prestandaoptimerad parser när du frågar CSV-filer. Mer information finns i [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Skapa statistik manuellt för CSV-filer

Serverlös SQL-pool använder statistik för att generera optimala frågekörningsplaner. Statistik skapas automatiskt för kolumner i Parquet-filer när det behövs. För tillfället skapas inte statistik automatiskt för kolumner i CSV-filer och du bör skapa statistik manuellt för kolumner som du använder i frågor, särskilt de som används i DISTINCT, JOIN, WHERE, ORDER BY och GROUP BY. Mer [information finns i statistik i serverlös SQL-pool.](develop-tables-statistics.md#statistics-in-serverless-sql-pool)


## <a name="data-types"></a>Datatyper

### <a name="use-appropriate-data-types"></a>Använda lämpliga datatyper

De datatyper som du använder i din fråga påverkar prestandan. Du kan få bättre prestanda om du följer dessa riktlinjer: 

- Använd den minsta datastorlek som ska hantera det största möjliga värdet.
  - Om den maximala teckenlängden är 30 tecken använder du en teckendatatyp med längden 30.
  - Om alla teckenkolumnvärden har fast storlek använder du **char** eller **nchar**. Annars använder du **varchar** eller **nvarchar**.
  - Om det maximala heltalskolumnvärdet är 500 använder du **smallint** eftersom det är den minsta datatypen som kan hantera det här värdet. Du hittar intervall för heltalsdatatyper i den [här artikeln.](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- Om möjligt använder du **varchar och** **char i** stället för **nvarchar** och **nchar**.
- Använd heltalsbaserade datatyper om det är möjligt. ÅTGÄRDERNA SORT, JOIN och GROUP BY slutförs snabbare på heltal än på teckendata.
- Om du använder schemainferens kontrollerar [du härledningsdatatyperna](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Kontrollera härdade datatyper

[Schemaferens](query-parquet-files.md#automatic-schema-inference) hjälper dig att snabbt skriva frågor och utforska data utan att känna till filscheman. Kostnaden för den här bekvämligheten är att härdade datatyper kan vara större än de faktiska datatyperna. Detta inträffar när det inte finns tillräckligt med information i källfilerna för att se till att rätt datatyp används. Parquet-filer innehåller till exempel inte metadata om maximal teckenkolumnlängd. Serverlös SQL-pool härjar den alltså som varchar(8000).

Du kan använda [sp_describe_first_results_set för](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) att kontrollera de resulterande datatyperna för din fråga.

I följande exempel visas hur du kan optimera härdade datatyper. Den här proceduren används för att visa härdade datatyper: 
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

Här är resultatuppsättningen:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

När du känner till de härdade datatyperna för frågan kan du ange lämpliga datatyper:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Filteroptimering

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Skicka jokertecken till lägre nivåer i sökvägen

Du kan använda jokertecken i sökvägen för att [fråga efter flera filer och mappar.](query-data-storage.md#query-multiple-files-or-folders) Serverlös SQL-pool visar en lista över filer i ditt lagringskonto, från och med den första * med hjälp av lagrings-API:et. Det eliminerar filer som inte matchar den angivna sökvägen. Att minska den första listan med filer kan förbättra prestanda om det finns många filer som matchar den angivna sökvägen upp till det första jokertecknet.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Använda funktioner för filnamn och filökväg för specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera en serverlös SQL-pool att fråga specifika mappar och filer. Detta minskar antalet filer och mängden data som frågan behöver läsa och bearbeta. En extra bonus är att du får bättre prestanda.

Mer information finns i funktionerna [filename och](query-data-storage.md#filename-function) [filepath](query-data-storage.md#filepath-function) och i exemplen för att [köra frågor mot specifika filer.](query-specific-files.md)

> [!TIP]
> Casta alltid resultatet av funktionerna filepath och filename till lämpliga datatyper. Om du använder teckendatatyper måste du använda rätt längd.

> [!NOTE]
> Funktioner som används för partitionseliminering, filökväg och filnamn stöds för närvarande inte för externa tabeller, förutom de som skapas automatiskt för varje tabell som skapas i Apache Spark för Azure Synapse Analytics.

Om dina lagrade data inte är partitionerade kan du överväga att partitionera dem. På så sätt kan du använda dessa funktioner för att optimera frågor som riktar sig mot dessa filer. När du [frågar efter partitionerade Apache Spark för Azure Synapse](develop-storage-files-spark-tables.md) från en serverlös SQL-pool kommer frågan automatiskt endast att rikta in sig på de nödvändiga filerna.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Använd korrekt sortering för att använda predikat-pushdown för teckenkolumner

Data i parquet-filen ordnas i radgrupper. Serverlös SQL-pool hoppar över radgrupper baserat på angivet predikat i WHERE-satsen och minskar därmed I/O, vilket resulterar i bättre frågeprestanda. 

Observera att predikat-pushdown för teckenkolumner i parquet-filer stöds endast för Latin1_General_100_BIN2_UTF8 sortering. Du kan ange sortering för en viss kolumn med hjälp av WITH-satsen. Om du inte anger den här sorteringen med HJÄLP av WITH-satsen används databasarkopiering.

## <a name="optimize-repeating-queries"></a>Optimera upprepade frågor

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använda CETAS för att förbättra frågeprestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna i serverlös SQL-pool. CETAS är en parallell åtgärd som skapar externa tabellmetadata och exporterar SELECT-frågeresultaten till en uppsättning filer i ditt lagringskonto.

Du kan använda CETAS för att materialisera ofta använda delar av frågor, till exempel sammanfogade referenstabeller, till en ny uppsättning filer. Du kan sedan ansluta till den här externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor.

När CETAS genererar Parquet-filer skapas statistik automatiskt när den första frågan riktar sig till den externa tabellen, vilket resulterar i bättre prestanda för efterföljande frågor som riktas mot tabeller som genereras med CETAS.

## <a name="next-steps"></a>Nästa steg

Läs [felsökningsartikeln](resources-self-help-sql-on-demand.md) för lösningar på vanliga problem. Om du arbetar med dedikerad SQL-pool i stället för en serverlös SQL-pool kan du få specifik vägledning i Metodtips för [dedikerade](best-practices-dedicated-sql-pool.md) SQL-pooler.
