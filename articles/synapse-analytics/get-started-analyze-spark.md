---
title: 'Snabb start: komma igång med att analysera med Spark'
description: I den här självstudien får du lära dig att analysera data med Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118888"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

I den här självstudien får du lära dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure-Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analysera NYC taxi-data i Blob Storage med Spark

1. I **data** hubben väljer du **Lägg till en ny resurs** (plus knapp ovanför **länkad**) > **Bläddra i galleriet**.
1. Välj **NYC taxi & limousine provision-Yellow taxi rese poster**.
1. Längst ned på sidan väljer du **Fortsätt**  >  **Lägg till data uppsättning**.
1. Högerklicka på **Azure Blob Storage** i **data** hubben under **länkad** och välj sedan **exempel data uppsättningar**  >  **nyc_tlc_yellow**.
1. Välj **ny Notebook** för att skapa en ny antecknings bok med följande kod:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. I menyn **bifoga till** i antecknings boken väljer du en server lös Spark-pool.
1. Välj **Kör** i cellen.
1. Om du bara vill se schemat för dataframe kör du en cell med följande kod:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Data är tillgängliga i en tabell i **SQLPOOL1**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Gå till **utveckla** hubben i Synapse Studio.
1. Välj **+**  >  **antecknings bok**.
1. Ange värdet **koppla till** värde till **Spark1** överst i antecknings boken.
1. Välj **Lägg till kod** för att lägga till en kod cell i antecknings boken och ange sedan följande text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Välj **Kör** i cellen.
1. I **data** hubben högerklickar du på **databaser** och väljer sedan **Uppdatera**. Du bör se dessa databaser:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande text.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör cellen för att Visa NYC taxi-data som du läste in i **nyctaxi** Spark-databasen.
1. Kör följande kod för att utföra samma analys som du gjorde tidigare med den dedikerade SQL-poolen **SQLPOOL1**. Den här koden sparar och visar resultatet av analysen i en tabell med namnet **nyctaxi. passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. I cell resultaten väljer du **diagram** för att visa data som visualiseras.

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Läsa in data från en spark-tabell till en dedikerad SQL-adresspool

Tidigare kopierade data från den dedikerade SQL-adresspoolen **SQLPOOL1. dbo. resan** till Spark-tabellen **nyctaxi. resan**. Sedan aggregerade du data till Spark-tabellen **nyctaxi. passengercountstats**. Nu ska du kopiera data från **nyctaxi. passengercountstats** till en särskild SQL-pool med namnet **SQLPOOL1. dbo. passengercountstats**.

Kör följande cell i antecknings boken. Den sammanställda Spark-tabellen kopieras till den dedikerade tabellen för SQL-poolen.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med Server lös SQL-pool](get-started-analyze-sql-on-demand.md)
