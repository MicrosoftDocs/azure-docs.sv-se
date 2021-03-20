---
title: Synkronisera Apache Spark för externa tabell definitioner i SQL-poolen utan Server
description: Översikt över hur du frågar Spark-tabeller med en server lös SQL-pool
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446500"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Synkronisera Apache Spark för externa definitioner av Azure Synapse-tabeller i SQL-poolen utan Server

SQL-poolen utan server kan automatiskt synkronisera metadata från Apache Spark. En databas för SQL-poolen utan server skapas för varje databas som finns i Server lös Apache Spark pooler. 

För varje spark-extern tabell som baseras på Parquet och finns i Azure Storage skapas en extern tabell i en server lös SQL-adresspool. Därför kan du stänga av Spark-poolerna och fortfarande fråga Spark-externa tabeller från Server lös SQL-poolen.

När en tabell är partitionerad i Spark ordnas filer i lagringen efter mappar. SQL-poolen utan server kommer att använda metadata för partitionen och endast rikta relevanta mappar och filer för frågan.

Synkronisering av metadata konfigureras automatiskt för varje server lös Apache Spark pool som tillhandahålls i Azure dataSynapses-arbetsytan. Du kan börja fråga direkt externa tabeller direkt.

Varje Spark Parquet extern tabell som finns i Azure Storage representeras av en extern tabell i ett dbo-schema som motsvarar en server lös SQL-adresspool. 

Kör en fråga som är riktad mot en extern [spark_table] för Spark-frågor för externa tabeller. Kontrol lera att du har rätt [åtkomst till lagrings kontot](develop-storage-files-storage-access-control.md) där filerna finns innan du kör följande exempel.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Kommandona Add, Drop eller Alter Spark external Table för en kolumn visas inte i den externa tabellen i SQL-poolen utan server.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark data typer till SQL data types-mappning

| Spark-datatyp | SQL-datatyp               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Kort typ      | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | flyt                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) \* (till JSON)\** |
| MapType         | varchar (max) \* (till JSON)\** |
| StructType      | varchar (max) \* (till JSON)\** |

\* Den sortering som används är Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType och StructType representeras som JSON-exempel.



## <a name="next-steps"></a>Nästa steg

Gå vidare till [lagrings Access Controls](develop-storage-files-storage-access-control.md) artikeln om du vill veta mer om åtkomst kontroll för lagring.
