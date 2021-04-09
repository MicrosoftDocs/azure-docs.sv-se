---
title: Delade metadata-tabeller
description: Azure Synapse Analytics tillhandahåller en delad metadata modell där du skapar en tabell i en server lös Apache Spark pool kommer att vara tillgänglig från Server lös SQL-pool och dedikerad SQL-pool utan att duplicera data.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: a8080720480beaeb7bc8692f2dcddddad5da0e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548469"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabeller för delade metadata i Azure Synapse Analytics


Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och Parquet tabeller mellan Apache Spark pooler och SQL-pool utan server.

När en databas har skapats av ett Spark-jobb kan du skapa tabeller i den med Spark som använder Parquet som lagrings format. Tabell namn kommer att konverteras till gemener och måste frågas med gemener/versaler. Tabellerna blir omedelbart tillgängliga för frågor från någon av Azure Synapse-arbetsytans Spark-pooler. De kan också användas från alla Spark-jobb som omfattas av behörigheter.

Spark created-, Managed-och external-tabellerna görs också tillgängliga som externa tabeller med samma namn i motsvarande synkroniserade databas i SQL-poolen utan server. Att [exponera en spark-tabell i SQL](#expose-a-spark-table-in-sql) innehåller mer information om Table-synkroniseringen.

Eftersom tabellerna är synkroniserade med en server lös SQL-pool asynkront kommer det att finnas en fördröjning tills de visas.

## <a name="manage-a-spark-created-table"></a>Hantera en spark-skapad tabell

Använd Spark för att hantera Spark-skapade databaser. Du kan t. ex. ta bort den via ett jobb utan server Apache Spark pool och skapa tabeller i det från Spark.

Om du skapar objekt i en sådan databas från en server lös SQL-pool eller försöker släppa databasen kommer åtgärden att Miss Miss läge. Den ursprungliga Spark-databasen kan inte ändras via server lös SQL-pool.

## <a name="expose-a-spark-table-in-sql"></a>Exponera en spark-tabell i SQL

### <a name="shared-spark-tables"></a>Delade Spark-tabeller

Spark innehåller två typer av tabeller som Azure Synapse visar i SQL automatiskt:

- Hanterade tabeller

  Spark innehåller många alternativ för att lagra data i hanterade tabeller, till exempel TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA och LIBSVM. De här filerna lagras vanligt vis i den `warehouse` katalog där hanterade tabell data lagras.

- Externa tabeller

  Spark ger också olika sätt att skapa externa tabeller över befintliga data, antingen genom att tillhandahålla `LOCATION` alternativet eller använda Hive-formatet. Sådana externa tabeller kan vara över en mängd olika data format, inklusive Parquet.

Azure-Synapse delar för närvarande bara hanterade och externa Spark-tabeller som lagrar sina data i Parquet-format med SQL-motorerna. Tabeller som backas upp av andra format synkroniseras inte automatiskt. Du kanske kan synkronisera sådana tabeller manuellt som en extern tabell i din egen SQL-databas om SQL-motorn stöder tabellens underliggande format.

### <a name="share-spark-tables"></a>Dela Spark-tabeller

De shareable-hanterade och externa Spark-tabellerna som visas i SQL-motorn som externa tabeller med följande egenskaper:

- Den externa SQL-tabellens data källa är data källan som representerar Spark-tabellens plats-mapp.
- Den externa SQL-tabellens fil format är Parquet.
- Den externa SQL-tabellens åtkomst-autentiseringsuppgifter är direkt.

Eftersom alla Spark-tabell namn är giltiga SQL-tabell namn och alla Spark kolumn namn är giltiga, används Spark-tabellen och kolumn namnen för den externa SQL-tabellen i SQL-kolumn.

Spark-tabeller tillhandahåller olika data typer än Synapse SQL-motorer. Följande tabell mappar Spark-tabellens data typer mappar till SQL-typerna:

| Spark-datatyp | SQL-datatyp | Kommentarer |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Med sortering `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serialiserar till JSON med sortering `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serialiserar till JSON med sortering `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serialiserar till JSON med sortering `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaser och-tabeller, samt deras synkroniserade representationer i SQL-motorn, kommer att skyddas på den underliggande lagrings nivån. Eftersom de för närvarande inte har behörighet till själva objekten kan objekten visas i Object Explorer.

Det säkerhetsobjekt som skapar en hanterad tabell anses vara ägare av den tabellen och har alla rättigheter till tabellen samt underliggande mappar och filer. Dessutom blir databasens ägare automatiskt medägare till tabellen.

Om du skapar en Spark- eller SQL-tabell med direktautentisering skyddas data endast på mapp- och filnivå. Om någon kör frågor mot den här typen av extern tabell skickas säkerhetsidentiteten för frågeskickaren vidare till filsystemet, som kontrollerar åtkomstbehörigheter.

Mer information om hur du anger behörigheter för mappar och filer finns i [Azure Synapse Analytics Shared Database](database.md).

## <a name="examples"></a>Exempel

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Skapa en hanterad tabell som backas upp av Parquet i Spark och fråga från Server lös SQL-pool

I det här scenariot har du en spark-databas med namnet `mytestdb` . Se [skapa och ansluta till en spark-databas med en server lös SQL-pool](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Skapa en hanterad Spark-tabell med SparkSQL genom att köra följande kommando:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Det här kommandot skapar tabellen `myparquettable` i-databasen `mytestdb` . Tabell namn kommer att konverteras till gemener. Efter en kort fördröjning kan du se tabellen i din server lös SQL-pool. Kör till exempel följande-sats från din server lös SQL-pool.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Kontrol lera att `myparquettable` ingår i resultaten.

>[!NOTE]
>En tabell som inte använder Parquet som lagrings format kommer inte att synkroniseras.

Infoga sedan några värden i tabellen från Spark, till exempel med följande C# Spark-uttryck i en C#-anteckningsbok:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Nu kan du läsa data från SQL-poolen utan server på följande sätt:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Du bör hämta följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Skapa en extern tabell som backas upp av Parquet i Spark och fråga från Server lös SQL-pool

I det här exemplet skapar du en extern Spark-tabell över de Parquet-datafiler som skapades i föregående exempel för den hanterade tabellen.

Till exempel när SparkSQL körs:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Ersätt plats hållaren `<fs>` med fil system namnet som är standard fil systemet för arbets ytan och plats hållaren `<synapse_ws>` med namnet på Synapse-arbetsytan som du använder för att köra det här exemplet.

I föregående exempel skapas tabellen `myextneralparquettable` i-databasen `mytestdb` . Efter en kort fördröjning kan du se tabellen i din server lös SQL-pool. Kör till exempel följande-sats från din server lös SQL-pool.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Kontrol lera att `myexternalparquettable` ingår i resultaten.

Nu kan du läsa data från SQL-poolen utan server på följande sätt:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Du bör hämta följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Nästa steg

- [Läs mer om delade metadata för Azure Synapse Analytics](overview.md)
- [Läs mer om Azure Synapse Analytics-databasen för delad metadata](database.md)


