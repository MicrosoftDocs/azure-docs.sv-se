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
ms.date: 12/31/2020
ms.openlocfilehash: 7410fcbc890780281763a91f33525562e9de1853
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182493"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

I den här självstudien får du lära dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure-Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analysera NYC taxi-data i Blob Storage med Spark

1. I **data** hubben klickar du på **+** knappen för att **lägga till en ny resurs** och klickar sedan på >> **Bläddra i galleriet**. 
1. Hitta **NYC taxi & limousine provision-Yellow taxi resor** och klicka på den. 
1. Klicka på **Fortsätt** längst ned på sidan och Lägg sedan **till data uppsättning**. 
1. Högerklicka på **Azure Blob Storage >> exempel data uppsättningar >> nyc_tlc_yellow** och välj **ny antecknings bok**, i **data** hubb under **länkad**, och Läs sedan **in till data ram**.
1. Då skapas en ny antecknings bok med följande kod:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. I antecknings boken, på menyn **Anslut till** , väljer du den **Spark1** -server som vi skapade tidigare.
1. Välj **Kör** i cellen
1. Om du bara vill se schemat för dataframe kör du en cell med följande kod:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Data är tillgängliga i en tabell i **SQLPOOL1**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Gå till **utveckla** hubben i Synapse Studio.
1. Välj **+**  >  **antecknings bok**.
1. Ange värdet **koppla till** värde till **Spark1** överst i antecknings boken.
1. I den nya antecknings bokens första kod cell och ange sedan följande kod:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Kör skriptet. Det kan ta 2-3 minuter.
1. I **data** hubben på fliken **arbets yta** högerklickar du på **databaser** och väljer sedan **Uppdatera**. Nu bör du se databasen **nyctaxi (Spark)** i listan.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande kod. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör cellen för att visa de NYC taxi-data som vi läste in i **nyctaxi** Spark-databasen.
1. Skapa en ny kod cell och ange följande kod. Kör sedan cellen för att utföra samma analys som vi gjorde tidigare med den dedikerade SQL-poolen **SQLPOOL1**. Den här koden sparar och visar resultatet av analysen i en tabell med namnet **nyctaxi. passengercountstats**.


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

1. Skapa en ny kod cell och ange följande kod. Kör cellen i din bärbara dator. Den sammanställda Spark-tabellen kopieras till den dedikerade tabellen för SQL-poolen.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med Server lös SQL-pool](get-started-analyze-sql-on-demand.md)
