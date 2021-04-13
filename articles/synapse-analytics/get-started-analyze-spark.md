---
title: 'Snabbstart: Kom igång med att analysera med Spark'
description: I den här självstudien lär du dig att analysera data med Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363860"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

I den här självstudien lär du dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en serverlös Apache Spark pool

1. I Synapse Studio rutan till vänster väljer du Hantera Apache Spark  >  **pooler.**
1. Välj **Ny** 
1. För **Apache Spark anger du** **Spark1**.
1. För **Nodstorlek** anger du **Liten**.
1. För **Antal noder anger** du minst 3 och det högsta till 3
1. Välj **Granska + skapa** > **Skapa**. Din Apache Spark är klar om några sekunder.

## <a name="understanding-serverless-apache-spark-pools"></a>Förstå serverlösa Apache Spark pooler

En serverlös Spark-pool är ett sätt att ange hur en användare vill arbeta med Spark. När du börjar använda en pool skapas en Spark-session om det behövs. Poolen styr hur många Spark-resurser som ska användas av den sessionen och hur länge sessionen varar innan den pausas automatiskt. Du betalar för Spark-resurser som används under den sessionen, inte för själva poolen. På så sätt kan du med en Spark-pool arbeta med Spark utan att behöva oroa dig för att hantera kluster. Detta liknar hur en serverlös SQL-pool fungerar.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Analysera nyc-taxidata med en Spark-pool

1. I Synapse Studio du till **hubben Utveckla**
2. Skapa en ny notebook
3. Skapa en ny kodcell och klistra in följande kod i cellen.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. I anteckningsboken går du till **menyn Anslut till** och väljer den **Spark1-serverlösa** Spark-pool som vi skapade tidigare.
1. Välj **Kör** i cellen. Synapse startar en ny Spark-session för att köra den här cellen om det behövs. Om en ny Spark-session krävs tar det inledningsvis cirka två sekunder att skapa. 
1. Om du bara vill se schemat för dataramen kör du en cell med följande kod:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läsa in nyc taxi-data i Spark-databasen

Data är tillgängliga via dataramen med namnet **df**. Läs in den i en Spark-databas med **namnet nycgett**.

1. Lägg till en ny kodcell i anteckningsboken och ange sedan följande kod:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera nyc-taxidata med Spark och notebook-datorer

1. Skapa en ny kodcell och ange följande kod. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör cellen för att visa nyc taxi-data som vi läst in i **nyc** spark-databasen.
1. Skapa en ny kodcell och ange följande kod. Vi ska analysera dessa data och spara resultaten i en tabell med namnet **nychuvud.passengercountstats**.

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

1. I cellresultaten väljer du **Diagram för** att se data visualiserade.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med dedikerad SQL-pool](get-started-analyze-sql-pool.md)
