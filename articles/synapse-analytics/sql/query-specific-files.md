---
title: Använda fil-metadata i frågor
description: Funktionen OpenRowSet innehåller fil-och Sök vägs information om alla filer som används i frågan för att filtrera eller analysera data baserat på fil namn och/eller mappsökväg.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96299623"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Använda fil-metadata i SQL-pooler utan Server

SQL-poolen utan server kan adressera flera filer och mappar enligt beskrivningen i artikeln [frågenoder och flera filer](query-folders-multiple-csv-files.md) . I den här artikeln får du lära dig hur du använder metadatainformation om fil-och mappnamn i frågorna.

Ibland kan du behöva veta vilken fil eller mapp källa som motsvarar en speciell rad i resultat uppsättningen.

Du kan använda funktionen `filepath` och `filename` för att returnera fil namn och/eller sökvägen i resultat uppsättningen. Eller så kan du använda dem för att filtrera data baserat på fil namnet och/eller mappsökvägen. Dessa funktioner beskrivs i funktionen syntax i avsnittet [filename](query-data-storage.md#filename-function) och funktionen fil [Sök väg](query-data-storage.md#filepath-function). I följande avsnitt hittar du korta beskrivningar utmed exempel.

## <a name="prerequisites"></a>Förutsättningar

Ditt första steg är att **skapa en databas** med en data källa som refererar till ett lagrings konto. Initiera sedan objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används i de här exemplen.

## <a name="functions"></a>Functions

### <a name="filename"></a>Sökväg

Den här funktionen returnerar fil namnet som raden kommer från.

Följande exempel läser de NYC gula taxi-datafilerna för de senaste tre månaderna av 2017 och returnerar antalet åsidosättningar per fil. Delen OpenRowSet i frågan anger vilka filer som ska läsas.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

I följande exempel visas hur *fil namn ()* kan användas i WHERE-satsen för att filtrera filerna som ska läsas. Den har åtkomst till hela mappen i OpenRowSet-delen av frågan och filtrerar filer i WHERE-satsen.

Resultatet är detsamma som i föregående exempel.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

Funktionen filsökväg returnerar en fullständig eller partiell sökväg:

- När den anropas utan en parameter, returnerar den den fullständiga fil Sök vägen som raden kommer från. När DATA_SOURCE används i OpenRowSet, returnerar den sökvägen i förhållande till DATA_SOURCE. 
- När den anropas med en parameter, returneras en del av sökvägen som matchar jokertecknet för den position som anges i parametern. Parameter värde 1 skulle till exempel returnera en del av sökvägen som matchar det första jokertecknet.

Följande exempel läser NYC Yellow taxi-datafiler under de tre senaste månaderna av 2017. Det returnerar antalet åsidosättningar per fil Sök väg. Delen OpenRowSet i frågan anger vilka filer som ska läsas.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

I följande exempel visas hur *sökvägen ()* kan användas i WHERE-satsen för att filtrera filerna som ska läsas.

Du kan använda jokertecken i delen OpenRowSet i frågan och filtrera filerna i WHERE-satsen. Resultatet är detsamma som i föregående exempel.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Nästa steg

I nästa artikel får du lära dig hur du [frågar Parquet-filer](query-parquet-files.md).
