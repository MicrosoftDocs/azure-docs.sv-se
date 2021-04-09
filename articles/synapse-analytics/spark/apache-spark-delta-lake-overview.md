---
title: Översikt över hur du använder Linux Foundation delta Lake i Apache Spark för Azure Synapse Analytics
description: Lär dig hur du använder delta Lake i Apache Spark för Azure Synapse Analytics för att skapa och använda tabeller med sur egenskaper.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676301"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation delta Lake-översikt

Den här artikeln har anpassats för mer klarhet från den ursprungliga motsvarigheten [här](https://docs.delta.io/latest/quick-start.html). Den här artikeln hjälper dig att snabbt utforska de viktigaste funktionerna i [delta Lake](https://delta.io). Artikeln innehåller kodfragment som visar hur du läser från och skriver till delta Lake-tabeller från interaktiva, batch-och strömnings frågor. Kodfragmenten är också tillgängliga i en uppsättning Notebooks [PySpark här](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala här](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)och [C# här](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

Det här är vad vi ska avse:

* Skapa en tabell
* Läsa data
* Uppdatera tabell data
* Skriv över tabell data
* Villkorlig uppdatering utan överskrivning
* Läs äldre versioner av data med hjälp av res tid
* Skriva en data ström till en tabell
* Läs en ström med ändringar från en tabell
* SQL-stöd

## <a name="configuration"></a>Konfiguration

Se till att ändra de nedan som passar din miljö.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Resultat i:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Skapa en tabell

Om du vill skapa en delta Lake-tabell skriver du en DataFrame ut en DataFrame i delta formatet. Du kan ändra formatet från Parquet, CSV, JSON och så vidare till delta.

Koden nedan visar hur du skapar en ny delta Lake-tabell med hjälp av schemat som härleds från din DataFrame.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Resultat i:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Läsa data

Du läser data i din delta Lake-tabell genom att ange sökvägen till filerna och delta formatet.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Resultat i:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Ordningen på resultaten skiljer sig från ovan, eftersom ingen ordning uttryckligen angavs innan resultatet kunde placeras.

## <a name="update-table-data"></a>Uppdatera tabell data

Delta Lake stöder flera åtgärder för att ändra tabeller med hjälp av standard DataFrame-API: er, detta är en av de stora förbättringar som delta formatet lägger till. I följande exempel körs ett batch-jobb för att skriva över data i tabellen.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Resultat i:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Här kan du se att alla fem poster har uppdaterats för att innehålla nya värden.

## <a name="save-as-catalog-tables"></a>Spara som katalog tabeller

Delta Lake kan skriva till hanterade eller externa katalog tabeller.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Resultat i:

|databas|         tableName|isTemporary|
|--------|------------------|-----------|
| standard|externaldeltatable|      falskt|
| standard| manageddeltatable|      falskt|

Med den här koden skapade du en ny tabell i katalogen från en befintlig dataframe, som kallas för en hanterad tabell. Sedan definierade du en ny extern tabell i katalogen som använder en befintlig plats, som en extern tabell. I de utdata du kan se båda tabellerna, oavsett hur de skapades, visas i katalogen.

Nu kan du titta på de utökade egenskaperna för båda dessa tabeller

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Resultat i:

|col_name                    |data_type                                                                                                    |kommentar|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Detaljerad tabell information  |                                                                                                             |       |
|Databas                    |standard                                                                                                      |       |
|Tabell                       |manageddeltatable                                                                                            |       |
|Ägare                       |betrodd-tjänst-användare                                                                                         |       |
|Genereringstid                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|Senaste åtkomst                 |Tor Jan 01 00:00:00 UTC 1970                                                                                 |       |
|Skapad av                  |Spark-2.4.4.2.6.99.201 – 11401300                                                                              |       |
|Typ                        |LEDA                                                                                                      |       |
|Leverantör                    |delta                                                                                                        |       |
|Tabell egenskaper            |[transient_lastDdlTime = 1587774934]                                                                           |       |
|Statistik                  |2407 byte                                                                                                   |       |
|Location                    |abfss://data @ <data lake> . DFS.Core.Windows.net/Synapse/workspaces/ <workspace name> /Warehouse/manageddeltatable|       |
|Serde-bibliotek               |org. apache. Hadoop. Hive. serde2. Lazy. LazySimpleSerDe                                                           |       |
|InputFormat                 |org. apache. Hadoop. mapred. SequenceFileInputFormat                                                             |       |
|OutputFormat                |org. apache. Hadoop. Hive. QL. io. HiveSequenceFileOutputFormat                                                    |       |
|Lagrings egenskaper          |[serialisering. format = 1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Resultat i:

|col_name                    |data_type                                                             |kommentar|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Detaljerad tabell information  |                                                                      |       |
|Databas                    |standard                                                               |       |
|Tabell                       |externaldeltatable                                                    |       |
|Ägare                       |betrodd-tjänst-användare                                                  |       |
|Genereringstid                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|Senaste åtkomst                 |Tor Jan 01 00:00:00 UTC 1970                                          |       |
|Skapad av                  |Spark-2.4.4.2.6.99.201 – 11401300                                       |       |
|Typ                        |5,25                                                              |       |
|Leverantör                    |DELTA                                                                 |       |
|Tabell egenskaper            |[transient_lastDdlTime = 1587774938]                                    |       |
|Location                    |abfss://data @ <data lake> . DFS.Core.Windows.net/delta/delta-Table-587152|       |
|Serde-bibliotek               |org. apache. Hadoop. Hive. serde2. Lazy. LazySimpleSerDe                    |       |
|InputFormat                 |org. apache. Hadoop. mapred. SequenceFileInputFormat                      |       |
|OutputFormat                |org. apache. Hadoop. Hive. QL. io. HiveSequenceFileOutputFormat             |       |
|Lagrings egenskaper          |[serialisering. format = 1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Villkorlig uppdatering utan överskrivning

Delta Lake tillhandahåller programmerings-API: er för villkorlig uppdatering, borttagning och sammanslagning (Detta kallas vanligt vis för upsert) data i tabeller.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Resultat i:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Här har du lagt till 100 i alla jämna ID.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Resultat i:

| ID|
|---|
|  5|
|  7|
|  9|

Observera att alla jämna rader har tagits bort.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Resultat i:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Här har du en kombination av befintliga data. Befintliga data har tilldelats värdet-1 i Update (WhenMatched) kod Sök väg. De nya data som skapades överst i kodfragmentet och har lagts till via INSERT Code Path (WhenNotMatched) har också lagts till.

### <a name="history"></a>Historik

Delta Lake kan göra det möjligt att titta på historiken för en tabell. Det vill säga de ändringar som gjorts i den underliggande delta tabellen. I cellen nedan visas hur enkelt det är att granska historiken.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Resultat i:

|version|          timestamp|userId|userName|operation|                                                operationParameters| jobb|notebook-fil|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    Katalog|                       [predikat-> (oldData.`ID` = newData. `ID` )]|null|    null|     null|          3|          null|        falskt|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[predikat-> ["(( `ID` % Cast (2 as bigint)) = Cast (0 som bigint))]]|null|    null|     null|          2|          null|        falskt|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predikat-> ((ID # 744L% Cast (2 as bigint)) = Cast (0 som bigint))]|null|    null|     null|          1|          null|        falskt|
|      1|2020-04-25 00:35:05|  null|    null|    Skriv|                             [mode-> överskrivning, Partitionby kolumndefinitionerna-> []]|null|    null|     null|          0|          null|        falskt|
|      0|2020-04-25 00:34:34|  null|    null|    Skriv|                         [mode-> ErrorIfExists, Partitionby kolumndefinitionerna-> []]|null|    null|     null|       null|          null|         true|

Här kan du se alla ändringar som gjorts över ovanstående kodfragment.

## <a name="read-older-versions-of-data-using-time-travel"></a>Läs äldre versioner av data med hjälp av res tid

Det är möjligt att fråga tidigare ögonblicks bilder av din delta Lake-tabell med hjälp av en funktion som heter Time Travel. Om du vill komma åt de data som du har skrivit över, kan du fråga en ögonblicks bild av tabellen innan du skrev över den första uppsättningen med data med hjälp av alternativet versionAsOf.

När du har kört cellen nedan bör du se den första uppsättningen data från innan du skrev över den. Res tiden är en mycket kraftfull funktion som drar nytta av kraften i delta Lake Transaction-loggen för att få åtkomst till data som inte längre finns i tabellen. Om du tar bort version 0-alternativet (eller anger version 1) kan du se nya data igen. Mer information finns i [fråga en äldre ögonblicks bild av en tabell](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Resultat i:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Här kan du se att du har gått tillbaka till den tidigaste versionen av data.

## <a name="write-a-stream-of-data-to-a-table"></a>Skriva en data ström till en tabell

Du kan också skriva till en delta Lake-tabell med Spark: s strukturerade strömning. Delta Lake Transaction-loggen garanterar exakt en gång efter bearbetning, även om det finns andra strömmar eller batch-frågor som körs samtidigt mot tabellen. Som standard körs strömmar i läget append, som lägger till nya poster i tabellen.

Mer information om delta Lake-integrering med strukturerad strömning finns i [tabell strömning läsningar och skrivningar](https://docs.delta.io/latest/delta-streaming.html).

I cellerna nedan finns följande:

* Cell 30 visar nyligen tillagda data
* Cell 31 inspektions historik
* Cell 32 stoppa det strukturerade strömnings jobbet
* Cell 33 inspektions historik < – du kommer att märka att tillägg har stoppats

Först ska du konfigurera ett enkelt Spark streaming-jobb för att generera en sekvens och göra jobbet skrivning till din delta tabell.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Läs en ström med ändringar från en tabell

Även om strömmen skrivs till delta Lake-tabellen kan du också läsa från tabellen som en strömmande källa. Du kan till exempel starta en annan strömnings fråga som skriver ut alla ändringar som gjorts i delta Lake-tabellen.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Resultat i:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Resultat i:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STRÖMMANDE UPPDATERING|[outputMode-> append, fråge-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           Katalog|                                         [predikat-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikat-> ["(( `id` % Cast (2 as bigint)) = Cast (0 som bigint))]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikat-> ((ID # 744L% Cast (2 as bigint)) = Cast (0 som bigint))]|          1|
|      1|2020-04-25 00:35:05|           Skriv|                                               [mode-> överskrivning, Partitionby kolumndefinitionerna-> []]|          0|
|      0|2020-04-25 00:34:34|           Skriv|                                           [mode-> ErrorIfExists, Partitionby kolumndefinitionerna-> []]|       null|

Här tar du bort några av de mindre intressanta kolumnerna för att förenkla visnings upplevelsen av historik visningen.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Resultat i:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STRÖMMANDE UPPDATERING|[outputMode-> append, fråge-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           Katalog|                                         [predikat-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikat-> ["(( `id` % Cast (2 as bigint)) = Cast (0 som bigint))]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikat-> ((ID # 744L% Cast (2 as bigint)) = Cast (0 som bigint))]|          1|
|      1|2020-04-25 00:35:05|           Skriv|                                               [mode-> överskrivning, Partitionby kolumndefinitionerna-> []]|          0|
|      0|2020-04-25 00:34:34|           Skriv|                                           [mode-> ErrorIfExists, Partitionby kolumndefinitionerna-> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Konvertera Parquet till delta

Du kan göra en konvertering på plats från Parquet-formatet till delta.

Här testar du om den befintliga tabellen är i delta-format eller inte.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultat i:

Falskt

Nu ska du konvertera data till delta-format och kontrol lera att de fungerade.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultat i:

Sant

## <a name="sql-support"></a>SQL-stöd

Delta stöder tabell verktygs kommandon via SQL. Du kan använda SQL för att:

* Hämta en DeltaTable historik
* Vakuum a DeltaTable
* Konvertera en Parquet-fil till delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Resultat i:

|version|          timestamp|userId|userName|       operation| operationParameters| jobb|notebook-fil|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|STRÖMMANDE UPPDATERING|[outputMode->-AP...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           Katalog|[predikat-> (OL...|null|    null|     null|          3|          null|        falskt|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[predikat-> ["(...|null|    null|     null|          2|          null|        falskt|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predikat-> ((i...|null|    null|     null|          1|          null|        falskt|
|      1|2020-04-25 00:35:05|  null|    null|           Skriv|[mode-> Overwrit...|null|    null|     null|          0|          null|        falskt|
|      0|2020-04-25 00:34:34|  null|    null|           Skriv|[mode-> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Resultat i:

|                path|
|--------------------|
|abfss://data@arca...|

Nu ska du kontrol lera att en tabell inte är en delta format tabell. konvertera den sedan till delta format med Spark SQL och bekräfta att den har konverterats korrekt.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultat i:

Sant

Fullständig dokumentation finns på sidan om [delta Lake-dokumentation](https://docs.delta.io/latest/delta-intro.html)

Mer information finns i [delta Lake Project](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om .NET för Apache Spark](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)