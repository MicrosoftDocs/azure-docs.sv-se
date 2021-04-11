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
ms.date: 03/24/2021
ms.openlocfilehash: 5d08bc216157fce9ad81eaf3c0f540c7a4d8c3f2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259837"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

I den här självstudien får du lära dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure-Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **nytt** 
1. Ange **Spark1** som **namn på Apache Spark pool** .
1. För **Node-storlek** anger du **liten**.
1. För **antal noder** ställer du in minst 3 och maximalt 3
1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

## <a name="understanding-serverless-apache-spark-pools"></a>Förstå Server lös Apache Spark pooler

En server lös Spark-pool är ett sätt att ange hur en användare vill arbeta med Spark. När du börjar använda en pool skapas en spark-session vid behov. Poolen styr hur många Spark-resurser som ska användas av sessionen och hur länge sessionen ska pausas innan den pausas automatiskt. Du betalar för Spark-resurser som används under den sessionen, inte för själva poolen. På så sätt kan du arbeta med Spark i en spark-pool utan att behöva oroa dig för att hantera kluster. Detta liknar hur en server lös SQL-pool fungerar.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analysera NYC taxi-data i Blob Storage med Spark

1. Gå till **utveckla** hubben i Synapse Studio
2. Skapa en ny antecknings bok med standard språket inställt på **PySpark (python)**.
3. Skapa en ny Code-cell och klistra in följande kod i cellen.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. I antecknings boken, på menyn **Anslut till** , väljer du den **Spark1** -server som vi skapade tidigare.
1. Välj **Kör** i cellen. Synapse kommer att starta en ny Spark-session för att köra den här cellen om det behövs. Om en ny Spark-session behövs tar det en första sekund att skapa två sekunder. 
1. Om du bara vill se schemat för dataframe kör du en cell med följande kod:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Data är tillgängliga via dataframe med namnet **data**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Lägg till en ny kod cell i antecknings boken och ange sedan följande kod:

    ```py
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande kod. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör cellen för att visa de NYC taxi-data som vi läste in i **nyctaxi** Spark-databasen.
1. Skapa en ny kod cell och ange följande kod. Vi kommer att analysera dessa data och spara resultaten i en tabell med namnet **nyctaxi. passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistance) as SumTripDistance,
          AVG(TripDistance) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistance > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. I cell resultaten väljer du **diagram** för att visa data som visualiseras.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med dedikerad SQL-pool](get-started-analyze-sql-pool.md)
