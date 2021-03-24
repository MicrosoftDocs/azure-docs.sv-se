---
title: Kopiera Synapse-länk för Azure Cosmos DB data till en dedikerad SQL-pool med Apache Spark
description: Läs in data i en spark-dataframe, granska data och Läs in dem i en dedikerad SQL-adresspool
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 755782b117ef5a5619ddde3e1573fe1805117e77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868570"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Kopiera data från Azure Cosmos DB till en dedikerad SQL-pool med Apache Spark

Azure Synapse-länken för Azure Cosmos DB gör det möjligt för användare att köra nära real tids analys över drifts data i Azure Cosmos DB. Det finns dock tillfällen då vissa data behöver aggregeras och berikas för att hantera informations lager användare. Att granska och exportera Synapse länk data kan göras med bara några få celler i en bärbar dator.

## <a name="prerequisites"></a>Förutsättningar
* [Etablera en Synapse-arbetsyta](../quickstart-create-workspace.md) med:
    * [Server lös Apache Spark pool](../quickstart-create-apache-spark-pool-studio.md)
    * [dedikerad SQL-pool](../quickstart-create-sql-pool-studio.md)
* [Etablera ett Cosmos DB-konto med en HTAP-behållare med data](../../cosmos-db/configure-synapse-link.md)
* [Anslut Azure Cosmos DB HTAP-behållaren till arbets ytan](./how-to-connect-synapse-link-cosmos-db.md)
* [Har rätt installation för att importera data till en dedikerad SQL-pool från Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Steg
I den här självstudien ansluter du till analys lagret så att det inte påverkar transaktions lagringen (den kommer inte att förbruka några enheter för programbegäran). Vi går igenom följande steg:
1. Läs Cosmos DB HTAP-behållaren i en spark-dataframe
2. Sammanställ resultatet i en ny dataframe
3. Mata in data i en dedikerad SQL-pool

[![Spark till SQL steg 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Data
I det exemplet använder vi en HTAP-behållare som heter **RetailSales**. Den är en del av en länkad tjänst med namnet **ConnectedData** och har följande schema:
* _rid: sträng (Nullable = true)
* _ts: långt (Nullable = true)
* logQuantity: Double (Nullable = true)
* productCode: sträng (Nullable = true)
* kvantitet: lång (Nullable = true)
* pris: långt (Nullable = true)
* ID: sträng (Nullable = true)
* annonsering: lång (Nullable = sant)
* storeId: långt (Nullable = true)
* weekStarting: långt (Nullable = true)
* _etag: sträng (Nullable = true)

Vi sammanställer försäljningen (*kvantitet*, *intäkt* (pris × kvantitet) av *ProductCode* och *weekStarting* för rapportering. Slutligen ska vi exportera dessa data till en dedikerad SQL-pool med namnet **dbo. productsales**.

## <a name="configure-a-spark-notebook"></a>Konfigurera en spark-anteckningsbok
Skapa en spark-anteckningsbok med Scala som Spark (Scala) som huvud språk. Vi använder antecknings bokens standardinställning för sessionen.

## <a name="read-the-data-in-spark"></a>Läsa data i Spark
Läs Cosmos DB HTAP-behållaren med Spark i en dataframe i den första cellen.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Sammanställ resultatet i en ny dataframe

I den andra cellen kör vi den omvandling och de agg regeringar som krävs för den nya dataframe innan du läser in den i en dedikerad SQL-adresspool.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>Läs in resultaten i en dedikerad SQL-pool

I den tredje cellen läser vi in data i en dedikerad SQL-pool. Det skapar automatiskt en temporär extern tabell, extern data källa och ett externt fil format som tas bort när jobbet är färdigt.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Fråga resultatet med SQL

Du kan fråga resultatet med hjälp av en enkel SQL-fråga, till exempel följande SQL-skript:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Frågan visar följande resultat i ett diagram läge: [ ![ Spark till SQL steg 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Nästa steg
* [Fråga Azure Cosmos DB analys lager med Apache Spark](./how-to-query-analytical-store-spark.md)